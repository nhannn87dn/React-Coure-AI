# ⭐ Bài 11: Data Fetching Nâng Cao Với TanStack Query

> 🎯 Mục tiêu: Học viên giải thích được sự khác nhau giữa Server State và Client State, sử dụng được `useQuery`/`useMutation` để đọc và ghi dữ liệu, và tự động cập nhật dữ liệu sau khi thay đổi bằng Invalidate Query.

---

## Phần 1: Vấn Đề Của Cách Fetch Thủ Công

### 1.0 Nhắc lại nhanh

Ở Bài 10, ta đã tự tay xử lý: 3 State Loading/Error/Success, gọi API trong `useEffect`, `AbortController` chống Race Condition, và đóng gói tất cả thành Custom Hook `useFetch<T>` (homework Bài 10). Bài này bắt đầu từ đúng câu hỏi bạn đã tự đặt ra ở cuối bài tập đó: `useFetch` còn thiếu gì?

### 1.1 Những gì phải tự viết lặp lại nhiều lần

Với cách làm thủ công ở Bài 10, mỗi khi cần gọi 1 API mới, ta đều phải tự tay lo:

- Khai báo `loading`/`error`/`data`, viết `try/catch/finally`.
- Không có **cache**: nếu 2 Component khác nhau cùng gọi `useFetch('/products')`, mỗi Component tự gọi API **riêng biệt**, dù dữ liệu giống hệt nhau — lãng phí request.
- Không tự động **refetch** khi cần (ví dụ: sau khi thêm 1 sản phẩm mới, danh sách cũ trong Cache không tự cập nhật — phải tự nghĩ cách gọi lại).
- Không có **retry** khi request thất bại do mạng chập chờn — muốn có phải tự viết logic thử lại.

### 1.2 Server State khác Client State như thế nào

> **Client State là dữ liệu THUỘC SỞ HỮU HOÀN TOÀN của phía client** (ví dụ: trạng thái mở/đóng 1 Modal, giá trị đang gõ trong 1 ô input — đã dùng `useState`/Zustand cho loại này xuyên suốt khóa học). **Server State là dữ liệu THỰC SỰ THUỘC VỀ SERVER, chỉ được "mượn" tạm về hiển thị ở client** — dữ liệu này có thể đã **lỗi thời** ngay khi vừa tải về (ví dụ: người khác vừa thêm 1 sản phẩm mới trên server, nhưng Cache ở máy bạn chưa biết điều đó).

Chính vì Server State có đặc tính "có thể lỗi thời", nó cần thêm nhiều thứ mà `useState` thuần không có sẵn: cache, tự động refetch, đồng bộ giữa nhiều nơi dùng cùng dữ liệu. **TanStack Query là 1 thư viện chuyên trị riêng cho Server State** — không thay thế `useState`/Zustand (vẫn dùng cho Client State như bình thường), mà bổ sung thêm 1 công cụ chuyên biệt.

---

## Phần 2: Cài Đặt TanStack Query Và Cấu Hình `QueryClientProvider`

### 2.1 Cài đặt

```bash
pnpm add @tanstack/react-query
```

### 2.2 Khởi tạo `QueryClient` và bọc ứng dụng bằng `QueryClientProvider`

```tsx
// main.tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { RouterProvider } from 'react-router'; // Đã có từ Bài 8
import { router } from './router';

const queryClient = new QueryClient();

createRoot(document.getElementById('root')!).render(
  <QueryClientProvider client={queryClient}>
    <RouterProvider router={router} />
  </QueryClientProvider>
);
```

`QueryClient` là nơi TanStack Query lưu trữ toàn bộ Cache của Server State — giống hệt cách `<AuthContext.Provider>` (Bài 9) "phát" dữ liệu xuống toàn bộ cây Component, `QueryClientProvider` cho phép **mọi Component bên trong** dùng `useQuery`/`useMutation` mà không cần truyền `queryClient` qua Props.

### 2.3 Cài đặt React Query Devtools

