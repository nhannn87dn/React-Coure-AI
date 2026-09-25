# 📝 Bài Tập Về Nhà — Bài 10: Data Fetching Cơ Bản (Fetch API & Axios)

> Mục tiêu: Tích hợp API thật vào Mini Shop đã xây dựng từ Bài 8-9, và đóng gói logic gọi API thành Custom Hook để chuẩn bị so sánh với TanStack Query ở Bài 11.

---

## Bài 1 — Tích hợp API vào Mini Shop

**1. Yêu cầu:** Thay toàn bộ dữ liệu sản phẩm **tĩnh** trong Mini Shop (đã xây dựng ở Bài 8-9) bằng dữ liệu thật từ **DummyJSON** (`https://dummyjson.com/products`):

- Trang danh sách sản phẩm (`/products`): gọi API lấy danh sách, hiển thị đầy đủ 3 trạng thái Loading/Error/Success (mục 4.2 của bài học) và trạng thái rỗng khi không có sản phẩm nào khớp bộ lọc.
- Trang chi tiết sản phẩm (`/products/:id`): dùng `useParams` (Bài 8) lấy `id` từ URL, gọi API `https://dummyjson.com/products/{id}` để lấy đúng sản phẩm đó — xử lý riêng trường hợp `id` không tồn tại (API trả lỗi 404).
- Bộ lọc theo danh mục: DummyJSON có endpoint `https://dummyjson.com/products/category/{category}` — gọi endpoint tương ứng khi người dùng chọn 1 danh mục thay vì tự lọc thủ công trên mảng đã tải sẵn.
- Toàn bộ lời gọi API phải đi qua **1 Axios Instance chung** (mục 3.1), không gọi `axios.get()` trực tiếp rải rác nhiều nơi.

---

## Bài 2 — Custom Hook `useFetch<T>`

**1. Dựa vào kiến thức:** Custom Hook với Generic (Bài 7), `useEffect` + `AbortController` (mục 5.2), 3 trạng thái Loading/Error/Success (Phần 4).

**2. Yêu cầu:**

- Viết Custom Hook `useFetch<T>(url: string)` — nhận vào 1 URL, tự động gọi API bằng Axios Instance đã tạo, trả về object `{ data: T | null; loading: boolean; error: string | null }`.
- Bên trong phải dùng `AbortController` để hủy request cũ nếu `url` thay đổi trước khi request trước kịp hoàn thành (tương tự mục 5.2), tránh Race Condition.
- Áp dụng `useFetch<Product[]>(...)` cho trang danh sách sản phẩm và `useFetch<Product>(...)` cho trang chi tiết — cả 2 đều dùng chung 1 Custom Hook, chỉ khác kiểu Generic truyền vào.
- **Viết 1 đoạn nhận xét ngắn** (comment hoặc file `NOTES.md`): sau khi dùng `useFetch` ở nhiều nơi, bạn thấy nó còn thiếu gì so với nhu cầu thực tế? (Gợi ý để suy nghĩ: nếu 2 Component khác nhau cùng gọi `useFetch` với cùng 1 `url`, dữ liệu có được chia sẻ/cache lại không, hay mỗi Component tự gọi API riêng dù dữ liệu giống hệt nhau? Điều gì xảy ra nếu muốn tự động gọi lại API sau khi thêm/sửa/xóa dữ liệu ở 1 Component khác? Nhận xét này sẽ dùng để so sánh trực tiếp với TanStack Query ở Bài 11.)
