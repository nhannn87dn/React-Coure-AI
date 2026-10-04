# ⭐ Bài 11: Data Fetching Nâng Cao Với TanStack Query

> 🎯 Mục tiêu: Học viên hiểu vì sao cần 1 Server State Library, cấu hình được `QueryClientProvider`, làm chủ `useQuery` để fetch dữ liệu có cache tự động, dùng `useMutation` để thêm/sửa/xoá dữ liệu, và biết cách tự động cập nhật lại Cache sau khi Mutation thành công.

---

## Phần 1: Vấn Đề Của Cách Fetch Thủ Công — Vì Sao Cần Server State Library

### 1.1 Những gì phải tự viết lặp lại nhiều lần

Nhìn lại `ProductList` đã viết ở Bài 10 (Phần 4.3), để gọi đúng 1 API, ta đã phải tự tay viết:

- 3 State riêng: `data`, `loading`, `error`.
- Logic `try/catch/finally` bên trong `useEffect`.
- Xử lý Race Condition thủ công bằng biến cờ hoặc `AbortController` (Bài 10, Phần 5.3).
- Không có sẵn cơ chế **cache** — mỗi lần Component mount lại, dữ liệu bị gọi lại từ đầu dù vừa fetch xong trước đó vài giây.
- Không có sẵn cơ chế **retry** khi request thất bại do lỗi mạng tạm thời.

Nếu dự án có hàng chục Component cần gọi API, toàn bộ đoạn code lặp đi lặp lại này phải viết lại ở từng nơi — đây chính là lý do cộng đồng React xây dựng các thư viện chuyên trách việc này, phổ biến nhất là **TanStack Query**.

### 1.2 Server State khác Client State như thế nào

> **Client State là dữ liệu hoàn toàn thuộc sở hữu của trình duyệt (ví dụ trạng thái mở/đóng 1 Modal — `useState` ở Bài 4 quản lý rất tốt). Server State là dữ liệu thực chất "sống" ở server, Client chỉ đang giữ 1 bản sao có thể lỗi thời bất cứ lúc nào.**

Server State có những đặc điểm riêng mà `useState`/`useEffect` thuần không xử lý sẵn: dữ liệu có thể **đã bị người khác thay đổi** trên server ngay cả khi bạn không làm gì (cần cơ chế tự làm mới), việc lấy dữ liệu luôn **bất đồng bộ** và có thể lỗi, và cùng 1 dữ liệu có thể được **nhiều Component khác nhau** cùng cần dùng (nên rất nên có cache dùng chung thay vì mỗi Component tự fetch riêng).

---

## Phần 2: Cài Đặt TanStack Query và Cấu Hình `QueryClientProvider`

### 2.1 Cài đặt `@tanstack/react-query`

```bash
pnpm add @tanstack/react-query
```

### 2.2 Khởi tạo `QueryClient` và bọc ứng dụng bằng `QueryClientProvider`

```tsx
// main.tsx
import { createRoot } from 'react-dom/client';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { RouterProvider } from 'react-router-dom';
import router from './router';

const queryClient = new QueryClient();

createRoot(document.getElementById('root')!).render(
  <QueryClientProvider client={queryClient}>
    <RouterProvider router={router} />
  </QueryClientProvider>
);
```

`QueryClient` chính là nơi lưu trữ **toàn bộ Cache** của ứng dụng — tương tự vai trò của 1 Store trong Zustand (Bài 9), nhưng chuyên biệt cho Server State. `QueryClientProvider` (dùng Context API bên dưới, đã học ở Bài 9, Phần 2) giúp mọi Component trong cây đều truy cập được `queryClient` này mà không cần Prop Drilling.

### 2.3 Cài đặt React Query Devtools

```bash
pnpm add -D @tanstack/react-query-devtools
```

```tsx
// main.tsx
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';

createRoot(document.getElementById('root')!).render(
  <QueryClientProvider client={queryClient}>
    <RouterProvider router={router} />
    <ReactQueryDevtools initialIsOpen={false} />
  </QueryClientProvider>
);
```

Devtools hiển thị 1 bảng điều khiển trực quan (chỉ xuất hiện trong môi trường dev) để xem trực tiếp trạng thái Cache của từng Query — dữ liệu đang `fresh`/`stale`, đang `fetching`, hay đã hết hạn — cực kỳ hữu ích khi debug hành vi cache trong Phần 3.

