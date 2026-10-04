# ⭐ Bài 8: React Router — Điều Hướng & Multi-Page App

> 🎯 Mục tiêu: Học viên hiểu vai trò của Router trong SPA, cấu hình được `react-router-dom` với `createBrowserRouter`, làm chủ Nested Routes, `Link`/`NavLink`/`useNavigate`, đọc tham số động qua `useParams`, và tự bảo vệ được Route yêu cầu đăng nhập.

---

## Phần 1: Single Page Application (SPA) Là Gì Và Vai Trò Của Router

### 1.0 Định nghĩa

> **Router là thư viện giúp React đồng bộ giao diện hiển thị với đường dẫn URL trên thanh địa chỉ trình duyệt, mà không cần tải lại trang.**

### 1.1 SPA là gì

Từ Bài 2 (Phần 4.1), ta đã biết React là **Single Page Application**: chỉ có **1 file `index.html` duy nhất**, mọi nội dung còn lại đều do JavaScript "vẽ" ra và thay đổi động. Khi người dùng chuyển từ trang "Trang chủ" sang "Giới thiệu", trình duyệt **không hề tải lại trang** — chỉ có Component hiển thị bên trong `#root` là thay đổi.

### 1.2 Vấn đề nếu không có Router

Nếu chỉ dùng State (`useState`, Bài 4) để quyết định hiển thị Component nào:

```tsx
// ❌ Cách làm thủ công - không dùng Router
function App() {
  const [page, setPage] = useState<'home' | 'about'>('home');

  return (
    <div>
      <button onClick={() => setPage('home')}>Trang chủ</button>
      <button onClick={() => setPage('about')}>Giới thiệu</button>
      {page === 'home' ? <HomePage /> : <AboutPage />}
    </div>
  );
}
```

Cách này **hoạt động được**, nhưng có 3 vấn đề lớn: URL trên thanh địa chỉ **không đổi** (luôn là `/`, không thể chia sẻ link trực tiếp đến trang "Giới thiệu"), nút **Back/Forward** của trình duyệt không hoạt động đúng, và F5 refresh trang sẽ luôn quay về `HomePage` dù đang ở "trang" nào. Router giải quyết cả 3 vấn đề này bằng cách đồng bộ URL thật với Component đang hiển thị.

### 1.3 Cài đặt thư viện `react-router-dom`

```bash
pnpm add react-router-dom
```

---

## Phần 2: Cài Đặt và Cấu Hình React Router (`createBrowserRouter`)

### 2.1 Cú pháp `createBrowserRouter()` định nghĩa danh sách route

```tsx
// router.tsx
import { createBrowserRouter } from 'react-router-dom';
import HomePage from './pages/HomePage';
import AboutPage from './pages/AboutPage';

const router = createBrowserRouter([
  { path: '/', element: <HomePage /> },
  { path: '/about', element: <AboutPage /> },
]);

export default router;
```

`createBrowserRouter` nhận vào 1 **mảng route object** (Phần 2.3), trả về 1 đối tượng router chứa toàn bộ cấu hình điều hướng của ứng dụng.

### 2.2 Bọc ứng dụng bằng `RouterProvider` tại `main.tsx`

```tsx
// main.tsx
import { createRoot } from 'react-dom/client';
import { RouterProvider } from 'react-router-dom';
import router from './router';

createRoot(document.getElementById('root')!).render(
  <RouterProvider router={router} />
);
```

So với Bài 2 (Phần 4.1), ta không còn `render(<App />)` trực tiếp nữa — `RouterProvider` thay thế `App`, tự động hiển thị đúng Component tương ứng với URL hiện tại dựa trên cấu hình `router`.

### 2.3 Cấu trúc 1 route object cơ bản: `path` và `element`

```tsx
{ path: '/products', element: <ProductListPage /> }
//        ↑                        ↑
//   đường dẫn URL          Component hiển thị khi URL khớp path này
```

Mỗi khi URL trên trình duyệt khớp với `path` nào trong danh sách, React Router sẽ render đúng `element` tương ứng vào vị trí `RouterProvider`.

---

## Phần 3: Định Nghĩa Routes, Nested Routes và Trang 404

### 3.1 Tạo nhiều Route cho các trang khác nhau

```tsx
const router = createBrowserRouter([
  { path: '/', element: <HomePage /> },
  { path: '/about', element: <AboutPage /> },
  { path: '/contact', element: <ContactPage /> },
]);
```

### 3.2 Nested Routes — Route lồng nhau dùng chung Layout

Nhiều trang trong thực tế dùng chung 1 phần giao diện cố định (Header, Sidebar, Footer) — thay vì lặp lại chúng ở mỗi trang, ta dùng **Nested Routes**:

