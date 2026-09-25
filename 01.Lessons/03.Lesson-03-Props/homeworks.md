# 📝 Bài Tập Về Nhà — Bài 3: JSX Nâng Cao & Props

> Mục tiêu: Xây dựng được bộ Component UI dùng chung có Props kiểu TypeScript đầy đủ, áp dụng đúng `children` và Composition Pattern, tái sử dụng Component với nhiều bộ dữ liệu khác nhau.

---

## Bài 1 — Component `Button` nhiều variant

**Yêu cầu:** Viết Component `Button` với:

```ts
type ButtonProps = {
  label: string;
  variant?: 'primary' | 'secondary' | 'danger';
  disabled?: boolean;
  onClick?: () => void;
};
```

- `variant` mặc định là `'primary'` nếu không truyền.
- Style khác nhau theo từng `variant` (màu nền khác nhau) bằng CSS Module.
- Khi `disabled` là `true`, thêm thuộc tính `disabled` cho thẻ `<button>` và giảm độ trong suốt (`opacity`).
- Test thử component với ít nhất 4 cách gọi khác nhau (đủ 3 variant + 1 trường hợp `disabled`).

---

## Bài 2 — Component `Badge` hiển thị trạng thái

**Yêu cầu:** Viết Component `Badge` nhận Props:

```ts
type BadgeProps = {
  status: 'in-stock' | 'out-of-stock' | 'coming-soon';
};
```

- Hiển thị chữ tương ứng: `"Còn hàng"` / `"Hết hàng"` / `"Sắp ra mắt"`.
- Mỗi trạng thái có 1 màu nền riêng.
- Thử gọi `<Badge status="pending" />` (giá trị không nằm trong Union Type) và ghi lại thông báo lỗi TypeScript hiển thị trong VS Code.

---

## Bài 3 — Component `Card` với `children` và Composition Pattern

**Yêu cầu:** Dựa trên Component `Card` đã xây dựng trên lớp (Phần 6), bổ sung:

- Thêm Props `children?: React.ReactNode` (đã có sẵn) và render `Button` (Bài 1) + `Badge` (Bài 2) bên trong `children` khi gọi `Card` — **không** sửa lại `CardProps` để thêm riêng `buttonLabel`/`badgeStatus`. Đây chính là ứng dụng của Composition Pattern đã học ở mục 5.3: `Card` không cần biết trước bên trong nó sẽ hiển thị `Button` hay `Badge` hay bất kỳ nội dung gì khác.

---

## Bài 4 — Trang danh sách sản phẩm tĩnh, tái sử dụng Card

**1. Dữ liệu ban đầu:**

```ts
type Product = {
  id: number;
  name: string;
  image: string;
  price: number;
  status: 'in-stock' | 'out-of-stock' | 'coming-soon';
};

const products: Product[] = [
  { id: 1, name: 'Áo thun basic', image: '/images/tshirt.jpg', price: 150000, status: 'in-stock' },
  { id: 2, name: 'Quần jean slimfit', image: '/images/jean.jpg', price: 350000, status: 'out-of-stock' },
  { id: 3, name: 'Mũ lưỡi trai', image: '/images/cap.jpg', price: 120000, status: 'coming-soon' },
];
```

**2. Yêu cầu:**

- Dùng `map()` (đã ôn ở Bài 1, học render danh sách chính thức ở Bài 5 — ở bài này cứ dùng tạm, chưa cần lo về `key` tối ưu) để render 1 `Card` cho mỗi sản phẩm trong mảng `products`.
- Mỗi `Card` hiển thị `Badge` đúng theo `status` và `Button` với label `"Thêm vào giỏ"` — disable `Button` nếu `status` là `'out-of-stock'` hoặc `'coming-soon'`.
- Toàn bộ Props của `Card`, `Badge`, `Button` phải đúng kiểu đã khai báo, không dùng `any`, không còn lỗi gạch đỏ trong VS Code.
