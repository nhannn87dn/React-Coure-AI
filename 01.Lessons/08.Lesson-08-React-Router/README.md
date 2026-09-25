# ⭐ Bài 8: React Router — Điều Hướng & Multi-Page App

> 🎯 Mục tiêu: Học viên cấu hình được React Router cho ứng dụng nhiều trang có Layout chung, điều hướng bằng `Link`/`useNavigate`, đọc được tham số từ URL (`useParams`, `useSearchParams`) và bảo vệ được các trang yêu cầu đăng nhập.

---

## Phần 1: Single Page Application (SPA) Là Gì Và Vai Trò Của Router

### 1.0 Nhắc lại nhanh

Từ Bài 1 đến giờ, ứng dụng của ta luôn chỉ có **1 trang duy nhất** (`App.tsx`) — mọi thứ hiển thị đều do Conditional Rendering (Bài 5) quyết định. Bài này học cách xây dựng ứng dụng **nhiều trang thật sự** (Home, About, trang chi tiết sản phẩm...), mỗi trang có 1 địa chỉ URL riêng.

### 1.1 SPA là gì

> **Single Page Application (SPA) là kiểu ứng dụng web chỉ tải 1 file HTML DUY NHẤT (đã học ở Bài 1 — `index.html` với 1 `<div id="root">`), sau đó mọi thay đổi "trang" tiếp theo đều do JavaScript tự vẽ lại giao diện, KHÔNG reload lại toàn bộ trang.**

Ứng dụng React ta viết từ đầu khóa học đến giờ đã là SPA, chỉ là chưa có khái niệm "nhiều trang với URL khác nhau" mà thôi.

### 1.2 Vấn đề nếu không có Router

Nếu chỉ dùng Conditional Rendering thuần túy để "giả lập nhiều trang" (ví dụ 1 State `currentPage: 'home' | 'about'` quyết định hiển thị gì), sẽ gặp ngay các vấn đề:

- **URL không đổi:** dù đang xem "trang" nào, thanh địa chỉ trình duyệt vẫn hiển thị y hệt 1 địa chỉ — không thể gửi link trực tiếp đến 1 trang cụ thể cho người khác.
- **Nút Back/Forward của trình duyệt không hoạt động đúng:** trình duyệt không biết người dùng đã "điều hướng" giữa các trang.
- **Refresh trang bị mất trạng thái:** tải lại trang luôn quay về màn hình mặc định.

> **React Router giải quyết đúng vấn đề này: đồng bộ URL trên thanh địa chỉ với giao diện đang hiển thị, mà KHÔNG cần reload lại trang** — vẫn giữ nguyên bản chất SPA (nhanh, mượt), nhưng có đầy đủ URL, nút Back/Forward, khả năng chia sẻ link như 1 website nhiều trang truyền thống.

### 1.3 Cài đặt thư viện `react-router`

```bash
pnpm add react-router
```

> 💡 Từ **React Router v7**, package chính thức là `react-router` (bao gồm cả các API dùng cho web lẫn cho React Native). Các tài liệu/dự án cũ hơn thường cài `react-router-dom` — package này vẫn còn nhưng chỉ để tương thích ngược, khóa học dùng thẳng `react-router`.

---

## Phần 2: Cài Đặt Và Cấu Hình React Router (`createBrowserRouter`)

### 2.1 Cú pháp `createBrowserRouter()` định nghĩa danh sách route

```tsx
// src/router.tsx
import { createBrowserRouter } from 'react-router';
import HomePage from './pages/HomePage';
import AboutPage from './pages/AboutPage';

export const router = createBrowserRouter([
  { path: '/', element: <HomePage /> },
  { path: '/about', element: <AboutPage /> },
]);
```

`createBrowserRouter` nhận vào **1 mảng các route object**, trả về 1 đối tượng router — dùng mảng ở đây áp dụng đúng kỹ thuật `map()` đã quen thuộc từ Bài 5 (dù ở đây khai báo trực tiếp, không `map()` qua dữ liệu).

### 2.2 Bọc ứng dụng bằng `RouterProvider` tại `main.tsx`

```tsx
// main.tsx
import { createRoot } from 'react-dom/client';
import { RouterProvider } from 'react-router';
import { router } from './router';

createRoot(document.getElementById('root')!).render(
  <RouterProvider router={router} />
);
```

