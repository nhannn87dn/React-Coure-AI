# ⭐ Bài 10: Data Fetching Cơ Bản (Fetch API & Axios)

> 🎯 Mục tiêu: Học viên gọi được API bằng Fetch và Axios có khai báo kiểu cho dữ liệu trả về, tổ chức được Axios Instance với Interceptor, xử lý đầy đủ trạng thái Loading/Error/Success, và khắc phục được Race Condition.

---

## Phần 0: API Dùng Xuyên Suốt Bài 10-11

Trong 2 bài học tới, ta dùng thống nhất 2 API công khai, miễn phí:

- **[DummyJSON](https://dummyjson.com)** — dùng cho dữ liệu **đọc** (GET): `https://dummyjson.com/products`, `https://dummyjson.com/products/1`, `https://dummyjson.com/products/search?q=...`. DummyJSON **có hỗ trợ** endpoint POST/PUT/DELETE, nhưng chỉ **giả lập phản hồi thành công** — dữ liệu **không thực sự được lưu lại** trên server. Vì vậy ta chỉ dùng nó cho phần đọc dữ liệu ở bài này.
- **json-server** hoặc **MockAPI** — dùng cho các thao tác **CRUD thật** (Create/Update/Delete có lưu lại) khi học `useMutation` ở Bài 11.

---

## Phần 1: Gọi API Với Fetch — Cú Pháp Và Xử Lý Lỗi

### 1.0 Nhắc lại nhanh

Ở Bài 1 ta đã ôn `fetch().then().catch()` và Async/Await; ở Bài 6 ta đã gọi `fetch` bên trong `useEffect` để tải danh sách người dùng. Bài này đào sâu hơn: xử lý lỗi HTTP đúng cách, và tổ chức code gọi API chuyên nghiệp hơn với Axios.

### 1.1 Cú pháp cơ bản với Async/Await

```ts
async function getProducts() {
  const response = await fetch('https://dummyjson.com/products');
  const data = await response.json();
  return data;
}
```

### 1.2 Chuyển response về JSON và gắn kiểu cho kết quả

```ts
type Product = {
  id: number;
  title: string;
  price: number;
};

type ProductsResponse = {
  products: Product[];
  total: number;
};

async function getProducts(): Promise<ProductsResponse> {
  const response = await fetch('https://dummyjson.com/products');
  const data: ProductsResponse = await response.json(); // Khai báo kiểu thủ công
  return data;
}
```

> ⚠️ **`response.json()` luôn trả về kiểu `any`** — TypeScript không có cách nào tự biết cấu trúc dữ liệu thật trả về từ 1 API bên ngoài. Dòng `const data: ProductsResponse = await response.json()` chỉ là bạn **tự khai báo và tin tưởng** cấu trúc đó đúng như mong đợi (đã cảnh báo tương tự ở Bài 1, mục 7.5) — TypeScript không hề kiểm tra dữ liệu thật khớp với `type` hay không. Nếu API đổi cấu trúc trả về, code vẫn biên dịch được nhưng có thể lỗi lúc chạy.

### 1.3 Xử lý lỗi HTTP — `fetch` không tự throw lỗi khi status 404/500

```ts
// ❌ Sai - fetch KHÔNG tự động throw lỗi khi API trả về 404/500
async function getProduct(id: number) {
  const response = await fetch(`https://dummyjson.com/products/${id}`);
  const data = await response.json(); // Vẫn chạy tiếp dù response là lỗi 404!
  return data;
}
```

> ⚠️ **`fetch` chỉ throw lỗi (rơi vào `catch`) khi có sự cố mạng** (mất kết nối, sai domain...). Khi server phản hồi với mã lỗi HTTP (404 Not Found, 500 Internal Server Error...), `fetch` vẫn coi đó là **"request thành công"** về mặt kỹ thuật — nó chỉ đơn giản trả về 1 `response` có `response.ok === false`. Đây là điểm khác biệt quan trọng nhất giữa `fetch` và Axios (Phần 2).

```ts
// ✅ Đúng - luôn kiểm tra response.ok trước khi dùng dữ liệu
async function getProduct(id: number): Promise<Product> {
  const response = await fetch(`https://dummyjson.com/products/${id}`);
  if (!response.ok) {
    throw new Error(`Lỗi ${response.status}: không tải được sản phẩm`);
  }
  const data: Product = await response.json();
  return data;
}
```

---

## Phần 2: Cài Đặt Và Sử Dụng Axios

### 2.1 Cài đặt và cú pháp GET/POST cơ bản

```bash
pnpm add axios
```

```ts
import axios from 'axios';

