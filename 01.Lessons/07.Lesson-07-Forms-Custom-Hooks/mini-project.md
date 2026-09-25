# 🧩 Mini Project — Ứng Dụng Tổng Hợp Bài 1-7

> Làm tại nhà, không giới hạn thời gian trong buổi học. Tổng hợp toàn bộ kiến thức từ Bài 1 đến Bài 7. Chọn **1 trong 3 đề** dưới đây.

---

## Yêu cầu tối thiểu (áp dụng cho cả 3 đề)

- Tối thiểu **5 Component**, Props và State có kiểu TypeScript đầy đủ, không dùng `any` (Bài 1, Bài 3, Bài 4).
- Có **Lifting State Up** (Bài 5): State dùng chung giữa nhiều Component đặt ở Component cha gần nhất, truyền xuống qua Props + callback — không có 2 Component nào tự giữ 2 bản sao lệch nhau của cùng 1 dữ liệu.
- Render danh sách bằng `map()` với `key` là **ID duy nhất**, không dùng `index` (Bài 5).
- Có ít nhất **1 Derived State** (Bài 5): 1 giá trị tính trực tiếp từ State/Props đã có, không lưu vào `useState` riêng.
- Có ít nhất **1 `useEffect` với Cleanup Function đúng cách** (Bài 6): dùng cho Timer, Event Listener, hoặc tác vụ tương tự — không viết Effect chỉ để đồng bộ 1 State sang 1 State khác (đó là Derived State, không phải Effect).
- Có ít nhất **1 Form dùng Controlled Component với validate cơ bản** (Bài 7): kiểm tra rỗng/độ dài trước khi submit, hiển thị thông báo lỗi rõ ràng.
- Có ít nhất **1 Custom Hook tự viết** (Bài 7): tách phần logic State + Effect lặp lại ra khỏi Component, đặt tên bắt đầu bằng `use`.
- Xử lý trạng thái rỗng (ví dụ "Chưa có dữ liệu") ở nơi phù hợp (Bài 5).
- Cập nhật State đúng nguyên tắc Immutability (Bài 4) — không mutate trực tiếp Object/Array State.

> 💡 Giao diện không cần cầu kỳ, nhưng nên dùng CSS Module hoặc Tailwind (đã giới thiệu ở Bài 2) để dễ nhìn và dễ thao tác khi demo.

---

## Đề 1 — Ứng dụng Quản lý chi tiêu cá nhân

**Gợi ý cấu trúc dữ liệu:**

```ts
type Expense = {
  id: number;
  title: string;
  amount: number;
  category: 'an-uong' | 'di-lai' | 'giai-tri' | 'hoa-don' | 'khac';
  date: string; // định dạng "yyyy-mm-dd"
};
```

**Chức năng cần có:**

- Form thêm khoản chi mới (`title`, `amount`, `category`, `date`) — Controlled Component, validate `title` không rỗng và `amount` phải lớn hơn 0 trước khi submit (đáp ứng yêu cầu Form + validate của đề này).
- Component Form và Component Danh sách là 2 Component riêng, chia sẻ State qua Lifting State Up.
- Sửa và xóa 1 khoản chi đã thêm.
- Lọc danh sách theo `category` và theo tháng (dựa vào `date`).
- Hiển thị **tổng chi tiêu** của danh sách **đã lọc** — đây là Derived State bắt buộc.
- Viết Custom Hook `useLocalStorage<Expense[]>` (đã học ở Bài 7) để tự động lưu và khôi phục danh sách chi tiêu từ `localStorage` — đáp ứng đồng thời yêu cầu Custom Hook.
- Gợi ý cho yêu cầu `useEffect` + Cleanup: hiển thị đồng hồ giờ hiện tại ở góc trang bằng `setInterval` + Cleanup (Bài 6), hoặc tự động ẩn thông báo "Đã lưu thành công" sau 2 giây bằng `setTimeout` + Cleanup.

---

## Đề 2 — Ứng dụng Quiz trắc nghiệm

**Gợi ý cấu trúc dữ liệu:**

```ts
type Question = {
  id: number;
  question: string;
  options: string[];
  correctIndex: number; // vị trí đáp án đúng trong "options"
};
```

**Chức năng cần có:**

- Bộ câu hỏi khai báo sẵn dưới dạng 1 mảng `Question[]` trong code (chưa cần gọi API thật — Data Fetching sẽ học ở Bài 10-11).
- Màn hình nhập tên người chơi trước khi bắt đầu: Controlled Input, validate không được để trống — đáp ứng yêu cầu Form + validate.
- Đếm ngược thời gian cho mỗi câu (ví dụ 15 giây). Tách toàn bộ logic đếm ngược (State thời gian còn lại + `useEffect`/`setInterval`/Cleanup) thành Custom Hook riêng, ví dụ `useCountdown(seconds: number)` trả về thời gian còn lại và hàm reset — đáp ứng đồng thời cả yêu cầu `useEffect`+Cleanup và Custom Hook.
- Hiển thị câu hỏi hiện tại, cho phép chọn 1 đáp án, chuyển sang câu tiếp theo.
- Nếu hết giờ mà chưa chọn đáp án, tự động tính là sai và chuyển sang câu kế tiếp.
- Sau khi hết bộ câu hỏi, hiển thị màn hình kết quả: số câu đúng / tổng số câu — đây là Derived State bắt buộc (tính từ mảng câu trả lời đã lưu, không lưu điểm số vào 1 `useState` riêng rồi tự cộng dồn thủ công).

---

## Đề 3 — Pomodoro & Task Tracker

**Gợi ý cấu trúc dữ liệu:**

```ts
type Task = {
  id: number;
  text: string;
  completedSessions: number; // số phiên Pomodoro đã hoàn thành cho việc này
};
```

**Chức năng cần có:**

- Danh sách công việc: Form thêm việc mới là Controlled Component, validate nội dung công việc không được để trống (đáp ứng yêu cầu Form + validate), xóa việc, chọn 1 việc để "tập trung" (focus).
- Tách toàn bộ logic đồng hồ Pomodoro (đếm ngược, Start/Pause/Reset, `useEffect`+`setInterval`+Cleanup) thành Custom Hook riêng, ví dụ `usePomodoroTimer(initialMinutes: number)` — đáp ứng đồng thời cả yêu cầu `useEffect`+Cleanup và Custom Hook.
- Khi đồng hồ đếm hết 1 phiên làm việc, tăng `completedSessions` của việc đang được chọn lên 1 — đây là ví dụ cụ thể của Lifting State Up: đồng hồ Pomodoro và danh sách công việc là 2 Component khác nhau, cùng chia sẻ State ở Component cha.
- Hiển thị **tổng số phiên đã hoàn thành** của tất cả công việc — Derived State bắt buộc, tính bằng cách cộng dồn `completedSessions` của toàn bộ `tasks`.

---

## Tiêu chí chấm điểm

- **Chức năng hoạt động đúng yêu cầu — 50%**
- **Chất lượng code: tách Component/Custom Hook hợp lý, TypeScript đúng, không mutate State — 30%**
- **Giao diện rõ ràng, có xử lý trạng thái rỗng/đang tải — 20%**

## Nộp bài

Nộp theo hình thức và thời hạn giảng viên hướng dẫn trên lớp (ví dụ: nén thư mục dự án hoặc gửi link GitHub Repository nếu đã biết dùng Git).