---

## Phần 3: `useQuery` — Fetch Dữ Liệu Với Cache Tự Động và Query Key

### 3.0 Định nghĩa

> **`useQuery` là Hook dùng để lấy dữ liệu (GET) từ server, tự động quản lý cache, loading, error, và tự làm mới dữ liệu theo các sự kiện được cấu hình sẵn — thay thế hoàn toàn cho tổ hợp `useState` + `useEffect` thủ công ở Bài 10.**

### 3.1 Cú pháp cơ bản `useQuery({ queryKey, queryFn })`

```tsx
import { useQuery } from '@tanstack/react-query';
import axiosInstance from '../lib/axiosInstance';

interface Product {
  id: number;
  name: string;
  price: number;
}

async function fetchProducts(): Promise<Product[]> {
  const response = await axiosInstance.get<Product[]>('/products');
  return response.data;
}

function ProductList() {
  const { data, isLoading, isError } = useQuery({
    queryKey: ['products'],
    queryFn: fetchProducts,
  });

  if (isLoading) return <p>Đang tải dữ liệu...</p>;
  if (isError) return <p>Đã có lỗi xảy ra.</p>;

  return (
    <ul>
      {data?.map((p) => (
        <li key={p.id}>{p.name}</li>
      ))}
    </ul>
  );
}

export default ProductList;
```

So với Bài 10 (Phần 4.3), toàn bộ logic `try/catch/finally` và 3 `useState` thủ công đã được thay bằng đúng **1 lời gọi Hook** — `useQuery` tự lo phần còn lại.

### 3.2 Query Key là gì

> **Query Key là 1 mảng (thường là chuỗi hoặc kết hợp chuỗi + giá trị động) dùng để định danh và lưu trữ dữ liệu trong Cache — 2 lời gọi `useQuery` cùng Query Key sẽ dùng chung 1 bản Cache.**

```tsx
// Query Key có tham số động - mỗi productId sẽ có 1 "ngăn Cache" riêng
function useProductDetail(productId: number) {
  return useQuery({
    queryKey: ['products', productId], // ['products', 1], ['products', 2]... là các key khác nhau
    queryFn: () => fetchProductById(productId),
  });
}
```

Nhờ Query Key, nếu 2 Component khác nhau (ví dụ `ProductList` và `CartBadge`) cùng gọi `useQuery({ queryKey: ['products'], ... })`, TanStack Query chỉ gọi API **1 lần duy nhất** và chia sẻ chung kết quả cho cả 2 Component — không còn hiện tượng gọi API trùng lặp thường gặp khi tự viết `useEffect` ở nhiều nơi.

### 3.3 Nhận về sẵn các trạng thái mà không cần tự khai báo State

`useQuery` trả về 1 object với rất nhiều trạng thái hữu ích đã tính toán sẵn:

```tsx
const { data, isLoading, isError, error, isFetching, refetch } = useQuery({
  queryKey: ['products'],
  queryFn: fetchProducts,
});
```

- `data`: dữ liệu trả về (có thể `undefined` nếu chưa fetch xong).
- `isLoading`: `true` chỉ trong lần fetch **đầu tiên** (chưa có Cache).
- `isFetching`: `true` mỗi khi đang fetch (kể cả khi refetch ngầm — Phần 5.2 — dù đã có `data` cũ để hiển thị tạm).
- `isError`/`error`: thông tin lỗi nếu request thất bại.
- `refetch`: hàm gọi lại API thủ công khi cần.

---

## Phần 4: `useMutation` — Xử Lý POST/PUT/DELETE Dữ Liệu

### 4.0 Định nghĩa

> **`useMutation` là Hook dùng cho các thao tác làm THAY ĐỔI dữ liệu trên server (POST/PUT/DELETE), khác với `useQuery` (chỉ dùng để ĐỌC dữ liệu).**

### 4.1 Cú pháp cơ bản và gọi bằng hàm `mutate()`

```tsx
import { useMutation } from '@tanstack/react-query';

interface NewProduct {
  name: string;
  price: number;
}

async function createProduct(newProduct: NewProduct): Promise<Product> {
  const response = await axiosInstance.post<Product>('/products', newProduct);
  return response.data;
}

function AddProductForm() {
  const mutation = useMutation({
    mutationFn: createProduct,
  });

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault(); // ôn lại Bài 4, Phần 6.2
    mutation.mutate({ name: 'Áo mới', price: 200000 });
  };

  return <form onSubmit={handleSubmit}>...</form>;
}
```