```bash
pnpm add -D @tanstack/react-query-devtools
```

```tsx
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';

<QueryClientProvider client={queryClient}>
  <RouterProvider router={router} />
  <ReactQueryDevtools initialIsOpen={false} />
</QueryClientProvider>
```

Devtools hiển thị 1 icon nổi ở góc màn hình (chỉ xuất hiện ở môi trường dev) — mở ra sẽ thấy toàn bộ Query đang có trong Cache, trạng thái của từng Query (`fresh`, `stale`, `fetching`...), và dữ liệu thật đang được lưu. Đây là công cụ quan sát trực quan nhất để hiểu Cache hoạt động ra sao — tương tự vai trò của React DevTools (Bài 4) với State/Props.

---

## Phần 3: `useQuery` — Fetch Dữ Liệu Với Cache Tự Động Và Query Key

### 3.1 Cú pháp cơ bản

```tsx
import { useQuery } from '@tanstack/react-query';
import { api } from '../lib/api'; // Axios Instance đã tạo ở Bài 10

type Product = { id: number; title: string; price: number };

function ProductListPage() {
  const { data, isPending, isError } = useQuery({
    queryKey: ['products'],
    queryFn: async () => {
      const response = await api.get<{ products: Product[] }>('/products');
      return response.data.products;
    },
  });

  if (isPending) return <p>Đang tải...</p>;
  if (isError) return <p>Đã có lỗi xảy ra.</p>;

  return (
    <ul>
      {data.map((p) => (
        <li key={p.id}>{p.title}</li>
      ))}
    </ul>
  );
}
```

So với `useFetch` tự viết ở Bài 10: không còn `useState` cho `loading`/`error`/`data`, không còn `useEffect`, không còn tự viết `try/catch` — `useQuery` tự lo toàn bộ, chỉ cần cung cấp **`queryKey`** và **`queryFn`** (kiểu của `data` được **tự động suy ra** từ giá trị `queryFn` trả về — Type Inference, đã học ở Bài 1, không cần khai báo Generic thủ công).

### 3.2 Query Key là gì

> **Query Key là 1 mảng (thường là chuỗi + các giá trị liên quan) dùng làm "định danh" duy nhất cho 1 Query trong Cache — TanStack Query dựa vào Query Key để biết: dữ liệu này đã có trong Cache chưa, và khi nào cần coi nó là "khác" với 1 lần gọi trước.**

```tsx
useQuery({ queryKey: ['products'], queryFn: () => api.get('/products') });
useQuery({ queryKey: ['products', productId], queryFn: () => api.get(`/products/${productId}`) });
useQuery({ queryKey: ['products', { category, page }], queryFn: () => api.get('/products', { params: { category, page } }) });
```

Khi Query Key thay đổi (ví dụ `productId` đổi từ `1` sang `2`), TanStack Query nhận ra đây là **1 Query khác**, tự động gọi lại `queryFn` — cùng bản chất với vai trò dependency array của `useEffect` (Bài 6), nhưng được thiết kế chuyên biệt cho việc định danh + cache dữ liệu.

### 3.3 `isPending`, `isError`, `data` — phân biệt `isPending` và `isLoading` ở v5

TanStack Query v5 cung cấp sẵn các cờ trạng thái, không cần tự khai báo State như Bài 10:

- **`isPending`**: đúng khi Query **chưa có dữ liệu nào trong Cache** và đang tải lần đầu.
- **`isLoading`**: (v5) là **bí danh hẹp hơn** của `isPending`, chỉ đúng khi `isPending` **và** Query đang thực sự `fetching` — dùng `isPending` là lựa chọn an toàn và phổ biến hơn cho trường hợp hiển thị "Đang tải..." lần đầu.
- **`isError`**: đúng khi lần gọi gần nhất thất bại (sau khi đã thử lại theo cấu hình `retry`, mặc định TanStack Query tự retry 3 lần khi lỗi trước khi báo `isError`).

