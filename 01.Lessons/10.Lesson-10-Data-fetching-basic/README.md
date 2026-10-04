# ⭐ Bài 10: Data Fetching Cơ Bản (Fetch API & Axios)

> 🎯 Mục tiêu: Học viên gọi API thành thạo bằng `fetch` lẫn `axios`, biết cách tạo Axios instance dùng chung cho dự án, tự tay quản lý trạng thái loading/error/success, và hiểu + xử lý được Race Condition khi gọi API bên trong `useEffect`.

---

## Phần 1: Gọi API Với Fetch — Cú Pháp và Xử Lý Lỗi

### 1.1 Cú pháp cơ bản `fetch(url).then().catch()` và dùng với Async/Await

```tsx
// Cách 1 - dùng .then()/.catch() (ôn lại Bài 1, Phần 6.3)
fetch('https://api.example.com/products')
  .then((response) => response.json())
  .then((data) => console.log(data))
  .catch((error) => console.log('Lỗi:', error));

// Cách 2 - dùng Async/Await (ôn lại Bài 1, Phần 6.4) - dễ đọc hơn, được ưu tiên trong bài này
async function getProducts() {
  try {
    const response = await fetch('https://api.example.com/products');
    const data = await response.json();
    return data;
  } catch (error) {
    console.log('Lỗi:', error);
  }
}
```

### 1.2 Chuyển response về JSON bằng `response.json()`

`fetch()` trả về 1 object `Response` — chưa phải dữ liệu thật, chỉ là "gói tin" mô tả kết quả HTTP. Phải gọi thêm `response.json()` (bản thân nó cũng là 1 `Promise` bất đồng bộ, nên cần `await` thêm 1 lần nữa) để lấy được dữ liệu JSON thật sự.

### 1.3 Xử lý lỗi HTTP — `fetch` không tự throw lỗi khi status 404/500

```tsx
async function getProducts() {
  const response = await fetch('https://api.example.com/products-khong-ton-tai');

  // ⚠️ Dù response trả về lỗi 404, fetch() KHÔNG tự throw lỗi ở đây!
  // .catch()/try-catch chỉ bắt được lỗi MẠNG (mất kết nối), không bắt được lỗi HTTP status

  if (!response.ok) {
    // response.ok là true khi status nằm trong khoảng 200-299
    throw new Error(`Lỗi HTTP: ${response.status}`);
  }

  const data = await response.json();
  return data;
}
```

Đây là điểm gây nhầm lẫn phổ biến nhất với `fetch`: **status 404/500 vẫn được coi là 1 request "thành công" về mặt mạng** — `fetch` chỉ thực sự bị bắt lỗi (`catch`) khi có sự cố ở tầng mạng (mất kết nối, sai domain...). Muốn xử lý đúng lỗi HTTP, luôn phải tự kiểm tra `response.ok` và tự `throw` lỗi khi cần.

---

## Phần 2: Cài Đặt và Sử Dụng Axios

### 2.1 Cài đặt `axios` và cú pháp gọi GET/POST cơ bản

```bash
pnpm add axios
```

```tsx
import axios from 'axios';

// GET
async function getProducts() {
  const response = await axios.get('https://api.example.com/products');
  return response.data; // dữ liệu JSON đã được parse sẵn
}

// POST
async function createProduct(product: { name: string; price: number }) {
  const response = await axios.post('https://api.example.com/products', product);
  return response.data;
}
```

### 2.2 So sánh Axios và Fetch

| Tiêu chí | `fetch` | `axios` |
|---|---|---|
| Tự parse JSON | Không (phải `.json()` thủ công — Phần 1.2) | Có sẵn (`response.data`) |
| Tự throw lỗi khi status 4xx/5xx | Không (Phần 1.3) | Có — status lỗi tự nhảy vào `catch` |
| Cú pháp truyền `body` (POST) | Phải `JSON.stringify()` thủ công | Truyền thẳng object |
| Hỗ trợ có sẵn trong trình duyệt | Có (không cần cài thêm) | Không (cần cài thư viện) |

### 2.3 Truyền `params`, `headers` và `body` trong request

```tsx
async function searchProducts(keyword: string, token: string) {
  const response = await axios.get('https://api.example.com/products', {
    params: { search: keyword }, // tự động nối thành query string: ?search=...
    headers: { Authorization: `Bearer ${token}` },
  });
  return response.data;
}

async function updateProduct(id: number, data: { price: number }) {
  const response = await axios.put(`https://api.example.com/products/${id}`, data);
  return response.data;
}
```

---

## Phần 3: Tạo Axios Instance Với Base URL và Interceptor

### 3.1 Tạo instance riêng bằng `axios.create()`

```tsx
// lib/axiosInstance.ts
import axios from 'axios';