Khác với `useQuery` (tự động chạy ngay khi Component mount), `useMutation` **không tự chạy** — chỉ thực thi khi bạn chủ động gọi `mutation.mutate(...)`, đúng với bản chất của nó: chỉ nên xảy ra khi có hành động cụ thể từ người dùng (submit form, bấm nút xoá...).

### 4.2 Xử lý callback `onSuccess`, `onError`, `onSettled`

```tsx
const mutation = useMutation({
  mutationFn: createProduct,
  onSuccess: (data) => {
    console.log('Tạo sản phẩm thành công:', data);
  },
  onError: (error) => {
    console.log('Tạo sản phẩm thất bại:', error);
  },
  onSettled: () => {
    console.log('Mutation đã hoàn tất (dù thành công hay thất bại)');
  },
});
```

3 callback này tương ứng đúng với 3 nhánh `try/catch/finally` đã viết thủ công ở Bài 10 (Phần 4.3) — nhưng ở đây chỉ cần khai báo, không cần tự quản lý State loading/error nữa.

### 4.3 Hiển thị trạng thái `isPending` khi đang xử lý Mutation

```tsx
function AddProductForm() {
  const mutation = useMutation({ mutationFn: createProduct });

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    mutation.mutate({ name: 'Áo mới', price: 200000 });
  };

  return (
    <form onSubmit={handleSubmit}>
      <button type="submit" disabled={mutation.isPending}>
        {mutation.isPending ? 'Đang thêm...' : 'Thêm sản phẩm'}
      </button>
      {mutation.isError && <p style={{ color: 'red' }}>Đã có lỗi xảy ra</p>}
    </form>
  );
}
```

`disabled={mutation.isPending}` là kỹ thuật quan trọng trong thực tế — ngăn người dùng bấm submit nhiều lần liên tiếp trong lúc request trước đó còn đang xử lý.

---

## Phần 5: Invalidate Query — Tự Động Cập Nhật Dữ Liệu Sau Khi Mutation

### 5.1 Vấn đề: dữ liệu cũ trong Cache không tự cập nhật

Sau khi `AddProductForm` (Phần 4) thêm thành công 1 sản phẩm mới, `ProductList` (Phần 3) — đang hiển thị `data` từ Cache `['products']` — **sẽ không tự biết** rằng vừa có 1 sản phẩm mới được thêm vào server, vì TanStack Query không có cách nào tự đoán được Mutation vừa rồi ảnh hưởng đến Query Key nào, trừ khi được chỉ định rõ.

### 5.2 Dùng `queryClient.invalidateQueries()`

```tsx
import { useMutation, useQueryClient } from '@tanstack/react-query';

function AddProductForm() {
  const queryClient = useQueryClient(); // lấy queryClient đã cấu hình ở Phần 2.2

  const mutation = useMutation({
    mutationFn: createProduct,
    onSuccess: () => {
      // Báo cho Cache biết dữ liệu ứng với key ['products'] đã "cũ", cần fetch lại
      queryClient.invalidateQueries({ queryKey: ['products'] });
    },
  });

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    mutation.mutate({ name: 'Áo mới', price: 200000 });
  };

  return <form onSubmit={handleSubmit}>...</form>;
}
```

`invalidateQueries({ queryKey: ['products'] })` đánh dấu mọi Query đang dùng Query Key `['products']` là **"stale" (đã cũ)**, khiến TanStack Query tự động gọi lại `queryFn` tương ứng ở **bất kỳ Component nào** đang hiển thị dữ liệu đó (kể cả `ProductList` không hề liên quan trực tiếp đến `AddProductForm`) — giải quyết đúng vấn đề ở Phần 5.1 mà không cần Component nào tự gọi `refetch()` thủ công.

### 5.3 Ứng dụng thực tế: Todo list tự cập nhật sau khi thêm mới