### 3.4 Đóng gói `useQuery` thành Custom Hook

```ts
// hooks/useProducts.ts
export function useProducts() {
  return useQuery({
    queryKey: ['products'],
    queryFn: async () => {
      const response = await api.get<{ products: Product[] }>('/products');
      return response.data.products;
    },
  });
}

export function useProduct(id: number) {
  return useQuery({
    queryKey: ['products', id],
    queryFn: async () => {
      const response = await api.get<Product>(`/products/${id}`);
      return response.data;
    },
  });
}
```

```tsx
function ProductListPage() {
  const { data, isPending, isError } = useProducts(); // Gọn hơn, tái sử dụng được ở nhiều nơi
  // ...
}
```

Đúng tinh thần Custom Hook đã học ở Bài 7: đóng gói lại để Component chỉ cần gọi `useProducts()`/`useProduct(id)`, không cần biết chi tiết `queryKey`/`queryFn` bên trong.

---

## Phần 4: `useMutation` — Xử Lý POST/PUT/DELETE Dữ Liệu

### 4.1 Cú pháp cơ bản và gọi bằng `mutate()`

```tsx
import { useMutation } from '@tanstack/react-query';

function AddProductForm() {
  const mutation = useMutation({
    mutationFn: (newProduct: { title: string; price: number }) =>
      api.post('/products', newProduct),
  });

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault(); // Đã học ở Bài 7
    mutation.mutate({ title: 'Sản phẩm mới', price: 100000 });
  };

  return <form onSubmit={handleSubmit}>{/* ... */}</form>;
}
```

Khác với `useQuery` (tự động gọi ngay khi Component mount), `useMutation` **không tự chạy** — nó chỉ thực thi khi bạn chủ động gọi `mutation.mutate(...)`, đúng bản chất của thao tác ghi dữ liệu (chỉ nên xảy ra do hành động của người dùng, ví dụ submit Form — liên hệ nguyên tắc đã học ở Bài 6, mục 8.2).

### 4.2 Callback `onSuccess`, `onError`, `onSettled`

```tsx
const mutation = useMutation({
  mutationFn: (newProduct: NewProduct) => api.post('/products', newProduct),
  onSuccess: (response) => {
    console.log('Thêm thành công:', response.data);
  },
  onError: (error) => {
    console.error('Thêm thất bại:', error);
  },
  onSettled: () => {
    console.log('Hoàn tất - dù thành công hay thất bại'); // Tương tự "finally" đã học ở Bài 10
  },
});
```

### 4.3 Hiển thị `isPending` khi đang xử lý Mutation

```tsx
function AddProductForm() {
  const mutation = useMutation({ mutationFn: (data: NewProduct) => api.post('/products', data) });

  return (
    <form onSubmit={(e) => { e.preventDefault(); mutation.mutate({ title: 'Áo mới', price: 100000 }); }}>
      <button type="submit" disabled={mutation.isPending}>
        {mutation.isPending ? 'Đang thêm...' : 'Thêm sản phẩm'}
      </button>
    </form>
  );
}
```

`isPending` của `useMutation` (khác với `isPending` của `useQuery` ở mục 3.3, dù cùng tên) đúng trong khoảng thời gian từ lúc `mutate()` được gọi đến khi có kết quả — dùng để disable nút Submit, tránh người dùng bấm nhiều lần liên tiếp.

---

## Phần 5: Invalidate Query — Tự Động Cập Nhật Sau Khi Mutation

### 5.1 Vấn đề: dữ liệu cũ trong Cache không tự cập nhật

Sau khi `AddProductForm` (Phần 4) thêm thành công 1 sản phẩm mới, `ProductListPage` (Phần 3) vẫn hiển thị danh sách **cũ** — vì Cache của Query `['products']` không hề biết vừa có 1 sản phẩm mới được thêm ở nơi khác.

### 5.2 `queryClient.invalidateQueries()` — báo Cache đã "cũ"

