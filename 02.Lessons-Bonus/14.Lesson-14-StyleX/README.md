# ⭐ Bài 14 (Bonus): StyleX — CSS-in-JS Biên Dịch Sẵn Của Meta

> 🎯 Mục tiêu: Giải thích được sự khác nhau giữa CSS-in-JS chạy lúc runtime và biên dịch lúc build, viết được style type-safe bằng `stylex.create`/`stylex.props`, xây dựng được Component cho phép ghi đè style có giới hạn qua Props, tạo được theme bằng `defineVars`/`createTheme`, và chọn được công cụ style phù hợp cho từng loại dự án.
>
> 📖 Bài này là tài liệu **tự học**, không dạy trên lớp và không có bài tập/homework nộp.
>
> 🔁 Kiến thức cần nắm trước: CSS Module (Bài 2), Props và Composition (Bài 3), dark mode với Context (homework Bài 9), Tailwind (Bài 13).

---

## Phần 1: CSS-in-JS Là Gì — Runtime So Với Compile-time

### 1.0 Nhắc lại nhanh

Ta đã học 2 cách style hoàn toàn khác nhau: CSS Module (Bài 2 — file `.css` riêng, class có tên) và Tailwind (Bài 13 — utility class có sẵn, không cần file `.css` riêng). StyleX đại diện cho 1 hướng thứ 3: viết style **bằng chính JavaScript/TypeScript**, ngay cạnh Component.

### 1.1 Vấn đề CSS toàn cục, ý tưởng CSS-in-JS

CSS Module (Bài 2) đã giải quyết được vấn đề trùng tên class giữa các Component, nhưng style vẫn nằm ở **1 file riêng biệt** (`Card.module.css`), phải tự chuyển qua lại giữa 2 file khi code. **CSS-in-JS** đưa định nghĩa style vào thẳng file `.tsx`/`.ts`, dưới dạng 1 object JavaScript — không cần file `.css` riêng, và có thể tận dụng toàn bộ hệ thống kiểu của TypeScript cho chính style đó.

### 1.2 Runtime so với Compile-time

> **Thư viện CSS-in-JS "runtime"** (ví dụ styled-components, Emotion) đọc object style và **tự chèn thẻ `<style>` vào trang lúc ứng dụng đang chạy** trong trình duyệt — tốn thêm 1 bước xử lý mỗi lần Component render. **StyleX biên dịch sẵn lúc build**: 1 công cụ (giống Vite build ở Bài 12) đọc code, chuyển toàn bộ `stylex.create(...)` thành các class CSS thật, gộp vào 1 file `.css` tĩnh — lúc chạy trong trình duyệt, StyleX không còn phải tính toán gì thêm, y hệt như dùng CSS Module hay Tailwind (cả 2 cũng đều xử lý sẵn lúc build).

### 1.3 StyleX là gì

StyleX do **Meta** phát triển, đang được dùng cho phiên bản web của Facebook, Instagram, WhatsApp. Điểm mạnh: **type-safe** (TypeScript kiểm tra được style hợp lệ hay không), thứ tự áp dụng style có thể **dự đoán được** (không có tình trạng "class nào định nghĩa sau CSS thắng" mơ hồ như CSS thuần), và file CSS sinh ra rất nhỏ (mỗi khai báo CSS chỉ tồn tại **đúng 1 lần** trong toàn bộ ứng dụng, dùng lại ở mọi nơi cần).

---

## Phần 2: Cài Đặt StyleX Vào Dự Án Vite

### 2.1 Cài đặt và cấu hình

```bash
pnpm add @stylexjs/stylex
pnpm add -D @stylexjs/babel-plugin vite-plugin-stylex
```

```ts
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import styleX from 'vite-plugin-stylex';

export default defineConfig({
  plugins: [react(), styleX()],
});
```

