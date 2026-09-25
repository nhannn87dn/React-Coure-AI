# ⭐ Bài 13 (Bonus): Tailwind CSS — Style Nhanh Theo Hướng Utility-First

> 🎯 Mục tiêu: Tự cài đặt được Tailwind CSS v4 vào dự án Vite + React, dựng được giao diện responsive bằng utility class, xử lý được trạng thái tương tác và dark mode, và tổ chức được class có điều kiện gọn gàng bằng `clsx` + `tailwind-merge`.
>
> 📖 Bài này là tài liệu **tự học**, không dạy trên lớp và không có bài tập/homework nộp. Đọc tuần tự từng phần, gõ lại các ví dụ trong 1 dự án Vite + React + TypeScript có sẵn (từ Bài 1) để thấy kết quả trực tiếp.
>
> 🔁 Kiến thức cần nắm trước: Global CSS và CSS Module (Bài 2), phần giới thiệu Tailwind ở mục 4.4.3 (Bài 2), Props và `children` (Bài 3), Conditional Rendering (Bài 5).

---

## Phần 1: Utility-First Là Gì — Vì Sao Tailwind Phổ Biến

### 1.0 Nhắc lại nhanh

Ở Bài 2 (mục 4.4), ta đã học 2 cách style: **Global CSS** (1 file `.css` dùng chung) và **CSS Module** (mỗi Component 1 file `.module.css` riêng, tránh xung đột tên class). Cả 2 cách đều theo cùng 1 tư duy: **đặt tên class, rồi viết CSS riêng cho class đó**. Tailwind đi theo hướng hoàn toàn khác.

### 1.1 So sánh cùng 1 Component viết bằng CSS Module và bằng utility class

```tsx
// Cách CSS Module (Bài 2)
// Card.module.css
// .card { border: 1px solid #ddd; border-radius: 8px; padding: 16px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }

import styles from './Card.module.css';

function Card({ title, children }: { title: string; children: React.ReactNode }) {
  return (
    <div className={styles.card}>
      <h3>{title}</h3>
      {children}
    </div>
  );
}
```

```tsx
// Cách Tailwind - KHÔNG cần file .css riêng, KHÔNG cần đặt tên class
function Card({ title, children }: { title: string; children: React.ReactNode }) {
  return (
    <div className="border border-gray-200 rounded-lg p-4 shadow-sm">
      <h3>{title}</h3>
      {children}
    </div>
  );
}
```

> **Utility-First nghĩa là: thay vì đặt 1 cái tên (`card`) rồi định nghĩa CSS cho cái tên đó, bạn ghép nhiều class nhỏ có sẵn (mỗi class ứng với ĐÚNG 1 khai báo CSS) ngay trong `className` để tạo ra giao diện.** `border` = có viền, `rounded-lg` = bo góc lớn, `p-4` = padding 16px, `shadow-sm` = đổ bóng nhẹ — không có class nào tên là "card" cả.

### 1.2 Ưu điểm và nhược điểm

**Ưu điểm:**
- Không cần đặt tên class (`card`, `card-header`, `card-title-active`...) — việc đặt tên vốn luôn khó và tốn thời gian.
- Không cần chuyển qua lại giữa file `.tsx` và file `.css` — style viết ngay tại chỗ đang code JSX.
- File CSS build ra **rất nhỏ**: Tailwind quét toàn bộ code, chỉ sinh ra đúng những class thực sự được dùng, không sinh thừa.

**Nhược điểm:**
- `className` có thể rất dài với Component phức tạp.
- Cần thời gian làm quen với tên các class (`p-4`, `gap-2`, `items-center`...).

### 1.3 Khi nào nên và không nên dùng

Tailwind phù hợp với hầu hết dự án React thực tế hiện nay — đây cũng là lý do nó được giới thiệu ở Bài 2 và khuyến khích dùng cho Mini Project/Capstone. Với dự án rất nhỏ (1-2 trang tĩnh) thì Global CSS đơn giản vẫn đủ dùng, không nhất thiết phải cài thêm Tailwind.

---

## Phần 2: Cài Đặt Tailwind CSS v4 Với Vite