async function getProducts(): Promise<ProductsResponse> {
  const response = await axios.get<ProductsResponse>('https://dummyjson.com/products');
  return response.data; // Axios tự parse JSON, kết quả nằm trong "response.data"
}
```

`axios.get<T>(url)` — Generic `<T>` (đã học ở Bài 1) khai báo trực tiếp kiểu cho `response.data`, không cần dòng khai báo kiểu riêng như `fetch` (mục 1.2).

### 2.2 So sánh Axios và Fetch

| Tiêu chí | Fetch (có sẵn trong trình duyệt) | Axios (thư viện ngoài) |
|---|---|---|
| Parse JSON | Phải tự gọi `response.json()` (bất đồng bộ) | Tự động, `response.data` đã sẵn là object |
| Lỗi HTTP (404/500) | KHÔNG tự throw — phải tự kiểm tra `response.ok` (mục 1.3) | Tự động throw lỗi (rơi vào `catch`) |
| Cú pháp | Dài hơn, nhiều bước thủ công | Ngắn gọn hơn |
| Cấu hình chung (baseURL, header...) | Phải tự viết wrapper | Có sẵn `axios.create()` (Phần 3) |

```ts
// Axios - lỗi HTTP tự động rơi vào catch, không cần kiểm tra response.ok thủ công
async function getProduct(id: number): Promise<Product> {
  try {
    const response = await axios.get<Product>(`https://dummyjson.com/products/${id}`);
    return response.data;
  } catch (error) {
    throw new Error('Không tải được sản phẩm');
  }
}
```

### 2.3 Truyền `params`, `headers` và `body` trong request

```ts
// GET với query params (Axios tự nối vào URL thành ?q=...&limit=...)
axios.get('https://dummyjson.com/products/search', {
  params: { q: 'áo', limit: 10 },
});

// POST với body và header tùy chỉnh
axios.post(
  'https://dummyjson.com/products/add',
  { title: 'Sản phẩm mới', price: 100000 }, // body
  { headers: { Authorization: 'Bearer some-token' } }
);
```

---

## Phần 3: Tạo Axios Instance Với Base URL Và Interceptor

### 3.1 Tạo instance riêng với `baseURL` từ biến môi trường

```ts
// src/lib/api.ts
import axios from 'axios';