```tsx
interface Todo {
  id: number;
  text: string;
  completed: boolean;
}

function TodoApp() {
  const queryClient = useQueryClient();

  const { data: todos, isLoading } = useQuery({
    queryKey: ['todos'],
    queryFn: async () => {
      const res = await axiosInstance.get<Todo[]>('/todos');
      return res.data;
    },
  });

  const addTodoMutation = useMutation({
    mutationFn: (text: string) => axiosInstance.post<Todo>('/todos', { text, completed: false }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['todos'] }); // tự động refetch danh sách Todo
    },
  });

  if (isLoading) return <p>Đang tải...</p>;

  return (
    <div>
      <button onClick={() => addTodoMutation.mutate('Việc mới')} disabled={addTodoMutation.isPending}>
        Thêm Todo
      </button>
      <ul>
        {todos?.map((todo) => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </div>
  );
}
```

Ngay sau khi `addTodoMutation.mutate(...)` thành công, danh sách hiển thị **tự động cập nhật** với Todo mới — không cần tự gọi lại `fetchTodos()` hay quản lý thêm bất kỳ State thủ công nào như đã phải làm ở Bài 10.

---

## Phần 6: So Sánh Thực Tế — Trước và Sau Khi Dùng TanStack Query

### 6.1 Đối chiếu độ phức tạp giữa Bài 10 và Bài 11

| Việc cần làm | Fetch thủ công (Bài 10) | TanStack Query (Bài 11) |
|---|---|---|
| Khai báo State loading/error/data | 3 `useState` thủ công | Có sẵn trong `useQuery` |
| Gọi API khi mount | Tự viết `useEffect` + `try/catch/finally` | `queryFn` tự động chạy |
| Cache dữ liệu giữa các Component | Không có, phải tự nghĩ giải pháp | Tự động theo Query Key |
| Chống Race Condition | Tự viết `AbortController`/biến cờ | Xử lý sẵn bên trong thư viện |
| Cập nhật lại UI sau khi POST/PUT/DELETE | Tự gọi lại hàm fetch thủ công | `invalidateQueries()` |

### 6.2 Demo tính năng tự động refetch khi quay lại tab (`refetchOnWindowFocus`)

```tsx
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      refetchOnWindowFocus: true, // mặc định TanStack Query đã bật sẵn tính năng này
    },
  },
});
```

Tính năng này khiến TanStack Query **tự động gọi lại API** mỗi khi người dùng chuyển sang tab trình duyệt khác rồi quay lại — đảm bảo dữ liệu hiển thị luôn mới nhất (ví dụ phát hiện đơn hàng vừa được người khác cập nhật), hoàn toàn không cần bạn tự viết bất kỳ logic nào.

### 6.3 Tổng kết khi nào nên dùng TanStack Query, khi nào fetch thủ công vẫn đủ dùng

- **Fetch thủ công (Bài 10)** vẫn phù hợp cho: dự án rất nhỏ, chỉ gọi API ở 1-2 nơi, không cần cache dùng chung giữa nhiều Component.
- **TanStack Query** nên dùng khi: dự án có nhiều màn hình cùng cần chung 1 nguồn dữ liệu server, cần cache/tự động refetch, hoặc có nhiều thao tác POST/PUT/DELETE cần đồng bộ lại UI sau đó — gần như là lựa chọn mặc định cho mọi dự án React thực tế hiện nay.

---

## 🧪 Bài tập thực hành cuối buổi

1. Viết lại `UserProfile` đã làm ở Bài 10 (gọi `https://jsonplaceholder.typicode.com/users/1`) bằng `useQuery`, so sánh số dòng code với bản dùng `fetch`/`useEffect` thủ công.
2. Xây `ProductList` dùng `useQuery` với Query Key `['products']` (Phần 3), mở React Query Devtools (Phần 2.3) để quan sát trạng thái Cache chuyển từ `fresh` sang `stale` theo thời gian.
3. Thêm `AddProductForm` dùng `useMutation` (Phần 4) để POST sản phẩm mới, áp dụng `invalidateQueries` (Phần 5.2) để `ProductList` tự động cập nhật ngay sau khi thêm thành công — không cần F5 lại trang.
4. Thêm nút "Xoá sản phẩm" cho từng dòng trong `ProductList`, dùng `useMutation` với `mutationFn` gọi DELETE, và cũng `invalidateQueries(['products'])` trong `onSuccess` để danh sách tự cập nhật sau khi xoá.

## 🧪 Bài tập Homework