### 2.1 Cài `tailwindcss` + `@tailwindcss/vite`

```bash
pnpm add tailwindcss @tailwindcss/vite
```

```ts
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  plugins: [react(), tailwindcss()],
});
```

### 2.2 Thêm `@import "tailwindcss";` vào file CSS gốc

```css
/* src/index.css */
@import "tailwindcss";
```

```tsx
// main.tsx
import './index.css'; // Import CSS gốc - đã học ở Bài 2 (Global CSS)
```

> 💡 **Từ Tailwind v4, không còn bắt buộc file `tailwind.config.js`** như các phiên bản trước — plugin `@tailwindcss/vite` tự động quét toàn bộ file trong dự án để tìm class đang được dùng. Việc tùy biến theme (nếu cần) chuyển sang viết trực tiếp trong CSS bằng `@theme` (Phần 6).

### 2.3 Công cụ hỗ trợ

- Extension **Tailwind CSS IntelliSense** (VS Code, tương tự các extension đã cài ở Bài 1): gợi ý tên class, xem trước màu sắc ngay trong editor.
- **`prettier-plugin-tailwindcss`**: tự động sắp xếp lại thứ tự class trong `className` theo 1 quy ước chuẩn mỗi khi format (Prettier đã dùng từ Bài 1).

---

## Phần 3: Nhóm Utility Class Cốt Lõi

### 3.1 Spacing và kích thước

```tsx
<div className="p-4 m-2 w-64 h-32 gap-2">...</div>
```

- `p-4`: padding tất cả các cạnh = `4 × 0.25rem = 1rem` (16px). Số sau dấu gạch nối đi theo **thang đo mặc định** của Tailwind (0, 1, 2, 4, 6, 8...), không phải số px trực tiếp.
- `m-2`: margin tất cả các cạnh. `w-64`/`h-32`: chiều rộng/cao cố định. `gap-2`: khoảng cách giữa các phần tử con trong `flex`/`grid` (Phần 3.3).

### 3.2 Chữ, màu, viền, bóng

```tsx
<p className="text-lg font-bold text-slate-700 bg-slate-100 border border-slate-300 rounded-md shadow-sm">
  Xin chào
</p>
```

`text-lg` (cỡ chữ), `font-bold` (đậm), `text-slate-700`/`bg-slate-100` (màu chữ/nền theo thang màu có sẵn), `border`/`rounded-md`/`shadow-sm` (viền/bo góc/đổ bóng).

### 3.3 Bố cục với Flexbox và Grid

```tsx
// Flexbox - căn giữa theo chiều dọc, dàn đều theo chiều ngang
<div className="flex items-center justify-between">
  <span>Trái</span>
  <span>Phải</span>
</div>

// Grid - lưới 3 cột đều nhau
<div className="grid grid-cols-3 gap-4">
  <div>1</div>
  <div>2</div>
  <div>3</div>
</div>
```

Class `flex`/`grid` bật chế độ layout tương ứng cho phần tử cha (giống thuộc tính CSS `display: flex`/`display: grid`), các class đi kèm (`items-center`, `justify-between`, `grid-cols-3`) điều chỉnh cách các phần tử con sắp xếp bên trong.

### 3.4 Giá trị tùy ý (arbitrary values)

```tsx
<div className="w-[137px] bg-[#1da1f2]">...</div>
```

Cặp ngoặc vuông `[...]` cho phép chỉ định **1 giá trị bất kỳ**, không nằm trong thang đo mặc định — dùng khi cần khớp chính xác 1 con số cụ thể (ví dụ theo thiết kế Figma). Chỉ nên dùng khi thực sự cần thiết; ưu tiên các giá trị có sẵn (`w-32`, `w-64`...) để giao diện nhất quán giữa các Component.

---

## Phần 4: Responsive Design Theo Hướng Mobile-First

### 4.1 Tiền tố breakpoint

```tsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
  {/* ... */}
</div>
```

> **Tailwind theo triết lý Mobile-First: class KHÔNG có tiền tố (`grid-cols-1`) áp dụng cho MỌI kích thước màn hình, bao gồm cả màn hình nhỏ nhất. Tiền tố (`md:`, `lg:`...) chỉ ghi đè kể từ độ rộng màn hình tương ứng TRỞ LÊN.**