const axiosInstance = axios.create({
  baseURL: 'https://api.example.com',
  timeout: 10000,
});

export default axiosInstance;
```

```tsx
// Sử dụng - không cần lặp lại URL gốc ở mọi nơi gọi API
import axiosInstance from './lib/axiosInstance';

async function getProducts() {
  const response = await axiosInstance.get('/products'); // tự động ghép thành .../products
  return response.data;
}
```

Thay vì gõ lặp lại `https://api.example.com` ở mọi lời gọi API trong dự án, `axios.create()` tạo ra **1 instance dùng chung** với cấu hình cố định (`baseURL`) — nếu sau này domain API thay đổi, chỉ cần sửa đúng 1 chỗ.

### 3.2 Request Interceptor — tự động gắn token xác thực

```tsx
axiosInstance.interceptors.request.use((config) => {
  const token = localStorage.getItem('access_token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

Request Interceptor chạy **trước khi mỗi request được gửi đi** — tự động gắn token xác thực vào `headers`, thay vì phải tự viết lại đoạn code này ở mọi nơi gọi API cần xác thực (như Phần 2.3 đã làm thủ công).

### 3.3 Response Interceptor — xử lý lỗi tập trung

```tsx
axiosInstance.interceptors.response.use(
  (response) => response, // request thành công - trả về nguyên response
  (error) => {
    if (error.response?.status === 401) {
      // Token hết hạn/không hợp lệ - tự động logout
      localStorage.removeItem('access_token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);
```

Response Interceptor chạy **sau khi nhận response** (kể cả response lỗi) — cho phép xử lý logic dùng chung 1 lần duy nhất, ví dụ tự động chuyển hướng về trang login khi gặp lỗi `401 Unauthorized` ở bất kỳ API nào, thay vì phải viết lặp lại logic này ở từng nơi.

---

## Phần 4: Xử Lý Trạng Thái Loading, Error, Success Thủ Công

### 4.1 Khai báo 3 State riêng biệt

```tsx
interface Product {
  id: number;
  name: string;
  price: number;
}

function ProductList() {
  const [data, setData] = useState<Product[]>([]);
  const [loading, setLoading] = useState<boolean>(false);
  const [error, setError] = useState<string | null>(null);

  // ...
}
```

3 State này phản ánh đúng **3 trạng thái của 1 Promise** đã học ở Bài 1 (Phần 6.3): `pending` (loading), `rejected` (error), `fulfilled` (data đã có).

### 4.2 Hiển thị Spinner/Skeleton, thông báo lỗi, hoặc dữ liệu

```tsx
function ProductList() {
  const [data, setData] = useState<Product[]>([]);
  const [loading, setLoading] = useState<boolean>(false);
  const [error, setError] = useState<string | null>(null);

  // Phần render dựa theo State - kết hợp conditional rendering đã học ở Bài 5
  if (loading) return <p>Đang tải dữ liệu...</p>;
  if (error) return <p style={{ color: 'red' }}>Lỗi: {error}</p>;

  return (
    <ul>
      {data.map((p) => (
        <li key={p.id}>{p.name}</li>
      ))}
    </ul>
  );
}
```

### 4.3 Cấu trúc chuẩn: `try/catch/finally`

```tsx
import { useEffect, useState } from 'react';
import axiosInstance from '../lib/axiosInstance';

function ProductList() {
  const [data, setData] = useState<Product[]>([]);
  const [loading, setLoading] = useState<boolean>(false);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    async function fetchProducts() {
      setLoading(true);
      setError(null);

      try {
        const response = await axiosInstance.get<Product[]>('/products');
        setData(response.data);
      } catch (err) {
        setError('Không thể tải danh sách sản phẩm');
      } finally {
        setLoading(false); // luôn chạy dù thành công hay thất bại
      }
    }

    fetchProducts();
  }, []); // ôn lại Bài 6, Phần 5.1: gọi API khi Component mount

  if (loading) return <p>Đang tải dữ liệu...</p>;
  if (error) return <p style={{ color: 'red' }}>Lỗi: {error}</p>;

  return (
    <ul>
      {data.map((p) => (
        <li key={p.id}>{p.name}</li>
      ))}
    </ul>
  );
}

export default ProductList;
```

Khối `finally` luôn chạy bất kể `try` thành công hay `catch` bắt được lỗi — đảm bảo `loading` luôn được tắt đúng lúc, tránh tình trạng giao diện "kẹt" mãi ở trạng thái loading nếu quên tắt trong nhánh lỗi.

---

## Phần 5: Gọi API Bên Trong `useEffect` — Lỗi Race Condition Thường Gặp

### 5.1 Vì sao cần gọi API trong `useEffect` thay vì trực tiếp trong thân Component

Đây chính là nguyên tắc đã học ở Bài 6 (Phần 1.1): gọi API là 1 **Side Effect**, không nên đặt trực tiếp trong thân hàm Component (sẽ chạy lại ở mọi lần render), mà phải đặt trong `useEffect` để kiểm soát đúng thời điểm chạy qua Dependency Array.

### 5.2 Race Condition là gì

> **Race Condition là hiện tượng dữ liệu trả về không đúng thứ tự khi params thay đổi liên tục — response của request CŨ hơn lại đến SAU và ghi đè lên response của request MỚI hơn.**

```tsx
// ❌ Có nguy cơ Race Condition
function SearchResults({ keyword }: { keyword: string }) {
  const [results, setResults] = useState<Product[]>([]);

  useEffect(() => {
    async function search() {
      const response = await axiosInstance.get<Product[]>(`/products?search=${keyword}`);
      setResults(response.data); // ⚠️ Không kiểm tra đây có còn là request MỚI NHẤT không
    }
    search();
  }, [keyword]);

  return <ul>{results.map((p) => <li key={p.id}>{p.name}</li>)}</ul>;
}
```

Tình huống cụ thể: người dùng gõ nhanh "a" → "ab" → "abc" vào ô tìm kiếm. Mỗi lần gõ, `keyword` đổi, Effect chạy lại, gửi đi 1 request mới (đúng theo Phần 3.3, Dependency Array ở Bài 6). Nhưng nếu request của "a" (do mạng chậm) trả lời **chậm hơn** request của "abc", nó sẽ set `results` **sau cùng** — hiển thị nhầm kết quả của "a" dù người dùng đã gõ xong "abc".

### 5.3 Cách khắc phục bằng biến cờ hoặc `AbortController`

**Cách 1 — biến cờ (flag), tận dụng Cleanup Function đã học ở Bài 6 (Phần 4):**

```tsx
useEffect(() => {
  let isCancelled = false;

  async function search() {
    const response = await axiosInstance.get<Product[]>(`/products?search=${keyword}`);
    if (!isCancelled) {
      setResults(response.data); // chỉ set nếu Effect này CHƯA bị "huỷ" bởi lần chạy kế tiếp
    }
  }

  search();

  return () => {
    isCancelled = true; // Cleanup chạy TRƯỚC khi Effect kế tiếp chạy (Bài 6, Phần 4.2)
  };
}, [keyword]);
```

**Cách 2 — `AbortController`, huỷ thẳng request cũ ở tầng mạng:**

```tsx
useEffect(() => {
  const controller = new AbortController();

  async function search() {
    try {
      const response = await axiosInstance.get<Product[]>(`/products?search=${keyword}`, {
        signal: controller.signal,
      });
      setResults(response.data);
    } catch (err) {
      if (axios.isCancel(err)) return; // request bị huỷ - bỏ qua, không phải lỗi thật
      console.log('Lỗi tìm kiếm:', err);
    }
  }

  search();

  return () => controller.abort(); // huỷ request cũ khi keyword đổi hoặc Component unmount
}, [keyword]);
```

`AbortController` là giải pháp triệt để hơn — không chỉ "bỏ qua" response cũ như cách dùng biến cờ, mà **huỷ thẳng request đang bay trên mạng**, tiết kiệm băng thông và tránh xử lý dữ liệu không cần thiết.

---

## 🧪 Bài tập thực hành cuối buổi

1. Viết Component `UserProfile` gọi API `https://jsonplaceholder.typicode.com/users/1` bằng `fetch`, xử lý đúng lỗi HTTP theo Phần 1.3 (kiểm tra `response.ok`), hiển thị tên và email người dùng.
2. Tạo `axiosInstance` với `baseURL: 'https://jsonplaceholder.typicode.com'` (Phần 3.1), viết lại `UserProfile` ở bài 1 bằng `axiosInstance.get('/users/1')` — so sánh độ ngắn gọn giữa 2 cách.
3. Xây Component `ProductList` đầy đủ 3 trạng thái loading/error/success (Phần 4) — cố tình gọi sai URL (API không tồn tại) để quan sát nhánh `error` hiển thị đúng.
4. Xây Component `SearchBox` gọi API tìm kiếm theo từ khoá gõ vào (mô phỏng bằng `setTimeout` giả lập độ trễ mạng ngẫu nhiên trong hàm fetch giả), áp dụng kỹ thuật `AbortController` ở Phần 5.3 để tránh Race Condition — thử gõ thật nhanh để kiểm chứng kết quả luôn khớp đúng với từ khoá cuối cùng.

## 🧪 Bài tập Homework
