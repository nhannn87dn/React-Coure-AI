# 📝 Bài Tập Về Nhà — Bài 8: React Router - Điều Hướng & Multi-Page App

> Mục tiêu: Mở rộng Mini Shop đã xây dựng trên lớp với phân trang qua Query String, giữ nguyên bộ lọc khi điều hướng, và hoàn thiện luồng đăng nhập với Protected Route.

---

## Bài 1 — Phân trang bằng Query String

**1. Dữ liệu ban đầu:** Danh sách sản phẩm tĩnh (tối thiểu 20-30 sản phẩm để phân trang có ý nghĩa) trong `ProductListPage` đã xây dựng trên lớp.

**2. Yêu cầu:**

- Thêm tham số `page` vào Query String (ví dụ `/products?page=2`), dùng `useSearchParams` (mục 6.1) để đọc và cập nhật.
- Hiển thị mỗi trang tối đa 6 sản phẩm (tự tính bằng `slice()` dựa trên `page` hiện tại — không cần gọi API, dữ liệu vẫn tĩnh).
- Có nút "Trang trước" / "Trang sau", disable đúng khi đang ở trang đầu/trang cuối.
- Khi `page` không hợp lệ (ví dụ nhỏ hơn 1, hoặc lớn hơn tổng số trang), tự động đưa về trang 1.
- Khi thay đổi từ khóa tìm kiếm (`q`) hoặc bộ lọc danh mục, tự động reset `page` về `1` trong cùng Query String (không xóa mất `q`/`category` đang có).

---

## Bài 2 — Trang chi tiết giữ nguyên bộ lọc khi quay lại

**1. Yêu cầu:**

- Trên `ProductDetailPage` (`/products/:id`), thêm nút **"Quay lại danh sách"**.
- Nút này phải điều hướng về **đúng** `ProductListPage` với **nguyên vẹn** Query String đã áp dụng trước đó (`q`, `category`, `page`) — không phải quay về `/products` trống trơn.
- Gợi ý: khi điều hướng từ `ProductListPage` sang trang chi tiết (`<Link to={...}>`), truyền kèm Query String hiện tại vào URL đích thông qua `state` của `Link`/`useNavigate`, hoặc nối trực tiếp `location.search` (tự tìm hiểu thêm `useLocation` từ `react-router`) vào đường dẫn quay lại.

---

## Bài 3 — Chuyển hướng đúng trang sau khi đăng nhập thành công

**1. Yêu cầu:** Cải thiện `ProtectedRoute` (mục 7.2) và `LoginPage` đã xây dựng trên lớp:

- Khi người dùng **chưa đăng nhập** cố truy cập 1 trang được bảo vệ (ví dụ gõ thẳng URL `/dashboard` vào trình duyệt), `ProtectedRoute` điều hướng sang `/login` **kèm theo thông tin trang định vào** (gợi ý: dùng `state` của `<Navigate to="/login" state={{ from: location.pathname }} />`, cần `useLocation` để lấy `location.pathname` hiện tại).
- Trên `LoginPage`, sau khi "đăng nhập" thành công (giả lập, giống bài học trên lớp), đọc lại thông tin `state.from` đó (qua `useLocation().state`) và dùng `useNavigate()` điều hướng thẳng về **đúng trang người dùng định vào ban đầu** — nếu không có thông tin đó (ví dụ người dùng vào thẳng `/login`), mặc định điều hướng về `/dashboard`.
- Test thử: đăng xuất (xóa token), gõ thẳng URL `/profile`, xác nhận bị đưa về `/login`; đăng nhập xong phải tự động về lại đúng `/profile`, không phải `/dashboard`.