Ví dụ trên: lưới sản phẩm hiển thị **1 cột** trên điện thoại (mặc định), chuyển sang **2 cột** khi màn hình đạt ngưỡng `md` (768px) trở lên, và **4 cột** khi đạt ngưỡng `lg` (1024px) trở lên.

### 4.2 Lỗi thường gặp: hiểu nhầm `sm:` là "chỉ áp dụng cho màn hình nhỏ"

```tsx
// ❌ Hiểu sai - nghĩ rằng class này CHỈ áp dụng khi màn hình nhỏ
<p className="sm:text-red-500">Văn bản</p>
```

`sm:text-red-500` áp dụng khi màn hình đạt ngưỡng `sm` (640px) **trở lên** — nghĩa là áp dụng cho **cả** màn hình `sm`, `md`, `lg`... chứ không phải chỉ riêng màn hình nhỏ. Nếu muốn 1 style chỉ áp dụng cho màn hình nhỏ nhất (không tiền tố nào), hãy đặt nó **không có tiền tố**, rồi dùng tiền tố lớn hơn để **ghi đè** khi màn hình to lên — đúng như ví dụ `grid-cols-1 md:grid-cols-2` ở mục 4.1.

---

## Phần 5: Trạng Thái Tương Tác Và Dark Mode

### 5.1 Variant `hover:`, `focus:`, `active:`, `disabled:`

```tsx
<button className="bg-blue-500 hover:bg-blue-600 active:bg-blue-700 disabled:opacity-50 disabled:cursor-not-allowed">
  Gửi
</button>
```

Mỗi variant chỉ áp dụng style tương ứng đúng lúc trạng thái đó xảy ra (di chuột vào, đang nhấn giữ, bị disable) — không cần viết `:hover`/`:disabled` trong 1 file CSS riêng như cách làm CSS thuần.

### 5.2 `group` và `peer`

```tsx
// group - style phần tử CON theo trạng thái của phần tử CHA
<div className="group border p-4 hover:bg-gray-50">
  <h3 className="group-hover:text-blue-600">Tiêu đề</h3>
</div>

// peer - style phần tử theo trạng thái của phần tử ANH EM đứng trước nó
<input type="checkbox" className="peer" />
<span className="peer-checked:text-green-600">Đã đồng ý</span>
```

`group` đặt ở phần tử cha, `group-hover:` ở phần tử con — cho phép đổi style con khi hover vào **cha**, chứ không phải hover trực tiếp vào con. `peer` tương tự nhưng dùng cho quan hệ **anh em kề nhau** (ví dụ đổi màu label khi checkbox đứng trước nó được tick).

### 5.3 Dark mode với `dark:`

```tsx
<div className="bg-white text-slate-900 dark:bg-slate-900 dark:text-white">
  Nội dung
</div>
```

Mặc định, `dark:` áp dụng theo **cài đặt hệ điều hành** của người dùng (`prefers-color-scheme`). Muốn cho người dùng **tự bật/tắt thủ công** (không phụ thuộc hệ điều hành), khai báo thêm 1 custom variant dựa trên class `.dark`:

```css
/* index.css */
@import "tailwindcss";
@custom-variant dark (&:where(.dark, .dark *));
```

```tsx
// Toggle theme - lưu lựa chọn bằng Custom Hook useLocalStorage đã viết ở Bài 7
function ThemeToggle() {
  const [isDark, setIsDark] = useLocalStorage<boolean>('dark-mode', false);

  useEffect(() => {
    document.documentElement.classList.toggle('dark', isDark);
  }, [isDark]);

  return <button onClick={() => setIsDark(!isDark)}>{isDark ? '☀️' : '🌙'}</button>;
}
```

Sau khi có `@custom-variant`, class `dark:` chỉ kích hoạt khi thẻ `<html>` (hoặc 1 cha nào đó) có class `.dark` — và ta tự thêm/gỡ class đó bằng JavaScript (`classList.toggle`) dựa theo State, đồng bộ với `useLocalStorage` (Bài 7) để giữ lựa chọn qua các lần tải lại trang.

---