```tsx
// layouts/MainLayout.tsx
import { Outlet } from 'react-router-dom';

function MainLayout() {
  return (
    <div>
      <header>Header chung cho mọi trang</header>
      <main>
        <Outlet /> {/* Nội dung của Route con sẽ hiển thị đúng chỗ này */}
      </main>
      <footer>Footer chung cho mọi trang</footer>
    </div>
  );
}

export default MainLayout;
```

```tsx
// router.tsx
const router = createBrowserRouter([
  {
    path: '/',
    element: <MainLayout />,
    children: [
      { index: true, element: <HomePage /> },     // path: '/'
      { path: 'about', element: <AboutPage /> },   // path: '/about'
      { path: 'contact', element: <ContactPage /> }, // path: '/contact'
    ],
  },
]);
```

`<Outlet />` giống hệt `props.children` đã học ở Bài 3 (Phần 6) — là "chỗ trống" để Route con hiển thị nội dung riêng của nó, trong khi `MainLayout` giữ nguyên phần khung chung. Route con dùng `index: true` để đánh dấu đây là route mặc định khi URL khớp đúng path của route cha (`/`), thay vì phải khai báo `path: '/'` lặp lại.

### 3.3 Cấu hình Route bắt lỗi 404

```tsx
const router = createBrowserRouter([
  {
    path: '/',
    element: <MainLayout />,
    children: [
      { index: true, element: <HomePage /> },
      { path: 'about', element: <AboutPage /> },
      { path: '*', element: <NotFoundPage /> }, // khớp MỌI path không tìm thấy ở trên
    ],
  },
]);
```

`path: '*'` là ký tự đại diện (wildcard) — khớp với bất kỳ URL nào **không trùng** với các route đã khai báo trước đó, dùng để hiển thị trang "404 - Không tìm thấy".

---

## Phần 4: Component `Link`/`NavLink` và Điều Hướng Bằng Code Với `useNavigate`

### 4.1 Thẻ `Link` thay cho thẻ `<a>`

```tsx
// ❌ Sai - thẻ <a> thường sẽ RELOAD TOÀN BỘ TRANG, phá vỡ trải nghiệm SPA
<a href="/about">Giới thiệu</a>

// ✅ Đúng - Link điều hướng mà KHÔNG reload trang
import { Link } from 'react-router-dom';

<Link to="/about">Giới thiệu</Link>
```

### 4.2 `NavLink` — tự động thêm class active

```tsx
import { NavLink } from 'react-router-dom';

function Navbar() {
  return (
    <nav>
      <NavLink
        to="/"
        style={({ isActive }) => ({ color: isActive ? 'blue' : 'black' })}
      >
        Trang chủ
      </NavLink>
      <NavLink to="/about" className={({ isActive }) => (isActive ? 'active-link' : '')}>
        Giới thiệu
      </NavLink>
    </nav>
  );
}
```

`NavLink` hoạt động giống `Link`, nhưng cung cấp thêm thông tin `isActive` (`true` khi URL hiện tại khớp đúng `to`) — cho phép style riêng cho menu đang được chọn, thường dùng cho thanh điều hướng chính.

### 4.3 `useNavigate` — điều hướng bằng code

```tsx
import { useNavigate } from 'react-router-dom';

function LoginForm() {
  const navigate = useNavigate();

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    console.log('Đăng nhập thành công');
    navigate('/dashboard'); // điều hướng bằng code, không cần người dùng click Link
  };

  return <form onSubmit={handleSubmit}>...</form>;
}
```

Khác với `Link`/`NavLink` (chỉ điều hướng khi người dùng **click**), `useNavigate` trả về 1 hàm cho phép điều hướng **từ trong logic code** — ví dụ tình huống ở Bài 7: sau khi `handleSubmit` xử lý đăng nhập thành công, tự động chuyển trang mà không cần thêm thao tác click nào từ người dùng.

---

## Phần 5: Lấy Tham Số Từ URL Với `useParams`

### 5.1 Định nghĩa Dynamic Route với dấu hai chấm

```tsx
const router = createBrowserRouter([
  {
    path: '/',
    element: <MainLayout />,
    children: [
      { path: 'products/:id', element: <ProductDetailPage /> }, // :id là tham số động
    ],
  },
]);
```

Dấu `:id` báo cho React Router biết: đoạn URL ở vị trí đó là 1 **giá trị động** (ví dụ `/products/1`, `/products/2`, `/products/abc` đều khớp cùng 1 route này), không phải chuỗi cố định.

### 5.2 Dùng `useParams()` để đọc giá trị tham số

```tsx
import { useParams } from 'react-router-dom';

function ProductDetailPage() {
  const { id } = useParams<{ id: string }>();

  return <p>Đang xem chi tiết sản phẩm có ID: {id}</p>;
}
```