Khác với các bài trước (`<App />` được render trực tiếp), giờ ta render `<RouterProvider>`, truyền vào router vừa tạo — `RouterProvider` sẽ tự động hiển thị đúng `element` tương ứng với `path` khớp với URL hiện tại.

### 2.3 Cấu trúc 1 route object cơ bản: `path` và `element`

Mỗi phần tử trong mảng truyền vào `createBrowserRouter` là **1 object** với tối thiểu 2 thuộc tính: `path` (chuỗi URL) và `element` (JSX sẽ hiển thị khi URL khớp `path` đó) — đơn giản là 1 `type` bạn có thể tự hình dung: `{ path: string; element: React.ReactNode }` (kiểu `React.ReactNode` đã học ở Bài 3).

---

## Phần 3: Định Nghĩa Routes, Nested Routes Và Trang 404

### 3.1 Tạo nhiều Route cho các trang khác nhau

```tsx
export const router = createBrowserRouter([
  { path: '/', element: <HomePage /> },
  { path: '/about', element: <AboutPage /> },
  { path: '/contact', element: <ContactPage /> },
]);
```

### 3.2 Nested Routes — Route lồng nhau dùng chung Layout

Hầu hết ứng dụng thực tế có 1 phần giao diện **lặp lại ở mọi trang** (thanh điều hướng, footer). Thay vì copy-paste `<Navbar />` vào từng trang, ta dùng **Nested Routes**:

```tsx
// layouts/MainLayout.tsx
import { Outlet } from 'react-router';

function MainLayout() {
  return (
    <div>
      <nav>{/* Thanh điều hướng dùng chung */}</nav>
      <main>
        <Outlet /> {/* Nội dung của route con sẽ render vào đây */}
      </main>
      <footer>© 2026</footer>
    </div>
  );
}

export default MainLayout;
```

```tsx
export const router = createBrowserRouter([
  {
    path: '/',
    element: <MainLayout />,
    children: [
      { index: true, element: <HomePage /> },      // "index: true" ứng với path cha ("/")
      { path: 'about', element: <AboutPage /> },     // Ứng với "/about"
      { path: 'contact', element: <ContactPage /> }, // Ứng với "/contact"
    ],
  },
]);
```

> **`<Outlet />` là 1 Component đặc biệt của React Router, đóng vai trò "chỗ trống" bên trong Layout — route con nào đang khớp URL sẽ được render đúng vào vị trí đặt `<Outlet />`.** Đây chính là ứng dụng thực tế của Composition Pattern (`children`, đã học ở Bài 3), chỉ khác là "nội dung" được quyết định bởi Router thay vì bởi Component cha truyền trực tiếp.

Chú ý: route con dùng `path: 'about'` (không có dấu `/` ở đầu) vì đường dẫn này được **nối tiếp** vào `path` của route cha (`/` + `about` = `/about`).

### 3.3 Cấu hình Route bắt lỗi 404

```tsx
export const router = createBrowserRouter([
  {
    path: '/',
    element: <MainLayout />,
    children: [
      { index: true, element: <HomePage /> },
      { path: 'about', element: <AboutPage /> },
      { path: '*', element: <NotFoundPage /> }, // Khớp với MỌI path không trùng các route ở trên
    ],
  },
]);
```

`path: '*'` là 1 route "bắt hết" (catch-all) — luôn đặt **ở cuối cùng** trong mảng `children`, vì React Router khớp route theo thứ tự khai báo, và route này khớp với bất kỳ URL nào.

---

## Phần 4: Component `Link`/`NavLink` Và Điều Hướng Bằng Code Với `useNavigate`

### 4.1 Thẻ `Link` thay cho thẻ `<a>`

```tsx
import { Link } from 'react-router';

function Navbar() {
  return (
    <nav>
      <Link to="/">Trang chủ</Link>
      <Link to="/about">Giới thiệu</Link>
    </nav>
  );
}
```