```tsx
import { useMutation, useQueryClient } from '@tanstack/react-query';

function AddProductForm() {
  const queryClient = useQueryClient(); // Lấy QueryClient đã khởi tạo ở main.tsx (Phần 2.2)

  const mutation = useMutation({
    mutationFn: (newProduct: NewProduct) => api.post('/products', newProduct),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['products'] }); // Đánh dấu Query này "cũ", cần fetch lại
    },
  });

  // ...
}
```

> **`invalidateQueries({ queryKey: [...] })` không tự sửa dữ liệu trong Cache — nó chỉ đánh dấu Query khớp với `queryKey` đó là "stale" (đã cũ), khiến TanStack Query tự động gọi lại `queryFn` tương ứng ở LẦN TỚI query đó được dùng (thường là ngay lập tức, nếu Component đang hiển thị Query đó vẫn đang mounted).** Đây chính là cách giải quyết triệt để vấn đề ở mục 5.1 — không cần tự viết logic "báo cho Component kia load lại" như khi dùng `useFetch` thủ công.

### 5.3 Ứng dụng thực tế: danh sách Todo tự cập nhật sau khi thêm

```tsx
function useAddTodo() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (text: string) => api.post('/todos', { text, completed: false }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['todos'] });
    },
  });
}
```

`AddProductForm`/`TodoForm` và `ProductListPage`/`TodoList` là **2 Component hoàn toàn độc lập, không có quan hệ cha-con** (tương tự tinh thần Zustand ở Bài 9) — nhưng nhờ cùng dùng chung `queryKey`, khi 1 nơi ghi dữ liệu xong, nơi kia tự động cập nhật mà không cần Lifting State Up (Bài 5) hay Global State thủ công.

---

## Phần 6: So Sánh Thực Tế — Trước Và Sau Khi Dùng TanStack Query

### 6.1 Đối chiếu số dòng code và độ phức tạp

| | Fetch thủ công (Bài 10 — `useFetch<T>`) | TanStack Query |
|---|---|---|
| Khai báo State | Tự viết `loading`/`error`/`data` | Không cần — có sẵn `isPending`/`isError`/`data` |
| Cache giữa các Component | Không có — mỗi nơi tự gọi API riêng | Tự động, theo Query Key |
| Cập nhật sau khi ghi dữ liệu | Tự nghĩ cách gọi lại API thủ công | `invalidateQueries()` — 1 dòng |
| Retry khi lỗi | Tự viết | Có sẵn (mặc định retry 3 lần) |
| Hủy request cũ (Race Condition) | Tự viết `AbortController` (Bài 10) | Tự động xử lý bên trong |

### 6.2 Tự động refetch khi quay lại tab trình duyệt

Mặc định, TanStack Query tự động gọi lại `queryFn` mỗi khi cửa sổ trình duyệt **được focus lại** (`refetchOnWindowFocus`, bật sẵn) — thử chuyển sang tab khác rồi quay lại `ProductListPage`, mở Devtools (Phần 2.3) để quan sát Query tự "fetching" lại mà không cần thao tác gì thêm. Đây là hành vi hợp lý cho Server State (mục 1.2): dữ liệu có thể đã thay đổi trong lúc bạn không nhìn vào tab đó.

### 6.3 Khi nào nên dùng TanStack Query, khi nào fetch thủ công vẫn đủ

**Quy tắc thực tế của khóa học:** với bất kỳ dữ liệu nào đến từ server (danh sách sản phẩm, thông tin user, kết quả tìm kiếm...), **luôn ưu tiên TanStack Query** ngay từ đầu dự án thay vì tự viết `useFetch` — chi phí học/cài đặt ban đầu nhỏ hơn nhiều so với lợi ích về cache, đồng bộ dữ liệu, và giảm code lặp lại. Cách gọi API thủ công (Bài 10) vẫn hữu ích để **hiểu bản chất** những gì đang diễn ra "bên dưới" TanStack Query, và cho các script gọi API đơn giản, dùng 1 lần, không cần cache (ví dụ 1 hàm chạy 1 lần khi build — sẽ gặp khi tìm hiểu Next.js sau khóa học).