export const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
  timeout: 10000,
});
```

```bash
# .env
VITE_API_URL=https://dummyjson.com
```

> 💡 Nhắc lại quy tắc đặt tên biến môi trường của Vite đã học ở Bài 1: **bắt buộc tiền tố `VITE_`** thì code phía client mới đọc được qua `import.meta.env`. Dùng `axios.create()` với `baseURL` giúp mọi lời gọi API sau này chỉ cần viết đường dẫn tương đối (`api.get('/products')`), không phải lặp lại domain đầy đủ ở mọi nơi.

### 3.2 Request Interceptor — tự động gắn token vào mọi request

```ts
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('auth-token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

**Interceptor** (bộ chặn) là 1 hàm được Axios tự động chạy **trước khi** mỗi request thật sự được gửi đi. Thay vì phải nhớ tự tay thêm header `Authorization` ở **từng nơi gọi API**, Request Interceptor đảm bảo mọi request đi qua `api` instance này đều tự động có token — nếu có.

### 3.3 Response Interceptor — xử lý lỗi tập trung

```ts
api.interceptors.response.use(
  (response) => response, // Response thành công - giữ nguyên
  (error) => {
    if (error.response?.status === 401) {
      localStorage.removeItem('auth-token');
      window.location.href = '/login'; // Tự động đăng xuất khi token hết hạn
    }
    return Promise.reject(error);
  }
);
```

Tương tự, Response Interceptor chạy **sau khi** nhận được phản hồi (hoặc lỗi) từ server — là nơi lý tưởng để xử lý **1 lần duy nhất** các tình huống lặp lại ở nhiều API khác nhau, ví dụ: hễ gặp lỗi 401 (chưa xác thực/token hết hạn) thì tự động đăng xuất, thay vì phải viết lại đoạn kiểm tra này ở mọi hàm gọi API.

---

## Phần 4: Xử Lý Trạng Thái Loading, Error, Success Thủ Công

### 4.1 Khai báo State phản ánh từng giai đoạn gọi API

```tsx
type Product = { id: number; title: string; price: number };

function ProductListPage() {
  const [products, setProducts] = useState<Product[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  // ... useEffect gọi API ở Phần 5
}
```

> 💡 Có thể thay 3 State rời rạc bằng **1 State duy nhất** dạng Union Type (đã học ở Bài 1): `type Status = 'loading' | 'success' | 'error'`. Cách này tránh được các tổ hợp State "vô lý" (ví dụ `loading: true` và `error: '...'` cùng lúc = `true`), nhưng dùng 3 State riêng vẫn là cách phổ biến và dễ đọc với quy mô 1 lần gọi API đơn giản như bài này.

### 4.2 Hiển thị Spinner/Skeleton, thông báo lỗi, hoặc dữ liệu

```tsx
function ProductListPage() {
  const [products, setProducts] = useState<Product[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  // ... (useEffect ở Phần 5)

  if (loading) return <p>Đang tải dữ liệu...</p>; // Hoặc 1 Component Skeleton
  if (error) return <p>Lỗi: {error}</p>;
  if (products.length === 0) return <p>Không có sản phẩm nào.</p>; // Nhắc lại Bài 5

  return (
    <ul>
      {products.map((p) => (
        <li key={p.id}>{p.title} - {p.price}đ</li>
      ))}
    </ul>
  );
}
```

### 4.3 Cấu trúc code chuẩn: `try/catch/finally`

```tsx
useEffect(() => {
  async function fetchProducts() {
    setLoading(true);
    setError(null);
    try {
      const response = await api.get<{ products: Product[] }>('/products');
      setProducts(response.data.products);
    } catch (err) {
      setError('Không tải được danh sách sản phẩm');
    } finally {
      setLoading(false); // Luôn chạy - dù thành công hay lỗi
    }
  }

  fetchProducts();
}, []);
```

`finally` luôn chạy **bất kể** `try` thành công hay `catch` bắt được lỗi — đây là chỗ hợp lý nhất để tắt `loading`, tránh phải lặp lại `setLoading(false)` ở cả 2 nhánh `try` và `catch`.

---

## Phần 5: Gọi API Bên Trong `useEffect` — Race Condition

### 5.0 Nhắc lại: vì sao cần gọi API trong `useEffect`

Đã học kỹ ở Bài 6: gọi Side Effect (trong đó có gọi API) trực tiếp trong thân Component sẽ chạy lại **mỗi lần re-render**, không kiểm soát được. `useEffect` với dependency phù hợp giúp API chỉ được gọi đúng lúc cần (ví dụ khi mount, hoặc khi 1 tham số tìm kiếm thay đổi).

### 5.1 Race Condition là gì

```tsx
function ProductSearch() {
  const [keyword, setKeyword] = useState('');
  const [results, setResults] = useState<Product[]>([]);

  useEffect(() => {
    async function search() {
      const response = await api.get<{ products: Product[] }>(`/products/search?q=${keyword}`);
      setResults(response.data.products); // Không kiểm tra gì trước khi setState
    }
    if (keyword) search();
  }, [keyword]);

  return (
    <div>
      <input value={keyword} onChange={(e) => setKeyword(e.target.value)} />
      <ul>{results.map((p) => <li key={p.id}>{p.title}</li>)}</ul>
    </div>
  );
}
```

> **Race Condition (tình trạng đua) xảy ra khi nhiều request được gửi đi liên tiếp (do `keyword` đổi liên tục khi gõ nhanh), nhưng chúng trả về KHÔNG ĐÚNG THỨ TỰ đã gửi đi** — ví dụ request gõ "á" gửi trước nhưng server phản hồi **chậm hơn** request gõ "áo" gửi sau. Nếu không xử lý gì, kết quả hiển thị cuối cùng trên màn hình sẽ là của từ khóa "á" (đến sau), dù người dùng đã gõ tiếp thành "áo" từ lâu — hiển thị **sai** dữ liệu so với ô input hiện tại.

Đây chính là bài tập "nhận diện vấn đề" đã làm ở Bài 6 (homework), giờ ta học cách khắc phục triệt để.

### 5.2 Cách khắc phục bằng `AbortController`

```tsx
useEffect(() => {
  if (!keyword) return;

  const controller = new AbortController(); // Tạo 1 "công tắc hủy" cho lần gọi API này

  async function search() {
    try {
      const response = await api.get<{ products: Product[] }>(`/products/search`, {
        params: { q: keyword },
        signal: controller.signal, // Gắn "công tắc" vào request
      });
      setResults(response.data.products);
    } catch (error) {
      if (axios.isCancel(error)) return; // Request bị hủy - bỏ qua, không phải lỗi thật
      console.error(error);
    }
  }

  search();

  return () => controller.abort(); // Cleanup (Bài 6) - hủy request CŨ khi keyword đổi tiếp hoặc unmount
}, [keyword]);
```

`AbortController` tạo ra 1 "công tắc" (`signal`) gắn vào request — khi Cleanup Function chạy (đúng theo cơ chế đã học ở Bài 6: chạy **trước** khi Effect kế tiếp thực thi), request **cũ** bị hủy ngay lập tức. Nhờ vậy, chỉ có **request mới nhất** mới có cơ hội `setResults` — giải quyết triệt để Race Condition, thay vì chỉ "giảm thiểu" như cách dùng biến cờ (`ignore`) đã thử ở Bài 6.

---

## Phần 6: Thực Hành — Trang Danh Sách Sản Phẩm Từ API

### 6.1 Tạo Axios Instance và hàm gọi API có kiểu

```ts
// src/lib/api.ts - đúng mục 3.1-3.3
export const api = axios.create({ baseURL: import.meta.env.VITE_API_URL });
```

```ts
// src/services/productService.ts
import { api } from '../lib/api';

export type Product = {
  id: number;
  title: string;
  price: number;
  thumbnail: string;
};

type ProductsResponse = {
  products: Product[];
  total: number;
};

export async function fetchProducts(params: { q?: string; limit?: number; skip?: number }) {
  const response = await api.get<ProductsResponse>('/products/search', { params });
  return response.data;
}
```

### 6.2 Skeleton khi tải, thông báo lỗi kèm nút "Thử lại"

```tsx
function ProductListPage() {
  const [state, setState] = useState<{
    status: 'loading' | 'success' | 'error';
    data: Product[];
  }>({ status: 'loading', data: [] });

  const loadProducts = async () => {
    setState((prev) => ({ ...prev, status: 'loading' }));
    try {
      const { products } = await fetchProducts({ q: '' });
      setState({ status: 'success', data: products });
    } catch {
      setState((prev) => ({ ...prev, status: 'error' }));
    }
  };

  useEffect(() => {
    loadProducts();
  }, []);

  if (state.status === 'loading') return <ProductSkeleton />;
  if (state.status === 'error') {
    return (
      <div>
        <p>Đã có lỗi xảy ra.</p>
        <button onClick={loadProducts}>Thử lại</button>
      </div>
    );
  }

  return <ProductGrid products={state.data} />; // ProductGrid - đã xây dựng từ Bài 3/5
}
```

### 6.3 Tìm kiếm kết hợp `useDebounce` và phân trang

```tsx
function ProductSearchPage() {
  const [keyword, setKeyword] = useState('');
  const debouncedKeyword = useDebounce(keyword, 500); // Custom Hook đã viết ở Bài 7
  const [page, setPage] = useState(1);
  const limit = 6;

  const [products, setProducts] = useState<Product[]>([]);

  useEffect(() => {
    const controller = new AbortController();

    async function search() {
      const { products } = await fetchProducts({
        q: debouncedKeyword,
        limit,
        skip: (page - 1) * limit, // Phân trang - đã học ý tưởng qua Query String ở Bài 8
      });
      setProducts(products);
    }
    search();

    return () => controller.abort();
  }, [debouncedKeyword, page]);

  // ... render input tìm kiếm + danh sách + nút phân trang
}
```

Đoạn code này kết hợp lại gần như toàn bộ kỹ thuật đã học: `useDebounce` (Bài 7) tránh gọi API mỗi lần gõ phím, `AbortController` (mục 5.2) tránh Race Condition khi `debouncedKeyword` hoặc `page` đổi liên tục, và phân trang dựa trên ý tưởng Query String đã thực hành ở Bài 8 (ở đây đơn giản hóa bằng `useState` cục bộ, có thể nâng cấp thành `useSearchParams` nếu muốn giữ trạng thái khi refresh).

---

## 🧪 Bài tập thực hành cuối buổi

1. Viết 2 hàm gọi cùng 1 API (`https://dummyjson.com/products/1`) — 1 hàm dùng `fetch`, 1 hàm dùng `axios`. Cố tình đổi URL thành 1 ID không tồn tại (ví dụ `/products/9999`) và so sánh: hàm nào tự nhảy vào `catch`, hàm nào cần tự kiểm tra `response.ok`.
2. Tạo Axios Instance với `baseURL: 'https://dummyjson.com'`, thêm Response Interceptor: nếu status là `404`, log ra `"Không tìm thấy dữ liệu"` trước khi tiếp tục ném lỗi (`Promise.reject`).
3. Xây dựng 1 trang hiển thị danh sách sản phẩm với đầy đủ 3 trạng thái Loading/Error/Success (mục 4.2) — cố tình thử nghiệm cả 3 trạng thái (tắt mạng để test Error, dùng `React.StrictMode`/DevTools throttle mạng để thấy rõ Loading).
4. Tái hiện lại Race Condition ở mục 5.1 (gõ tìm kiếm thật nhanh, dùng DevTools Network throttle "Slow 3G" để dễ quan sát), sau đó áp dụng `AbortController` (mục 5.2) để khắc phục.

## 🧪 Bài tập HomeWork

Xem tại file [homeworks.md](homeworks.md)
