# 📝 Bài Tập Về Nhà — Bài 11: Data Fetching Nâng Cao Với TanStack Query

> Mục tiêu: Chuyển toàn bộ Mini Shop sang dùng TanStack Query, và xây dựng thêm 1 trang quản trị (Admin) có CRUD đầy đủ.

---

## Bài 1 — Chuyển Mini Shop sang TanStack Query

**1. Yêu cầu:** Trong dự án Mini Shop (đã tích hợp API thật ở Bài 10), thay toàn bộ phần gọi API thủ công (`useEffect` + `useState` hoặc Custom Hook `useFetch<T>`) bằng TanStack Query:

- Trang danh sách sản phẩm: chuyển sang `useProducts()` bằng `useQuery` (mục 3.4 của bài học).
- Trang chi tiết sản phẩm: chuyển sang `useProduct(id)` bằng `useQuery`, Query Key phải bao gồm `id` (ví dụ `['products', id]`) để mỗi sản phẩm có Cache riêng.
- Xóa hoàn toàn Custom Hook `useFetch<T>` cũ (Bài 10) khỏi dự án — không dùng song song 2 cách.
- Cấu hình `QueryClientProvider` + React Query Devtools ở `main.tsx` (Phần 2 của bài học).
- **So sánh (viết ngắn gọn trong `NOTES.md` hoặc comment):** đếm số dòng code của phần gọi API trang danh sách sản phẩm — trước (dùng `useFetch`) và sau (dùng `useQuery`) khi chuyển đổi.

---

## Bài 2 — Trang quản lý sản phẩm (Admin) với CRUD đầy đủ

**1. Chuẩn bị:** Dựng `json-server` (mục 7.1 của bài học) với resource `products`, có tối thiểu các field `id`, `title`, `price`, `category`.

**2. Yêu cầu:** Xây dựng trang `/admin/products` (có thể là Protected Route — đã học ở Bài 8-9, không bắt buộc nhưng khuyến khích):

- **Read:** Hiển thị bảng/danh sách toàn bộ sản phẩm bằng `useQuery`, đầy đủ trạng thái `isPending`/`isError`.
- **Create:** Form thêm sản phẩm mới (Controlled Component + validate cơ bản — đã học ở Bài 7), dùng `useMutation` gọi `POST`, `invalidateQueries` sau khi thành công để danh sách tự cập nhật.
- **Update:** Mỗi dòng sản phẩm có nút "Sửa" mở Form chỉnh sửa (có thể dùng chung Component Form với Create), dùng `useMutation` gọi `PATCH`/`PUT`, `invalidateQueries` sau khi thành công.
- **Delete:** Nút "Xóa" cho từng sản phẩm, dùng `useMutation` gọi `DELETE`, `invalidateQueries` sau khi thành công. Thêm bước xác nhận trước khi xóa (ví dụ `window.confirm(...)`).
- Với cả 3 Mutation (Create/Update/Delete): disable nút tương ứng và hiển thị rõ trạng thái "Đang xử lý..." trong lúc `isPending` là `true` (mục 4.3, 7.3 của bài học) — không cho phép bấm nút đó thêm lần nữa trong lúc đang xử lý.
