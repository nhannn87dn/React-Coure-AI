# 📝 Bài Tập Về Nhà — Bài 7: Forms & Custom Hooks

> Mục tiêu: Xây dựng được Form nhiều trường có validate đầy đủ, và tự viết được Custom Hook Generic để tái sử dụng logic.

---

## Bài 1 — Form đăng ký nhiều trường với validate đầy đủ

**1. Dữ liệu ban đầu:**

```ts
type RegisterFormData = {
  fullName: string;
  email: string;
  password: string;
  confirmPassword: string;
  gender: 'male' | 'female' | 'other';
  agreeTerms: boolean;
};
```

**2. Yêu cầu:** Xây dựng Component `RegisterForm` với 6 trường trên (dùng **1 Object State** và **1 hàm `handleChange` chung**, đã học ở mục 2.4), validate khi submit:

- `fullName`: không được để trống.
- `email`: không được để trống và phải chứa ký tự `@`.
- `password`: tối thiểu 6 ký tự.
- `confirmPassword`: phải trùng khớp với `password`.
- `gender`: phải chọn 1 trong 3 giá trị (mặc định để trống, không cho submit nếu chưa chọn).
- `agreeTerms`: phải tick đồng ý điều khoản mới cho submit.
- Hiển thị **thông báo lỗi riêng cho từng trường** (không chỉ 1 thông báo lỗi chung chung), dùng 1 Object State thứ 2 dạng `Partial<Record<keyof RegisterFormData, string>>` để lưu lỗi theo từng field.
- Khi submit hợp lệ, hiển thị `"Đăng ký thành công!"` và log toàn bộ `form` ra console.

---

## Bài 2 — Custom Hook `useDebounce<T>`

**1. Dựa vào kiến thức:**

- Custom Hook với Generic (mục 5.2)
- `useEffect` + Cleanup Function (Bài 6) — dùng `setTimeout`/`clearTimeout`

**2. Yêu cầu:**

- Viết Custom Hook `useDebounce<T>(value: T, delay: number): T` — nhận vào 1 giá trị bất kỳ kiểu `T` và số mili-giây trễ, trả về **giá trị đã debounce**: chỉ cập nhật giá trị trả về sau khi `value` **ngừng thay đổi** trong khoảng `delay` mili-giây (gợi ý: bên trong dùng 1 `useState<T>` lưu giá trị debounce, 1 `useEffect` với `setTimeout` cập nhật State đó sau `delay`ms, và Cleanup Function `clearTimeout` mỗi khi `value` đổi trước khi hết `delay`).
- Áp dụng `useDebounce` cho 1 ô tìm kiếm `SearchBox`: gõ nhanh liên tục nhưng chỉ `console.log("Đang tìm:", debouncedKeyword)` sau khi người dùng **ngừng gõ** khoảng 500ms — không log ngay mỗi lần gõ 1 ký tự.
- Test thử: gõ liên tục 5 ký tự thật nhanh, xác nhận console chỉ log **đúng 1 lần** (với giá trị cuối cùng), không log 5 lần.

---

## Bài 3 — Uncontrolled Form với `useRef`

**1. Yêu cầu:** Xây dựng Component `FeedbackForm` gồm 2 trường `name` và `message`, dùng **Uncontrolled Component** (mục 2.2, mục 3.3) — mỗi trường 1 `useRef<HTMLInputElement>`/`useRef<HTMLTextAreaElement>`, không dùng `useState` cho giá trị input.

- Khi submit, đọc giá trị qua `.current?.value`, validate không được để trống (nếu để trống, dùng `.current?.focus()` để tự động focus lại vào đúng ô bị thiếu).
- Sau khi submit thành công, dùng ref để **xóa trắng** cả 2 ô input (gợi ý: gán lại `.current.value = ''`).
- Giải thích ngắn gọn (viết dưới dạng comment trong code): vì sao cách làm này **không cần** `useState` mà vẫn lấy được giá trị input.