> ⚠️ **Không dùng thẻ `<a href="...">` để điều hướng nội bộ trong ứng dụng React Router.** Thẻ `<a>` gốc khiến trình duyệt **reload lại toàn bộ trang** (đúng hành vi mặc định của HTML), phá vỡ hoàn toàn lợi ích SPA đã nói ở Phần 1. `<Link>` render ra thẻ `<a>` thật trên DOM (vẫn có `href` để nhấn chuột phải mở tab mới được), nhưng chặn hành vi reload mặc định và tự điều hướng bằng JavaScript.

### 4.2 `NavLink` — tự động thêm class `active`

```tsx
import { NavLink } from 'react-router';

function Navbar() {
  return (
    <nav>
      <NavLink to="/" className={({ isActive }) => (isActive ? 'nav-link active' : 'nav-link')}>
        Trang chủ
      </NavLink>
      <NavLink to="/about" className={({ isActive }) => (isActive ? 'nav-link active' : 'nav-link')}>
        Giới thiệu
      </NavLink>
    </nav>
  );
}
```

`NavLink` hoạt động giống `Link`, nhưng `className` (và `style`) có thể nhận vào **1 hàm** thay vì 1 chuỗi cố định — hàm này nhận tham số `{ isActive: boolean }` (đang ở đúng route này hay không), cho phép tô đậm mục đang được chọn trên thanh điều hướng.

### 4.3 `useNavigate` — điều hướng bằng code

```tsx
import { useNavigate } from 'react-router';

function LoginForm() {
  const navigate = useNavigate();

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault(); // Đã học ở Bài 7
    // ... xử lý đăng nhập ...
    navigate('/dashboard'); // Điều hướng bằng code, không cần người dùng click Link nào
  };

  return <form onSubmit={handleSubmit}>{/* ... */}</form>;
}
```

Dùng `useNavigate` khi việc điều hướng **không xuất phát từ 1 cú click trực tiếp** lên `Link`, mà là **kết quả của 1 hành động khác** — phổ biến nhất: điều hướng sang trang khác ngay sau khi submit Form thành công (đã thực hành ở Bài 7).

---

## Phần 5: Lấy Tham Số Từ URL Với `useParams`

### 5.1 Định nghĩa Dynamic Route

```tsx
{ path: 'products/:id', element: <ProductDetailPage /> }
```

Cú pháp `:id` (dấu hai chấm trước tên) đánh dấu **1 đoạn URL động** — route này khớp với `/products/1`, `/products/2`, `/products/ao-thun`... bất kỳ giá trị nào ở đúng vị trí đó.

### 5.2 Dùng `useParams()` để đọc giá trị tham số động

```tsx
import { useParams } from 'react-router';

function ProductDetailPage() {
  const { id } = useParams(); // id có kiểu: string | undefined

  return <p>Đang xem sản phẩm có ID: {id}</p>;
}
```

> ⚠️ **Giá trị trả về từ `useParams()` luôn có kiểu `string | undefined`** (Union Type — đã học ở Bài 1), không bao giờ là `number`, kể cả khi URL chỉ chứa toàn chữ số. TypeScript không thể biết chắc `useParams()` luôn được gọi đúng trong 1 route có `:id`, nên nó thận trọng để `undefined` là khả năng luôn phải xử lý. Muốn dùng `id` như 1 số (ví dụ để gọi API), phải tự chuyển kiểu và validate:

```tsx
function ProductDetailPage() {
  const { id } = useParams();
  const productId = Number(id); // Chuyển sang number

  if (!id || Number.isNaN(productId)) {
    return <p>ID sản phẩm không hợp lệ</p>;
  }

  return <p>Đang xem sản phẩm có ID: {productId}</p>;
}
```

### 5.3 Ứng dụng thực tế: trang chi tiết sản phẩm dựa theo ID trên URL

```tsx
const products = [
  { id: 1, name: 'Áo thun' },
  { id: 2, name: 'Quần jean' },
];

function ProductDetailPage() {
  const { id } = useParams();
  const product = products.find((p) => p.id === Number(id)); // find() - đã ôn ở Bài 1

  if (!product) {
    return <p>Không tìm thấy sản phẩm.</p>;
  }

  return <h1>{product.name}</h1>;
}
```

---

## Phần 6: Query String Với `useSearchParams`

### 6.0 Query String là gì

> **Query String là phần URL sau dấu `?`, dùng để mang theo dữ liệu dạng cặp `key=value`, nối nhau bằng dấu `&`.** Ví dụ: `/products?q=ao&sort=price&page=2` — có 3 cặp: `q=ao`, `sort=price`, `page=2`.