> 💡 `useParams` luôn trả về giá trị kiểu `string` (vì URL bản chất là văn bản), kể cả khi ID thực tế là số — nếu cần dùng như `number` (ví dụ để gọi API theo Bài 10), phải tự chuyển đổi bằng `Number(id)`.

### 5.3 Ứng dụng thực tế: trang chi tiết sản phẩm dựa theo ID

```tsx
interface Product {
  id: number;
  name: string;
  price: number;
}

const products: Product[] = [
  { id: 1, name: 'Áo thun', price: 150000 },
  { id: 2, name: 'Quần jean', price: 350000 },
];

function ProductDetailPage() {
  const { id } = useParams<{ id: string }>();
  const product = products.find((p) => p.id === Number(id)); // ôn lại Bài 1: find()

  if (!product) {
    return <p>Không tìm thấy sản phẩm.</p>;
  }

  return (
    <div>
      <h2>{product.name}</h2>
      <p>{product.price.toLocaleString()}đ</p>
    </div>
  );
}
```

```tsx
// components/ProductList.tsx - link tới từng trang chi tiết
function ProductList() {
  return (
    <ul>
      {products.map((p) => (
        <li key={p.id}>
          <Link to={`/products/${p.id}`}>{p.name}</Link>
        </li>
      ))}
    </ul>
  );
}
```

---

## Phần 6: Bảo Vệ Route (Protected Route)

### 6.1 Khái niệm Protected Route

> **Protected Route là kỹ thuật chặn không cho người dùng truy cập vào 1 số Route nhất định nếu họ chưa thoả điều kiện yêu cầu — phổ biến nhất là "chưa đăng nhập".**

### 6.2 Viết Component bọc kiểm tra điều kiện

```tsx
// components/ProtectedRoute.tsx
import { Navigate, Outlet } from 'react-router-dom';

interface ProtectedRouteProps {
  isAuthenticated: boolean;
}

function ProtectedRoute({ isAuthenticated }: ProtectedRouteProps) {
  if (!isAuthenticated) {
    // Chưa đăng nhập - điều hướng NGAY LẬP TỨC về trang login, không render Route con
    return <Navigate to="/login" replace />;
  }

  // Đã đăng nhập - cho phép hiển thị Route con qua Outlet (ôn lại Phần 3.2)
  return <Outlet />;
}

export default ProtectedRoute;
```

`<Navigate to="..." />` là cách điều hướng **khai báo** (declarative — ôn lại tư duy Bài 1) ngay trong JSX, khác với `useNavigate` (Phần 4.3) vốn dùng để điều hướng theo sự kiện/logic. Prop `replace` giúp trang login **thay thế** lịch sử trình duyệt hiện tại thay vì thêm mới, tránh việc bấm nút Back lại quay về trang vừa bị chặn.

### 6.3 Áp dụng Protected Route cho các Route cần bảo vệ

```tsx
const isUserLoggedIn = false; // giả lập - thực tế sẽ lấy từ State/Store (Bài 9)

const router = createBrowserRouter([
  {
    path: '/',
    element: <MainLayout />,
    children: [
      { index: true, element: <HomePage /> },
      { path: 'login', element: <LoginPage /> },
      {
        element: <ProtectedRoute isAuthenticated={isUserLoggedIn} />,
        children: [
          { path: 'dashboard', element: <DashboardPage /> },
          { path: 'profile', element: <ProfilePage /> },
        ],
      },
    ],
  },
]);
```

`ProtectedRoute` được đặt làm route cha (không có `path` riêng, chỉ đóng vai trò "người gác cổng") bọc quanh các Route con cần bảo vệ (`dashboard`, `profile`). Nếu `isAuthenticated` là `false`, không Route con nào bên trong được render — người dùng luôn bị điều hướng về `/login` trước.

---

## 🧪 Bài tập thực hành cuối buổi

1. Dựng lại cấu trúc Router hoàn chỉnh cho 1 website nhỏ gồm: `MainLayout` (Header + Outlet + Footer), 3 route con `Home`, `About`, `Contact`, và 1 route `*` hiển thị trang 404.
2. Thêm `Navbar` dùng `NavLink` cho cả 3 trang trên, style link đang active có màu khác (dùng `isActive`, Phần 4.2).
3. Xây `ProductListPage` + `ProductDetailPage` với Dynamic Route `products/:id` (Phần 5) — từ danh sách sản phẩm, click vào 1 sản phẩm sẽ điều hướng đến đúng trang chi tiết của nó.
4. Thêm 1 route `/dashboard` được bảo vệ bởi `ProtectedRoute` (Phần 6) với biến `isAuthenticated` giả lập bằng `useState` — thêm 2 nút "Đăng nhập"/"Đăng xuất" ở `Navbar` để đổi giá trị này và quan sát việc `/dashboard` bị chặn hay cho phép truy cập tương ứng.

## 🧪 Bài tập Homework