## Phần 6: Tùy Biến Theme Với `@theme`

### 6.1 Khai báo design token bằng CSS variable

```css
/* index.css */
@import "tailwindcss";

@theme {
  --color-brand-500: #6366f1;
  --font-display: 'Be Vietnam Pro', sans-serif;
  --breakpoint-3xl: 1920px;
}
```

`@theme` là cách khai báo mới của v4 để tùy biến design token (màu thương hiệu, font chữ, breakpoint riêng) — thay thế cho `tailwind.config.js` của các phiên bản trước.

### 6.2 Dùng class sinh ra từ theme tùy biến

```tsx
<button className="bg-brand-500 font-display">Đăng ký</button>
```

Khai báo `--color-brand-500` trong `@theme` tự động sinh ra các class dùng được ngay (`bg-brand-500`, `text-brand-500`, `border-brand-500`...) — giống hệt cách các màu có sẵn (`bg-blue-500`) hoạt động.

---

## Phần 7: Tổ Chức Class Trong Component React

### 7.1 Class có điều kiện: template literal so với `clsx`

```tsx
// Template literal (đã ôn ở Bài 1) - dùng được cho trường hợp đơn giản
function Badge({ isActive }: { isActive: boolean }) {
  return <span className={`px-2 py-1 rounded ${isActive ? 'bg-green-500' : 'bg-gray-300'}`}>...</span>;
}
```

```bash
pnpm add clsx
```

```tsx
import clsx from 'clsx';

// clsx - dễ đọc hơn khi có NHIỀU điều kiện cùng lúc
function Badge({ isActive, isPending }: { isActive: boolean; isPending: boolean }) {
  return (
    <span
      className={clsx('px-2 py-1 rounded', {
        'bg-green-500': isActive,
        'bg-yellow-400': isPending,
        'bg-gray-300': !isActive && !isPending,
      })}
    >
      ...
    </span>
  );
}
```

### 7.2 Xung đột class khi Component nhận thêm `className` từ bên ngoài

```tsx
// ❌ Vấn đề - nếu Component cha truyền className="bg-red-500", nó bị nối vào SAU
// "bg-blue-500 bg-red-500" - CSS không có quy tắc chắc chắn class nào thắng
function Button({ className }: { className?: string }) {
  return <button className={`bg-blue-500 px-4 py-2 ${className}`}>Nhấn</button>;
}
```

```bash
pnpm add tailwind-merge
```

```ts
// lib/cn.ts - helper gộp clsx + tailwind-merge, dùng xuyên suốt dự án
import { clsx, type ClassValue } from 'clsx';
import { twMerge } from 'tailwind-merge';

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

```tsx
// ✅ Đúng - twMerge loại bỏ đúng class Tailwind bị trùng nhóm, giữ lại class truyền vào SAU CÙNG
function Button({ className }: { className?: string }) {
  return <button className={cn('bg-blue-500 px-4 py-2', className)}>Nhấn</button>;
}

<Button className="bg-red-500" /> // Kết quả: chỉ còn "bg-red-500 px-4 py-2" - không còn bg-blue-500
```

`twMerge` hiểu được **ngữ nghĩa** của class Tailwind (biết `bg-blue-500` và `bg-red-500` cùng thuộc nhóm "background color"), nên tự động loại bỏ class cũ bị ghi đè — khác với việc chỉ nối chuỗi đơn thuần.

### 7.3 Tách Component tái sử dụng thay vì lạm dụng `@apply`

```css
/* ❌ Không khuyến khích - @apply đưa utility class ngược lại vào 1 class CSS, mất chính lợi ích của utility-first */
.btn-primary {
  @apply bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600;
}
```

```tsx
// ✅ Đúng - tách thành Component React tái sử dụng (đúng tinh thần Bài 3), không cần @apply
type ButtonProps = {
  variant?: 'primary' | 'secondary';
  children: React.ReactNode;
};