**Khác biệt với `useParams` (Phần 5):** tham số động (`:id`) là **phần bắt buộc, cố định trong cấu trúc URL** (route chỉ khớp khi có đủ đoạn đó), còn Query String là phần **tùy chọn, đi kèm thêm** — cùng 1 trang `/products` có thể có hoặc không có Query String, và có thể mang nhiều cặp `key=value` khác nhau tùy tình huống (tìm kiếm, sắp xếp, phân trang...).

### 6.1 Đọc và cập nhật Query String bằng `useSearchParams()`

```tsx
import { useSearchParams } from 'react-router';

function ProductListPage() {
  const [searchParams, setSearchParams] = useSearchParams();

  const keyword = searchParams.get('q') ?? ''; // Đọc giá trị "q" - luôn trả về string | null, dùng ?? cho giá trị mặc định

  const handleSearch = (newKeyword: string) => {
    setSearchParams({ q: newKeyword }); // Cập nhật Query String -> URL tự đổi thành "?q=..."
  };

  return (
    <div>
      <input value={keyword} onChange={(e) => handleSearch(e.target.value)} />
      {/* ... render danh sách đã lọc theo keyword ... */}
    </div>
  );
}
```

`useSearchParams()` hoạt động rất giống `useState` (trả về 1 cặp `[giá trị, hàm cập nhật]`), chỉ khác là "giá trị" ở đây được **đọc/ghi trực tiếp vào URL** thay vì lưu trong bộ nhớ Component.

### 6.2 Ứng dụng: giữ nguyên trạng thái khi reload hoặc chia sẻ link

So sánh với cách đã làm ở Bài 5 (lưu `keyword`/`category` bằng `useState`): nếu người dùng gõ từ khóa tìm kiếm rồi **refresh trang** hoặc **copy link gửi cho bạn bè**, State thông thường sẽ **mất trắng** — quay về giá trị mặc định. Với `useSearchParams`, vì giá trị nằm ngay trên URL (`/products?q=ao-thun`), refresh trang hay mở link đó ở máy khác đều cho ra **đúng kết quả tìm kiếm đã lưu**.

> 💡 **Quy tắc thực tế:** dữ liệu ảnh hưởng đến "trang này đang hiển thị gì" và **nên giữ lại được khi chia sẻ/refresh** (từ khóa tìm kiếm, bộ lọc, số trang phân trang) → dùng `useSearchParams`. Dữ liệu chỉ có ý nghĩa tạm thời trong phiên tương tác hiện tại (ví dụ nội dung đang gõ dở trong 1 ô input chưa submit) → vẫn dùng `useState` như bình thường.

---

## Phần 7: Bảo Vệ Route (Protected Route)

### 7.0 Khái niệm Protected Route

> **Protected Route là kỹ thuật chặn quyền truy cập vào 1 số route nhất định nếu điều kiện nào đó chưa thỏa (phổ biến nhất: người dùng chưa đăng nhập) — tự động điều hướng sang trang khác (thường là trang Login) thay vì hiển thị nội dung được bảo vệ.**

### 7.1 Lưu trạng thái đăng nhập tạm thời bằng `useLocalStorage`

```tsx
function useAuth() {
  const [token] = useLocalStorage<string | null>('auth-token', null); // Custom Hook đã viết ở Bài 7
  return { isLoggedIn: token !== null };
}
```

> 💡 Đây là giải pháp **tạm thời** cho bài học này — dùng lại `useLocalStorage` đã viết ở Bài 7 để không phải học thêm công cụ mới ngay lúc này. Bài 9 sẽ thay thế cách lưu trạng thái đăng nhập này bằng **Zustand** (kèm `persist` middleware), giải quyết gọn gàng hơn khi nhiều Component khác nhau đều cần biết trạng thái đăng nhập.

### 7.2 Viết Component bọc (Wrapper) kiểm tra điều kiện

```tsx
import { Navigate, Outlet } from 'react-router';

function ProtectedRoute() {
  const { isLoggedIn } = useAuth();

  if (!isLoggedIn) {
    return <Navigate to="/login" replace />;
  }

  return <Outlet />; // Đã đăng nhập - cho phép render route con
}
```

