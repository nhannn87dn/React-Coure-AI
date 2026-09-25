# 📝 Bài Tập Về Nhà — Bài 2: Component & JSX

> Mục tiêu: Áp dụng đúng các quy tắc lý thuyết đã học (định nghĩa Component, quy tắc JSX, nhúng biểu thức `{}`, cấu trúc dự án).

---

## Bài 1 — Viết đúng 1 Component chuẩn

**Yêu cầu:** Viết Component tên `ProfileCard`, hiển thị tĩnh (chưa cần Props) thông tin:
- Tên: "Trần Thị Bình"
- Chức danh: "UI/UX Designer"
- 1 dòng mô tả ngắn bất kỳ

Component phải tuân thủ đúng **toàn bộ 3 quy tắc** đã học ở Phần 1 (đặt tên, return JSX, cấu trúc hàm), dùng `className` thay vì `class`, và chỉ được có **1 root element**.

---

## Bài 2 — Sửa lỗi trong đoạn code có sẵn

**Yêu cầu:** Đoạn code dưới đây có **4 lỗi** liên quan đến JSX. Hãy viết lại đoạn code cho đúng, không thay đổi nội dung hiển thị.

```tsx
function EventCard() {
  const title = 'Buổi Workshop React';
  const seats = 30;

  return (
    <h1>title</h1>
    <div class="event-card">
      <img src="/event.png">
      <p>Số chỗ còn lại: seats</p>
    </div>
  );
}
```

---

## Bài 3 — Chuyển đổi HTML tĩnh sang JSX hợp lệ

**Yêu cầu:** Bạn được giao đoạn HTML tĩnh dưới đây (copy từ 1 file thiết kế cũ). Hãy chuyển nó thành 1 Component React tên `PricingBox` hợp lệ về mặt JSX.

```html
<div class="pricing-box">
  <h3>Gói Pro</h3>
  <input type="text" placeholder="Nhập mã giảm giá">
  <hr>
  <button>Đăng ký ngay</button>
</div>
```

---

## Bài 4 — Ghép Component cha - con qua nhiều file

**Yêu cầu:** Tạo cấu trúc thư mục thật (trong dự án Vite của bạn) gồm:
```text
src/components/
├── Header.tsx
└── StatBox.tsx
```

- `StatBox.tsx`: hiển thị 1 con số thống kê tĩnh bất kỳ (ví dụ "1.200+ học viên").
- `Header.tsx`: chứa tiêu đề trang, và **render 2 lần** `StatBox` bên trong (2 con số khác nhau).
- Ghép `Header` vào `App.tsx`, chạy `pnpm run dev` để kiểm tra hiển thị đúng cả 2 `StatBox`.


---

## Bài 5 — Nhúng biểu thức JS kết hợp kiến thức ESNext (Bài 1)

**Yêu cầu:** Viết Component `OrderSummary` với dữ liệu tĩnh sau:
```ts
const items = ['Áo thun', 'Quần jean', 'Mũ lưỡi trai'];
const total = 620000;
```
Component phải hiển thị:
1. Danh sách sản phẩm nối bằng dấu phẩy, viết trên **1 dòng** (gợi ý: dùng `join()` đã ôn ở Bài 1).
2. Tổng tiền hiển thị dạng `"620.000đ"` bằng cách dùng **Template Literal** kết hợp `{}`.
3. Số lượng sản phẩm (dùng `items.length`).