> ⚠️ **Cách tích hợp StyleX với Vite thay đổi khá nhanh theo từng phiên bản** (tên package plugin, cách cấu hình có thể khác so với ví dụ trên tại thời điểm bạn đọc). Luôn đối chiếu lại [tài liệu chính thức](https://stylexjs.com) trước khi cài đặt cho 1 dự án thật.

### 2.2 ESLint plugin

```bash
pnpm add -D @stylexjs/eslint-plugin
```

Plugin này bắt lỗi viết style sai **ngay khi gõ** — tương tự vai trò của `eslint-plugin-react-hooks` đã dùng từ Bài 7, nhưng chuyên biệt cho các quy tắc riêng của StyleX (mục 3.2).

---

## Phần 3: Viết Style Cơ Bản Với `stylex.create` Và `stylex.props`

### 3.1 Cú pháp

```tsx
import * as stylex from '@stylexjs/stylex';

const styles = stylex.create({
  card: {
    border: '1px solid #e2e8f0',
    borderRadius: 8,
    padding: 16,
    boxShadow: '0 2px 4px rgba(0,0,0,0.1)',
  },
  title: {
    color: '#1e293b',
    marginBottom: 12,
  },
});

function Card({ title, children }: { title: string; children: React.ReactNode }) {
  return (
    <div {...stylex.props(styles.card)}>
      <h3 {...stylex.props(styles.title)}>{title}</h3>
      {children}
    </div>
  );
}
```

`stylex.create({...})` định nghĩa 1 object style (tên thuộc tính CSS viết camelCase, giống cách viết `style={{...}}` inline đã học ở Bài 2). `stylex.props(styles.card)` trả về `{ className: '...' }` — gắn vào JSX bằng cú pháp **spread** (`{...}`, đã ôn ở Bài 1) thay vì gán trực tiếp `className={styles.card}` như CSS Module.

### 3.2 Quy tắc quan trọng: style phải phân tích tĩnh được

```tsx
// ❌ Sai - StyleX không biết trước "key" là gì lúc build, không phân tích tĩnh được
const dynamicKey = 'card';
const styles = stylex.create({
  [dynamicKey]: { padding: 16 },
});

// ❌ Sai - lấy giá trị từ 1 biến import ở file khác (không phải hằng số nội bộ)
import { spacing } from './config';
const styles = stylex.create({
  card: { padding: spacing.medium },
});
```

> ⚠️ Vì StyleX cần **đọc và biên dịch code lúc build** (Phần 1.2) — không chạy JavaScript thật để biết giá trị — mọi thứ bên trong `stylex.create` phải **xác định được ngay khi đọc code**, không được tạo ra từ biến động hay import chéo phức tạp. Đây là đánh đổi để có được tốc độ chạy nhanh và CSS nhỏ đã nói ở Phần 1.

### 3.3 So sánh cùng 1 Card viết bằng 3 cách

| | CSS Module (Bài 2) | Tailwind (Bài 13) | StyleX |
|---|---|---|---|
| Nơi viết style | File `.css` riêng | Ngay trong `className` | Ngay trong file `.tsx`/`.ts`, dạng object |
| Đặt tên | Phải đặt tên class | Không cần | Không cần (đặt tên key nội bộ, không sinh ra class theo tên đó) |
| Kiểm tra kiểu | Không | Không | Có — TypeScript hiểu cấu trúc style |

---

## Phần 4: Pseudo-class, Media Query Và Style Động

### 4.1 Pseudo-class và media query

```tsx
const styles = stylex.create({
  button: {
    backgroundColor: {
      default: '#3b82f6',
      ':hover': '#2563eb',
      ':active': '#1d4ed8',
    },
    padding: {
      default: 8,
      '@media (min-width: 768px)': 12, // Tương tự tiền tố md: của Tailwind (Bài 13)
    },
  },
});
```

Pseudo-class (`:hover`) và media query viết **lồng bên trong giá trị** của từng thuộc tính CSS (`default` là giá trị mặc định), khác hẳn cú pháp tiền tố `hover:`/`md:` của Tailwind — nhưng biểu đạt cùng 1 ý tưởng: "style khác nhau tùy trạng thái/kích thước màn hình".

### 4.2 Style có điều kiện

```tsx
function Button({ isActive }: { isActive: boolean }) {
  return (
    <button {...stylex.props(styles.base, isActive && styles.active)}>
      Nhấn
    </button>
  );
}
```

> **`stylex.props` nhận vào NHIỀU object style, style truyền vào SAU luôn thắng nếu có thuộc tính trùng nhau** — giống hệt cách `tailwind-merge` (Bài 13) xử lý class truyền sau ghi đè class truyền trước, chỉ khác là StyleX đảm bảo thứ tự này **luôn đúng và dự đoán được** (đã nhắc ở Phần 1.3), không phụ thuộc vào thứ tự CSS được nạp vào trang như CSS thuần.

### 4.3 Style động theo Props/State

```tsx
const styles = stylex.create({
  bar: (percent: number) => ({
    width: `${percent}%`,
    backgroundColor: percent > 80 ? '#ef4444' : '#22c55e',
  }),
});

function ProgressBar({ percent }: { percent: number }) {
  return <div {...stylex.props(styles.bar(percent))} />;
}
```

Truyền 1 **hàm** vào `stylex.create` cho phép tính style dựa trên giá trị chỉ biết được lúc chạy (ví dụ `percent` từ Props/State) — StyleX vẫn biên dịch được phần "khung" của style lúc build, chỉ giá trị cụ thể mới tính lúc chạy.

---

## Phần 5: Tái Sử Dụng Và Ghi Đè Style Qua Props

### 5.1 Nhận thêm style từ bên ngoài

```tsx
import * as stylex from '@stylexjs/stylex';
import type { StyleXStyles } from '@stylexjs/stylex';

type CardProps = {
  title: string;
  style?: StyleXStyles<{ marginTop?: number; marginBottom?: number }>;
};

function Card({ title, style }: CardProps) {
  return <div {...stylex.props(styles.card, style)}>{title}</div>;
}
```

```tsx
// Component cha ghi đè MỘT PHẦN style, không thay đổi toàn bộ giao diện của Card
<Card title="Sản phẩm" style={stylex.create({ box: { marginTop: 24 } }).box} />
```

### 5.2 Giới hạn property được phép ghi đè

> **Kiểu `StyleXStyles<{...}>` khai báo CHÍNH XÁC những thuộc tính CSS nào Component cho phép Component cha ghi đè** (ở ví dụ trên: chỉ `marginTop`/`marginBottom`). Nếu Component cha cố truyền 1 thuộc tính khác (ví dụ `backgroundColor`), TypeScript báo lỗi ngay — đây là điểm khác biệt lớn nhất so với `className`/`cn()` của Tailwind (Bài 13): `className` là 1 chuỗi tự do, không có gì ngăn Component cha ghi đè **bất kỳ** thuộc tính nào (kể cả những thuộc tính không nên bị đổi, ví dụ đổi `display` khiến Component vỡ layout).

### 5.3 So sánh với `className` + `cn()`

| | `className` + `cn()` (Tailwind, Bài 13) | `style` kiểu `StyleXStyles<T>` |
|---|---|---|
| Component cha ghi đè được gì | Bất kỳ class nào | Chỉ đúng những thuộc tính đã khai báo cho phép |
| TypeScript kiểm tra | Không (chuỗi tự do) | Có |

---

## Phần 6: Biến CSS Và Theme Với `defineVars` / `createTheme`

### 6.1 `stylex.defineVars` khai báo design token

```ts
// tokens.stylex.ts - BẮT BUỘC đặt trong file đuôi ".stylex.ts"
import * as stylex from '@stylexjs/stylex';

export const colors = stylex.defineVars({
  bgPrimary: '#ffffff',
  textPrimary: '#0f172a',
  brand: '#6366f1',
});

export const spacing = stylex.defineVars({
  md: '16px',
  lg: '24px',
});
```

Tương tự `@theme` của Tailwind (Bài 13), `defineVars` khai báo tập hợp design token (màu, khoảng cách...) dùng chung cho toàn dự án — nhưng ở dạng biến TypeScript có kiểu, thay vì CSS variable viết trong file `.css`.

### 6.2 `stylex.createTheme` tạo theme sáng/tối

```ts
// darkTheme.stylex.ts
import * as stylex from '@stylexjs/stylex';
import { colors } from './tokens.stylex';

export const darkTheme = stylex.createTheme(colors, {
  bgPrimary: '#0f172a',
  textPrimary: '#f1f5f9',
  brand: '#818cf8',
});
```

```tsx
import { darkTheme } from './darkTheme.stylex';

function App() {
  const [isDark] = useLocalStorage<boolean>('dark-mode', false); // Custom Hook - Bài 7

  return (
    <div {...stylex.props(isDark && darkTheme)}>
      {/* Toàn bộ Component con dùng colors.bgPrimary/colors.textPrimary sẽ tự đổi giá trị */}
    </div>
  );
}
```

`createTheme` tạo ra **1 bộ giá trị thay thế** cho các token đã khai báo ở `defineVars`, áp dụng cho **1 nhánh cây Component** (giống phạm vi của `<Context.Provider>` — Bài 9) — mọi Component con dùng `colors.bgPrimary` sẽ tự động nhận đúng giá trị của theme đang active, không cần truyền Props theme xuống từng Component.

> 💡 Việc chuyển đổi theme (State lưu `isDark`, đồng bộ với `localStorage`) áp dụng đúng kỹ thuật đã học ở Bài 7 (`useLocalStorage`). Muốn thêm hiệu ứng chuyển động, StyleX có `stylex.keyframes(...)` để định nghĩa animation, dùng tương tự `@keyframes` trong CSS thuần.

---

## Phần 7: So Sánh CSS Module, Tailwind Và StyleX

| Tiêu chí | CSS Module (Bài 2) | Tailwind (Bài 13) | StyleX |
|---|---|---|---|
| Cách viết | File `.css` riêng, đặt tên class | Utility class trong `className` | Object style trong `.tsx`/`.ts` |
| Type-safety | Không | Không | Có |
| Kích thước CSS | Theo file gốc | Rất nhỏ (chỉ sinh class dùng tới) | Rất nhỏ (mỗi khai báo chỉ tồn tại 1 lần) |
| Hệ sinh thái / độ phổ biến | Có sẵn trong Vite, dùng lâu năm | Rất phổ biến, cộng đồng lớn | Còn mới, cộng đồng nhỏ hơn |
| Độ khó khi học | Thấp (giống CSS thuần) | Trung bình (cần nhớ tên utility) | Trung bình-cao (cần hiểu compile-time, `StyleXStyles`) |

**Khi nào chọn StyleX:** dự án lớn, có **design system dùng chung nhiều team**, cần đảm bảo Component chỉ cho phép ghi đè đúng những gì được thiết kế cho phép (mục 5.2) — lợi ích về an toàn kiểu và khả năng mở rộng lớn hơn chi phí học thêm công cụ mới.

**Khi nào Tailwind là đủ:** phần lớn dự án còn lại, đặc biệt khi làm việc solo hoặc nhóm nhỏ — tốc độ code nhanh và cộng đồng hỗ trợ lớn quan trọng hơn khả năng giới hạn ghi đè style.

---

## Phần 8: Ví Dụ Tổng Hợp — Mini Design System Với StyleX

### 8.1 `tokens.stylex.ts` — design token dùng chung

```ts
import * as stylex from '@stylexjs/stylex';

export const colors = stylex.defineVars({
  bgPrimary: '#ffffff',
  textPrimary: '#0f172a',
  brand: '#6366f1',
  border: '#e2e8f0',
});

export const spacing = stylex.defineVars({
  sm: '8px',
  md: '16px',
  lg: '24px',
});

export const radii = stylex.defineVars({
  md: '8px',
});
```

### 8.2 Component `Button` — variant + size, ghi đè style có giới hạn

```tsx
import * as stylex from '@stylexjs/stylex';
import type { StyleXStyles } from '@stylexjs/stylex';
import { colors, spacing, radii } from './tokens.stylex';

const styles = stylex.create({
  base: {
    borderRadius: radii.md,
    fontWeight: 600,
    border: 'none',
    cursor: 'pointer',
  },
  primary: { backgroundColor: colors.brand, color: '#ffffff' },
  secondary: { backgroundColor: colors.border, color: colors.textPrimary },
  sm: { padding: `${spacing.sm} ${spacing.md}`, fontSize: 14 },
  lg: { padding: `${spacing.md} ${spacing.lg}`, fontSize: 16 },
});

type ButtonProps = {
  variant?: 'primary' | 'secondary';
  size?: 'sm' | 'lg';
  children: React.ReactNode;
  style?: StyleXStyles<{ marginTop?: number }>;
};

function Button({ variant = 'primary', size = 'sm', children, style }: ButtonProps) {
  return (
    <button
      {...stylex.props(
        styles.base,
        variant === 'primary' && styles.primary,
        variant === 'secondary' && styles.secondary,
        styles[size],
        style
      )}
    >
      {children}
    </button>
  );
}
```

### 8.3 Component `Card`, theme sáng/tối và theme thứ 3

```tsx
const cardStyles = stylex.create({
  box: {
    backgroundColor: colors.bgPrimary,
    color: colors.textPrimary,
    border: `1px solid ${colors.border}`,
    borderRadius: radii.md,
    padding: spacing.lg,
  },
});

function Card({ children }: { children: React.ReactNode }) {
  return <div {...stylex.props(cardStyles.box)}>{children}</div>;
}
```

```ts
// darkTheme.stylex.ts
export const darkTheme = stylex.createTheme(colors, {
  bgPrimary: '#0f172a',
  textPrimary: '#f1f5f9',
  brand: '#818cf8',
  border: '#334155',
});

// highContrastTheme.stylex.ts - theme thứ 3, KHÔNG cần sửa Button/Card
export const highContrastTheme = stylex.createTheme(colors, {
  bgPrimary: '#000000',
  textPrimary: '#ffffff',
  brand: '#ffff00',
  border: '#ffffff',
});
```

```tsx
function App() {
  const [theme, setTheme] = useLocalStorage<'light' | 'dark' | 'high-contrast'>('theme', 'light');

  const themeStyle = theme === 'dark' ? darkTheme : theme === 'high-contrast' ? highContrastTheme : undefined;

  return (
    <div {...stylex.props(themeStyle)}>
      <Card>
        <Button variant="primary">Xác nhận</Button>
        <Button variant="secondary">Hủy</Button>
      </Card>
    </div>
  );
}
```

Điểm mấu chốt: `Button` và `Card` **không hề biết** có bao nhiêu theme đang tồn tại — thêm theme thứ 3 (`highContrastTheme`) chỉ cần khai báo 1 file `.stylex.ts` mới bằng `createTheme`, không phải sửa lại bất kỳ Component nào đã viết.

---

## 📚 Tài Liệu Tham Khảo

- [StyleX — Installation](https://stylexjs.com/docs/learn/installation/)
- [StyleX — Defining styles](https://stylexjs.com/docs/learn/styling-with-stylex/defining-styles/)
- [StyleX — Using styles](https://stylexjs.com/docs/learn/styling-with-stylex/using-dynamic-styles/)
- [StyleX — Theming](https://stylexjs.com/docs/learn/theming/)
- [StyleX — Statically typed styles](https://stylexjs.com/docs/learn/thinking-in-stylex/#static-types)
- Hướng dẫn tích hợp StyleX với Vite trong tài liệu chính thức (kiểm tra phiên bản mới nhất khi đọc — mục 2.1 của bài này)