`<Navigate>` là 1 Component đặc biệt: chỉ cần render nó, React Router sẽ **lập tức điều hướng** sang `to` — tương đương gọi `useNavigate()` (Phần 4.3) nhưng viết dưới dạng khai báo (declarative — đúng tinh thần React đã học từ Bài 1), phù hợp khi quyết định điều hướng phụ thuộc trực tiếp vào 1 điều kiện lúc render. Prop `replace` giúp thay thế URL hiện tại trong lịch sử trình duyệt thay vì thêm 1 mục mới — người dùng bấm nút Back sẽ không quay lại được trang được bảo vệ vừa bị chặn.

### 7.3 Áp dụng Protected Route cho các Route cần bảo vệ

```tsx
export const router = createBrowserRouter([
  {
    path: '/',
    element: <MainLayout />,
    children: [
      { index: true, element: <HomePage /> },
      { path: 'login', element: <LoginPage /> },
      {
        element: <ProtectedRoute />, // Route "bọc" không có path riêng
        children: [
          { path: 'dashboard', element: <DashboardPage /> },
          { path: 'profile', element: <ProfilePage /> },
        ],
      },
    ],
  },
]);
```

`ProtectedRoute` được đặt làm **route cha** của `dashboard` và `profile` (không có `path` riêng — nó không thêm gì vào URL, chỉ đóng vai trò "gác cổng"). Nhờ Nested Routes (Phần 3.2), mọi route con bên trong chỉ được render khi `ProtectedRoute` cho phép (`<Outlet />` chỉ xuất hiện khi đã đăng nhập).

---

## Phần 8: Thực Hành — Mini Shop Nhiều Trang

### 8.1 Layout chung có thanh điều hướng và trang 404

```text
src/
├── router.tsx
├── layouts/
│   └── MainLayout.tsx       # Navbar (NavLink) + <Outlet />
└── pages/
    ├── HomePage.tsx
    ├── ProductListPage.tsx   # /products - tìm kiếm qua useSearchParams
    ├── ProductDetailPage.tsx # /products/:id
    ├── LoginPage.tsx
    ├── DashboardPage.tsx     # Được bảo vệ
    └── NotFoundPage.tsx      # path: "*"
```

### 8.2 Trang danh sách sản phẩm tìm kiếm/lọc qua `useSearchParams`, trang chi tiết `/products/:id`

Áp dụng đúng kỹ thuật Phần 5 và Phần 6: `ProductListPage` đọc/ghi từ khóa tìm kiếm qua `useSearchParams` (Phần 6.1), mỗi sản phẩm trong danh sách là 1 `<Link to={`/products/${product.id}`}>` (Phần 4.1) dẫn sang `ProductDetailPage` đọc `id` qua `useParams` (Phần 5.2).

### 8.3 Trang Login và Dashboard được bảo vệ bằng Protected Route

Áp dụng đúng cấu trúc Phần 7.3: `LoginPage` dùng `useNavigate` (Phần 4.3) để điều hướng sang `/dashboard` ngay sau khi "đăng nhập" thành công (giả lập — ghi token bất kỳ vào `localStorage` qua `useLocalStorage`), `DashboardPage` chỉ xem được sau khi đã có token.

---

## 🧪 Bài tập thực hành cuối buổi

1. Tạo 1 dự án Vite mới (hoặc dùng lại dự án cũ), cài `react-router`, cấu hình `createBrowserRouter` với 3 route tĩnh: `/`, `/about`, `/contact`, dùng chung 1 `MainLayout` có `NavLink`.
2. Thêm route `path: "*"` hiển thị trang 404 tùy chỉnh, có 1 `<Link to="/">Về trang chủ</Link>`.
3. Thêm route động `/users/:username`, hiển thị `"Trang cá nhân của {username}"` — thử truy cập với vài giá trị `username` khác nhau trên URL.
4. Xây dựng 1 trang `/search` đọc/ghi từ khóa qua `useSearchParams`, kiểm tra: gõ từ khóa, copy URL, mở URL đó ở 1 tab mới, xác nhận từ khóa vẫn hiển thị đúng.

## 🧪 Bài tập HomeWork

Xem tại file [homeworks.md](homeworks.md)
