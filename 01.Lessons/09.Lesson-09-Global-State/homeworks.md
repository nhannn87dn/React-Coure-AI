# 📝 Bài Tập Về Nhà — Bài 9: Quản Lý Global State Với Zustand

> Mục tiêu: Xây dựng thêm 1 Store Zustand độc lập có `persist`, và biết phân biệt khi nào nên dùng Context thay vì Zustand.

---

## Bài 1 — Danh sách yêu thích (Wishlist) dùng Store riêng

**1. Dữ liệu ban đầu:** Tiếp tục dự án Mini Shop / giỏ hàng đã xây dựng trên lớp (mục 8.2 của bài học).

**2. Yêu cầu:**

- Tạo `useWishlistStore` (file `stores/useWishlistStore.ts`) — **độc lập** với `useCartStore`, không gộp chung 1 Store (đúng nguyên tắc mục 4.3 của bài học).
- State: `productIds: number[]` (danh sách ID sản phẩm đã yêu thích). Action: `toggleWishlist(id: number)` — nếu `id` đã có trong danh sách thì xóa đi, chưa có thì thêm vào (dùng đúng kỹ thuật Immutability đã học ở Bài 4, không mutate trực tiếp mảng).
- Áp dụng `persist` middleware (mục 6.2) để danh sách yêu thích được giữ nguyên sau khi reload trang.
- Trên mỗi `ProductCard`, thêm 1 icon trái tim — bấm vào để gọi `toggleWishlist`, đổi màu icon tùy theo sản phẩm đó đã có trong Wishlist hay chưa (dùng Selector Pattern, mục 5.2, để đọc đúng trạng thái của riêng sản phẩm này).
- Trên `Header`, hiển thị **số lượng sản phẩm** hiện có trong Wishlist (Derived State — tính từ `productIds.length`, không lưu vào 1 field State riêng).

---

## Bài 2 — Dark/Light Theme bằng Context API

**1. Yêu cầu:**

- Tạo `ThemeContext` (kiểu `{ theme: 'light' | 'dark'; toggleTheme: () => void }`) theo đúng cấu trúc đã học ở Phần 2 (`createContext`, `Provider`, Custom Hook `useTheme()` có kiểm tra `null`).
- Bọc `<ThemeContext.Provider>` quanh toàn bộ ứng dụng (ở `App.tsx` hoặc `main.tsx`).
- Thêm 1 nút chuyển đổi Theme trên `Header`, đổi `background-color`/`color` của toàn trang tương ứng (gợi ý: dùng `data-theme` attribute trên thẻ `<html>`/`<body>` kết hợp CSS, hoặc đổi `className` gốc).
- **Viết 1 đoạn giải thích ngắn (dưới dạng comment hoặc trong 1 file `NOTES.md`):** vì sao bài này chọn Context API thay vì tạo thêm 1 Zustand Store — liên hệ lại bảng so sánh ở mục 3.3 của bài học (tần suất thay đổi, có cần Selector Pattern hay không).