function Button({ variant = 'primary', children }: ButtonProps) {
  return (
    <button
      className={cn(
        'px-4 py-2 rounded-md font-medium',
        variant === 'primary' && 'bg-blue-500 text-white hover:bg-blue-600',
        variant === 'secondary' && 'bg-gray-200 text-gray-900 hover:bg-gray-300'
      )}
    >
      {children}
    </button>
  );
}
```

Với React, cách tự nhiên để tái sử dụng style là **tách thành Component** (đã học từ Bài 2-3) — không cần cơ chế riêng của CSS như `@apply`. Với dự án lớn hơn, có thể tìm hiểu thêm thư viện **`class-variance-authority` (cva)** — giúp định nghĩa nhiều `variant`/`size` cho 1 Component 1 cách có cấu trúc, gọn hơn nhiều điều kiện `cn(...)` lồng nhau.

---

## Phần 8: Ví Dụ Tổng Hợp — Dựng Lại Trang Danh Sách Sản Phẩm Bằng Tailwind

Phần này áp dụng lại đúng bài `ProductListPage`/`SearchBox`/`CategoryFilter` đã xây dựng ở homework Bài 5 (lúc đó dùng CSS Module), chuyển hoàn toàn sang Tailwind.

### 8.1 `ProductCard` — chuyển từ CSS Module sang Tailwind

```tsx
type Product = { id: number; name: string; price: number; image: string };

function ProductCard({ product }: { product: Product }) {
  return (
    <div className="border border-gray-200 rounded-lg overflow-hidden shadow-sm hover:shadow-md transition-shadow dark:border-gray-700 dark:bg-gray-800">
      <img src={product.image} alt={product.name} className="w-full h-40 object-cover" />
      <div className="p-4">
        <h3 className="font-semibold text-slate-800 dark:text-white">{product.name}</h3>
        <p className="text-slate-500 dark:text-slate-400">{product.price.toLocaleString('vi-VN')}đ</p>
      </div>
    </div>
  );
}
```

### 8.2 Lưới sản phẩm responsive và thanh điều hướng có menu thu gọn

```tsx
// ProductGrid.tsx - responsive: 1 cột (mobile) -> 2 cột (md) -> 4 cột (lg), đúng Phần 4
function ProductGrid({ products }: { products: Product[] }) {
  if (products.length === 0) {
    return <p className="text-center text-slate-500 py-8">Không tìm thấy sản phẩm.</p>; // Nhắc lại Bài 5
  }

  return (
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
      {products.map((p) => (
        <ProductCard key={p.id} product={p} />
      ))}
    </div>
  );
}
```

```tsx
// Navbar.tsx - menu thu gọn trên điện thoại, dùng useToggle đã viết ở Bài 7
function Navbar() {
  const [isOpen, toggle] = useToggle(false);

  return (
    <nav className="flex items-center justify-between p-4 border-b dark:border-gray-700">
      <span className="font-bold">Mini Shop</span>

      {/* Nút hamburger - chỉ hiện trên điện thoại (ẩn từ md trở lên) */}
      <button className="md:hidden" onClick={toggle}>☰</button>

      {/* Menu - ẩn/hiện theo isOpen trên điện thoại, LUÔN hiện từ md trở lên */}
      <div className={cn('flex-col gap-4 md:flex md:flex-row', isOpen ? 'flex' : 'hidden')}>
        <a href="/">Trang chủ</a>
        <a href="/products">Sản phẩm</a>
      </div>
    </nav>
  );
}
```

### 8.3 Nút chuyển Dark/Light mode

Ghép lại đúng `ThemeToggle` đã viết ở mục 5.3 — đặt trong `Navbar` ở trên, hoàn thiện toàn bộ trang danh sách sản phẩm responsive, có dark mode, không còn dòng CSS thuần nào.

---

## 📚 Tài Liệu Tham Khảo

- [Tailwind CSS — Installation with Vite](https://tailwindcss.com/docs/installation/using-vite)
- [Tailwind CSS — Core concepts](https://tailwindcss.com/docs/styling-with-utility-classes)
- [Tailwind CSS — Theme variables](https://tailwindcss.com/docs/theme)
- [Tailwind CSS — Dark mode](https://tailwindcss.com/docs/dark-mode)
- [clsx](https://github.com/lukeed/clsx) · [tailwind-merge](https://github.com/dcastil/tailwind-merge) · [class-variance-authority](https://cva.style/docs)
