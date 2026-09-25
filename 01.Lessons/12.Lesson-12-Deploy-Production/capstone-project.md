# 🏁 Capstone Project — Dự Án Tổng Kết Khóa Học

> Làm tại nhà, nộp sau khi kết thúc khóa học. Tổng hợp **toàn bộ** kiến thức 12 buổi học. Bắt buộc gọi API thực tế và deploy lên Vercel. Chọn **1 trong 3 đề** dưới đây.

---

## Yêu cầu tối thiểu (áp dụng cho cả 3 đề)

- **Tối thiểu 4 trang** với React Router (Bài 8), bao gồm đầy đủ: 1 Nested Route (Layout chung + `Outlet`), 1 Dynamic Route (`:id`), 1 Protected Route, và 1 trang 404.
- Gọi API bằng **TanStack Query** (Bài 11) — có ít nhất **1 `useMutation`** (Create/Update/Delete thật, không chỉ đọc dữ liệu).
- **Global State bằng Zustand** (Bài 9) cho ít nhất 1 loại dữ liệu dùng chung nhiều nơi (giỏ hàng, trạng thái đăng nhập, danh sách yêu thích...).
- Có ít nhất **1 Form dùng Controlled Component với validate** (Bài 7).
- Có ít nhất **1 Custom Hook tự viết** (Bài 7) — không tính các hook có sẵn của thư viện ngoài.
- **TypeScript cho toàn bộ dự án, không dùng `any`** (nguyên tắc xuyên suốt từ Bài 1) — `pnpm run build` phải chạy thành công, không còn lỗi kiểu dữ liệu.
- **Deploy lên Vercel** (Bài 12): link demo hoạt động, không lỗi 404 khi refresh ở route con (đã cấu hình `vercel.json`).
- Có **README** trong Repository, hướng dẫn cách chạy dự án cục bộ (cài đặt, biến môi trường cần thiết, lệnh chạy).

> 💡 Xử lý đầy đủ trạng thái Loading/Error/rỗng ở mọi nơi có gọi API hoặc render danh sách (Bài 5, Bài 10-11) — đây là 1 phần của tiêu chí "Chức năng" bên dưới, không phải yêu cầu phụ.

---

## Đề 1 — E-commerce Mini

**Gợi ý cấu trúc dữ liệu:**

```ts
type Product = {
  id: number;
  title: string;
  price: number;
  category: string;
  thumbnail: string;
};

type CartItem = Product & { quantity: number };
```

**Chức năng cần có:**

- Trang danh sách sản phẩm: tải từ API thật (ví dụ DummyJSON hoặc API tự chọn), có tìm kiếm/lọc theo danh mục/phân trang — toàn bộ trạng thái này lưu qua **Query String** bằng `useSearchParams` (Bài 8), không dùng `useState` cục bộ, để giữ được khi refresh/chia sẻ link.
- Trang chi tiết sản phẩm theo `/products/:id` (Dynamic Route).
- Giỏ hàng dùng **Zustand Store** có `persist` (Bài 9) — giữ nguyên sau khi reload trang.
- Trang thanh toán (`/checkout`) là **Protected Route** — chỉ vào được sau khi đăng nhập.
- Trang Admin quản lý sản phẩm (tùy chọn, cộng điểm) với CRUD đầy đủ qua `useMutation` — đáp ứng đúng yêu cầu tối thiểu về `useMutation` nếu không làm trang thanh toán thật.

---

## Đề 2 — Task Manager (Kanban)

**Gợi ý cấu trúc dữ liệu:**

```ts
type Task = {
  id: number;
  title: string;
  status: 'todo' | 'in-progress' | 'done';
  assignee: string;
  dueDate: string; // "yyyy-mm-dd"
};
```

**Chức năng cần có:**

- Dựng backend giả bằng **json-server** hoặc MockAPI (Bài 11) — CRUD Task **thật**, có lưu lại dữ liệu.
- Giao diện Kanban: các cột theo `status` (`Todo` / `In Progress` / `Done`), mỗi Task hiển thị dưới dạng thẻ (card — liên hệ Composition Pattern Bài 3).
- Đổi trạng thái Task (kéo-thả nếu muốn nâng cao, hoặc đơn giản hơn: nút "Chuyển cột") — dùng `useMutation` + `invalidateQueries` (Bài 11) để đồng bộ ngay.
- Lọc Task theo `assignee` (người phụ trách) và theo `dueDate` (hạn chót) — lưu bộ lọc qua Query String (Bài 8).
- Trang chi tiết Task theo `/tasks/:id` (Dynamic Route), có Form sửa Task (Controlled Component + validate).
- Trang thống kê tổng số Task theo từng trạng thái — dùng **Derived State** (Bài 5), tính trực tiếp từ danh sách Task đã tải, không lưu số liệu vào 1 State riêng.

---

## Đề 3 — Movie Explorer

**Gợi ý cấu trúc dữ liệu:**

```ts
type Movie = {
  id: number;
  title: string;
  posterPath: string;
  releaseDate: string;
  overview: string;
};
```

**Chức năng cần có:**

- Tra cứu phim từ **TMDB API** (The Movie Database — cần đăng ký tài khoản miễn phí để lấy API key riêng, cấu hình qua biến môi trường `VITE_TMDB_API_KEY`, không hard-code trực tiếp trong code).
- Trang tìm kiếm phim: từ khóa tìm kiếm lưu qua Query String (Bài 8), kết hợp `useDebounce` (Bài 7) để tránh gọi API liên tục khi gõ.
- Trang chi tiết phim theo `/movies/:id` (Dynamic Route).
- Danh sách phim yêu thích dùng **Zustand Store** có `persist` (Bài 9).
- Lịch sử tìm kiếm: lưu 5-10 từ khóa gần nhất, cũng dùng Zustand có `persist` — mỗi từ khóa trong lịch sử là 1 `Link` (Bài 8) điều hướng thẳng về đúng kết quả tìm kiếm đó.
- Trang yêu thích (`/favorites`) là **Protected Route** — yêu cầu "đăng nhập" giả lập (giống Bài 8-9) trước khi xem.

---

## Tiêu chí chấm điểm (Rubric)

- **Chức năng — 40%**: đầy đủ yêu cầu tối thiểu, hoạt động ổn định, xử lý đúng trạng thái loading/error/rỗng ở mọi nơi cần thiết.
- **Chất lượng code — 30%**: cấu trúc thư mục rõ ràng, tách Component/Custom Hook hợp lý, TypeScript chặt chẽ, quản lý State đúng chỗ (local State — Bài 4, Lifting State Up — Bài 5, Global State — Bài 9, Server State — Bài 10-11).
- **UI/UX — 20%**: giao diện nhất quán, responsive (hiển thị tốt trên cả di động), phản hồi rõ ràng khi người dùng thao tác (disable nút khi đang xử lý, thông báo thành công/thất bại).
- **Deploy & tài liệu — 10%**: link demo hoạt động, không lỗi 404 khi refresh, README đầy đủ hướng dẫn chạy dự án, commit history rõ ràng theo từng giai đoạn phát triển (không phải 1 commit duy nhất chứa toàn bộ code).

## Nộp bài

Nộp theo hình thức và thời hạn giảng viên hướng dẫn (gửi link GitHub Repository và link demo Vercel).