---

## Phần 7: Thực Hành — Ứng Dụng CRUD Với json-server

### 7.1 Dựng json-server làm backend giả có lưu dữ liệu thật

```bash
pnpm add -D json-server
```

```json
// db.json
{
  "todos": [
    { "id": 1, "text": "Học TanStack Query", "completed": false }
  ]
}
```

```bash
pnpm dlx json-server --watch db.json --port 3001
```

Khác với DummyJSON (Bài 10, chỉ giả lập phản hồi, không lưu thật), `json-server` chạy **cục bộ trên máy bạn**, đọc/ghi thật vào file `db.json` — phù hợp để thực hành trọn vẹn `useMutation` + `invalidateQueries` với dữ liệu thực sự thay đổi qua các lần gọi.

### 7.2 Hiển thị danh sách bằng `useQuery`, CRUD bằng `useMutation` + `invalidateQueries`

```tsx
type Todo = { id: number; text: string; completed: boolean };

function useTodos() {
  return useQuery({
    queryKey: ['todos'],
    queryFn: async () => (await api.get<Todo[]>('/todos')).data,
  });
}

function useToggleTodo() {
  const queryClient = useQueryClient();
  return useMutation({
    mutationFn: (todo: Todo) => api.patch(`/todos/${todo.id}`, { completed: !todo.completed }),
    onSuccess: () => queryClient.invalidateQueries({ queryKey: ['todos'] }),
  });
}

function useDeleteTodo() {
  const queryClient = useQueryClient();
  return useMutation({
    mutationFn: (id: number) => api.delete(`/todos/${id}`),
    onSuccess: () => queryClient.invalidateQueries({ queryKey: ['todos'] }),
  });
}
```

### 7.3 Disable nút và hiển thị trạng thái đang xử lý

```tsx
function TodoItem({ todo }: { todo: Todo }) {
  const toggleTodo = useToggleTodo();
  const deleteTodo = useDeleteTodo();

  return (
    <li>
      <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>{todo.text}</span>
      <button onClick={() => toggleTodo.mutate(todo)} disabled={toggleTodo.isPending}>
        {todo.completed ? 'Bỏ hoàn thành' : 'Hoàn thành'}
      </button>
      <button onClick={() => deleteTodo.mutate(todo.id)} disabled={deleteTodo.isPending}>
        Xóa
      </button>
    </li>
  );
}
```

Mỗi `TodoItem` tự quản lý Mutation riêng của chính nó (`useToggleTodo`, `useDeleteTodo`) — khi bấm nút ở **1 item bất kỳ**, chỉ nút của item đó bị disable trong lúc xử lý (nhờ `mutation.isPending` là State cục bộ của lần gọi `useMutation` đó), các item khác trong danh sách vẫn thao tác bình thường.

---

## 🧪 Bài tập thực hành cuối buổi

1. Dựng `json-server` với 1 resource `products` (tối thiểu 5 sản phẩm mẫu trong `db.json`). Viết `useProducts()` bằng `useQuery`, hiển thị danh sách kèm đầy đủ trạng thái `isPending`/`isError`.
2. Viết `useAddProduct()` bằng `useMutation`, tích hợp `invalidateQueries` để danh sách ở bài tập 1 tự cập nhật ngay sau khi thêm — xác nhận **không cần** F5 lại trang.
3. Mở React Query Devtools (Phần 2.3), quan sát Query `['products']` chuyển trạng thái `fresh` → `stale` → `fetching` ngay sau khi gọi `invalidateQueries` ở bài tập 2.
4. Thử tắt mạng (DevTools → Network → Offline), gọi `useProducts()` và quan sát TanStack Query tự động retry vài lần trước khi chuyển `isError` thành `true` (Phần 3.3).

## 🧪 Bài tập HomeWork

Xem tại file [homeworks.md](homeworks.md)
