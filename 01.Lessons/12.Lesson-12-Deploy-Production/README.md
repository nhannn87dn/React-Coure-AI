# ⭐ Bài 12: Deploy Ứng Dụng React Với Vercel

> 🎯 Mục tiêu: Học viên chuẩn bị và build được dự án cho production (không lỗi TypeScript, biến môi trường đúng), deploy được lên Vercel với React Router hoạt động đúng, và hiểu được quy trình tự động deploy khi push code.

---

## Phần 1: Chuẩn Bị Dự Án Trước Khi Deploy

### 1.0 Nhắc lại nhanh

Suốt 11 bài học, ta chỉ chạy ứng dụng bằng `pnpm run dev` — môi trường **development**, ưu tiên tốc độ code (HMR — đã học ở Bài 1) hơn là hiệu năng. Bài này học cách đưa ứng dụng sang môi trường **production** — nơi người dùng thật sẽ truy cập, ưu tiên tốc độ tải trang và độ ổn định.

### 1.1 Rà soát `console.log`, code test còn sót lại

Trước khi build, dành thời gian tìm và xóa các `console.log` dùng để debug (đã dùng rất nhiều từ Bài 4 đến Bài 11), các đoạn code thử nghiệm tạm thời, hoặc dữ liệu giả (mock data) không còn cần thiết. Đây là bước dọn dẹp thủ công đơn giản nhưng dễ bị bỏ quên.

### 1.2 File `.env` và quy tắc đặt tên biến môi trường

Đã học từ Bài 1 và dùng xuyên suốt (`VITE_API_URL` ở Bài 10): biến môi trường trong dự án Vite **bắt buộc tiền tố `VITE_`** để code phía client đọc được qua `import.meta.env`.

```bash
# .env
VITE_API_URL=https://dummyjson.com
```

Khai báo kiểu cho biến môi trường trong `vite-env.d.ts` để có gợi ý code và kiểm tra kiểu khi dùng `import.meta.env`:

```ts
// src/vite-env.d.ts
/// <reference types="vite/client" />

interface ImportMetaEnv {
  readonly VITE_API_URL: string;
}

interface ImportMeta {
  readonly env: ImportMetaEnv;
}
```

Sau khi khai báo, `import.meta.env.VITE_API_URL` sẽ được TypeScript hiểu đúng là kiểu `string`, và gõ `import.meta.env.` sẽ được VS Code gợi ý đầy đủ các biến đã khai báo — tương tự lợi ích của việc gõ `type`/`interface` cho Props đã học ở Bài 3.

### 1.3 Kiểm tra `.gitignore`

```text
# .gitignore
node_modules
dist
.env
```

> ⚠️ File `.env` chứa các giá trị nhạy cảm (API key, token...) **tuyệt đối không được commit lên Git** — nếu vô tình đã commit trước đó, phải đổi lại toàn bộ key/token đã lộ, xóa khỏi lịch sử Git không đủ để coi là an toàn (key đã có thể bị người khác thấy).

---

## Phần 2: Build Ứng Dụng Với Vite Và Kiểm Tra Bằng `preview`

### 2.1 Chạy `pnpm run build`

```bash
pnpm run build
```

Lệnh này tạo ra thư mục **`dist/`** — chứa các file HTML/CSS/JS đã được **tối ưu hóa** cho production: code được rút gọn (minify), gộp file (bundle), và đặt tên file kèm mã hash (ví dụ `index-a1b2c3.js`) để trình duyệt biết khi nào cần tải lại phiên bản mới.

### 2.2 Build chạy kiểm tra TypeScript trước

```json
// package.json
{
  "scripts": {
    "build": "tsc -b && vite build"
  }
}
```

> ⚠️ Script `build` mặc định của dự án Vite + TypeScript chạy **`tsc -b`** (kiểm tra kiểu TypeScript trên toàn dự án) **trước** khi thực sự đóng gói bằng `vite build`. Nếu còn **bất kỳ lỗi kiểu dữ liệu nào** (ví dụ dùng sai kiểu Props — Bài 3, quên xử lý `null` — Bài 1) dù ứng dụng vẫn chạy bình thường ở `pnpm run dev`, lệnh `build` sẽ **thất bại hoàn toàn**, không tạo ra `dist/`. Đây là lý do quy tắc "không dùng `any`" và luôn sửa hết lỗi TypeScript (nhắc lại xuyên suốt từ Bài 1) không chỉ là thói quen tốt, mà còn là **điều kiện bắt buộc** để deploy được.

### 2.3 Xem thử bản build với `pnpm run preview`

```bash
pnpm run preview
```

`pnpm run dev` (môi trường dev, có HMR) và `pnpm run preview` (chạy thử **đúng** file trong `dist/` vừa build) là 2 lệnh khác nhau. Luôn chạy `preview` để xác nhận bản build hoạt động đúng **trước khi** đưa lên Vercel — 1 vài lỗi chỉ xuất hiện ở bản build production (ví dụ biến môi trường cấu hình sai) sẽ không thấy được nếu chỉ test bằng `pnpm run dev`.

### 2.4 Kiểm tra dung lượng file build

Sau khi `pnpm run build` chạy xong, terminal in ra bảng liệt kê từng file trong `dist/` kèm dung lượng. Vite tự động cảnh báo (dòng chữ vàng) nếu có file JavaScript vượt quá ngưỡng khuyến nghị (mặc định 500kB) — đây là dấu hiệu nên áp dụng Code Splitting (Phần 3).

---

## Phần 3: Code Splitting — Giảm Dung Lượng Tải Lần Đầu

### 3.1 Vấn đề: toàn bộ ứng dụng gộp vào 1 file JavaScript lớn

Mặc định, `vite build` gộp **toàn bộ code** của ứng dụng (mọi trang, mọi Route đã cấu hình ở Bài 8) vào chung 1 (hoặc vài) file JavaScript. Người dùng vừa mở trang chủ đã phải tải luôn cả code của trang Admin, trang Dashboard... dù có thể họ không bao giờ ghé thăm những trang đó.

### 3.2 `React.lazy` + `Suspense` — tách code theo từng Route

```tsx
// router.tsx - TRƯỚC: import trực tiếp, mọi trang gộp chung 1 file
import ProductListPage from './pages/ProductListPage';
import AdminPage from './pages/AdminPage';
```

```tsx
// router.tsx - SAU: import "lười" (lazy), mỗi trang tách thành 1 file JS riêng
import { lazy, Suspense } from 'react';

const ProductListPage = lazy(() => import('./pages/ProductListPage'));
const AdminPage = lazy(() => import('./pages/AdminPage'));

export const router = createBrowserRouter([
  {
    path: '/',
    element: <MainLayout />,
    children: [
      {
        path: 'products',
        element: (
          <Suspense fallback={<p>Đang tải trang...</p>}>
            <ProductListPage />
          </Suspense>
        ),
      },
      {
        path: 'admin',
        element: (
          <Suspense fallback={<p>Đang tải trang...</p>}>
            <AdminPage />
          </Suspense>
        ),
      },
    ],
  },
]);
```

`React.lazy(() => import(...))` báo cho Vite biết: **tách riêng** Component này thành 1 file JS độc lập, chỉ **tải xuống khi thực sự cần** (khi người dùng điều hướng đến route đó — dùng `Link`/`NavLink` đã học ở Bài 8). `<Suspense fallback={...}>` bắt buộc phải bọc quanh Component `lazy` — hiển thị `fallback` trong khoảng thời gian ngắn file JS đang được tải về.

So sánh dung lượng: chạy lại `pnpm run build` (mục 2.4) sau khi áp dụng `React.lazy`, quan sát danh sách file trong `dist/` giờ được **tách thành nhiều file nhỏ hơn** thay vì 1 file lớn duy nhất.

---

## Phần 4: Đưa Dự Án Lên GitHub

### 4.1 Khởi tạo Git repository

```bash
git init
```

Chỉ cần chạy nếu dự án **chưa** là 1 Git repository (dự án tạo bằng `pnpm create vite` thường đã tự động `git init` sẵn).

### 4.2 Quy trình `git add`, `git commit`, `git push`

```bash
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/<username>/<repo-name>.git
git push -u origin main
```

### 4.3 Viết commit message rõ ràng

Quy ước cơ bản: mô tả ngắn gọn **"làm gì"**, ở dạng động từ nguyên mẫu, ví dụ: `feat: thêm trang chi tiết sản phẩm`, `fix: sửa lỗi 404 khi refresh trang route con`. Tránh commit message chung chung như `"update"`, `"fix bug"` không rõ sửa gì.

---

## Phần 5: Giới Thiệu Vercel

### 5.1 Đăng ký tài khoản bằng GitHub

Vào [vercel.com](https://vercel.com), chọn **"Sign in with GitHub"** — Vercel sẽ liên kết trực tiếp với tài khoản GitHub, không cần tạo mật khẩu riêng.

### 5.2 Import Repository từ GitHub

Trong Vercel Dashboard, chọn **"Add New..." → "Project"**, chọn đúng Repository vừa đẩy code lên ở Phần 4.

### 5.3 Vercel tự động nhận diện dự án Vite/React

Vercel phát hiện `package.json` có `vite` trong dependencies, tự động đề xuất sẵn cấu hình Build Command và Output Directory phù hợp (sẽ kiểm tra kỹ ở Phần 6).

---

## Phần 6: Deploy Dự Án Lên Vercel

### 6.1 Kiểm tra Build Command

Đảm bảo Vercel đang dùng đúng `pnpm run build` (hoặc `pnpm build`) — khớp với script đã cấu hình ở mục 2.2.

### 6.2 Kiểm tra Output Directory

Với dự án Vite, thư mục chứa bản build luôn là **`dist`** (đã thấy ở mục 2.1) — xác nhận Vercel điền đúng giá trị này ở phần cấu hình Output Directory.

### 6.3 Theo dõi quá trình Deploy

Nhấn **"Deploy"**, Vercel hiển thị Log build **theo thời gian thực** (giống terminal khi tự chạy `pnpm run build` ở máy) — nếu build lỗi (ví dụ lỗi TypeScript đã nói ở mục 2.2), Log sẽ cho biết chính xác dòng nào gây lỗi. Deploy thành công, Vercel cấp 1 link demo dạng `https://<project-name>.vercel.app`.

---

## Phần 7: Xử Lý Lỗi 404 Khi Refresh Trang Với React Router

### 7.1 Nguyên nhân lỗi 404

Thử refresh (F5) ở 1 route con bất kỳ trên bản deploy vừa xong (ví dụ `https://<project>.vercel.app/products/1`) — Vercel báo lỗi **404 Not Found**.

> **Nguyên nhân: Vercel (giống mọi hosting tĩnh) mặc định tìm 1 FILE VẬT LÝ khớp với đường dẫn URL** (ví dụ tìm file `products/1/index.html`) — nhưng dự án SPA (Phần 1, Bài 8) chỉ có **đúng 1 file HTML** (`index.html`), mọi route con đều do React Router **tự vẽ bằng JavaScript** sau khi trang đã tải, Vercel không hề biết đến khái niệm "route" mà React Router định nghĩa.

Lưu ý: lỗi này **không xảy ra khi điều hướng bằng `Link`/`NavLink`** (Bài 8) vì đó là điều hướng nội bộ bằng JavaScript, không có request mới gửi lên server — chỉ xảy ra khi **request thật** được gửi thẳng đến server (refresh trang, gõ thẳng URL, mở link chia sẻ).

### 7.2 Cấu hình `vercel.json` với `rewrites`

```json
// vercel.json (đặt tại thư mục gốc dự án)
{
  "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }]
}
```

`rewrites` báo cho Vercel: **bất kỳ đường dẫn nào** (`/(.*)` — khớp mọi route) đều trả về `index.html`, để React Router (đã tải sẵn trong `index.html`) tự đọc URL và vẽ đúng route con tương ứng — thay vì Vercel tự đi tìm file vật lý không tồn tại.

### 7.3 Deploy lại và kiểm tra

Commit + push file `vercel.json` (Phần 4) — Vercel tự động deploy lại (cơ chế CI/CD sẽ học ở Phần 9). Refresh lại đúng route con vừa bị lỗi 404, xác nhận giờ hiển thị đúng.

---

## Phần 8: Cấu Hình Environment Variables Trên Vercel

### 8.1 Khai báo biến qua Project Settings

Vào **Project Settings → Environment Variables** trên Vercel Dashboard, thêm các biến giống hệt file `.env` cục bộ (mục 1.2) — ví dụ `VITE_API_URL`.

### 8.2 Phân biệt Production / Preview / Development

Vercel cho phép khai báo giá trị **khác nhau** cho từng môi trường — ví dụ `VITE_API_URL` ở Production trỏ đến API thật, còn ở Preview (Phần 9.3) có thể trỏ đến 1 API thử nghiệm.

### 8.3 Redeploy để biến môi trường có hiệu lực

> ⚠️ Biến môi trường của Vite được **nhúng cứng vào code** ngay lúc build (không đọc động lúc chạy như biến môi trường phía server) — thêm/sửa biến môi trường trên Vercel Dashboard **không** tự áp dụng cho bản đã deploy trước đó. Phải **redeploy lại** (Deployments → chọn bản mới nhất → Redeploy) để bản build mới đọc đúng giá trị biến môi trường vừa cập nhật.

---

## Phần 9: Custom Domain Và CI/CD Cơ Bản Với Vercel

### 9.1 Gắn Custom Domain

Nếu đã sở hữu 1 domain riêng, vào **Project Settings → Domains** để gắn domain đó thay cho địa chỉ `vercel.app` mặc định.

### 9.2 Continuous Deployment — tự động Deploy khi Push code mới

> **Sau khi đã liên kết GitHub Repository (Phần 5.2), MỌI lần `git push` lên nhánh chính (`main`) đều tự động kích hoạt 1 lượt Deploy mới trên Vercel** — không cần vào Dashboard bấm Deploy thủ công lần nào nữa (đây chính là điều đã xảy ra ở mục 7.3).

### 9.3 Preview Deployment cho mỗi Pull Request

Khi làm việc nhóm, mỗi Pull Request được tạo trên GitHub sẽ được Vercel tự động tạo ra **1 bản Deploy Preview riêng** (link demo tạm thời, khác với domain Production) — cho phép review giao diện thực tế của thay đổi đó **trước khi** merge vào `main`, không ảnh hưởng đến bản Production đang chạy.

---

## Phần 10: Tổng Kết Khóa Học Và Giao Đề Capstone Project

### 10.1 Ôn lại dòng chảy 12 buổi qua 1 dự án thực tế

Từ Component/JSX đầu tiên (Bài 2-3), qua State/Effect (Bài 4-6), Form/Custom Hook (Bài 7), Router (Bài 8), Global State (Bài 9), đến Data Fetching (Bài 10-11) và Deploy (bài này) — đây chính xác là quy trình xây dựng **1 ứng dụng frontend hoàn chỉnh** từ đầu đến khi đưa ra production.

### 10.2 Checklist Best Practices

- Cấu trúc thư mục rõ ràng: `components/`, `pages/`, `hooks/`, `stores/`, `lib/`/`services/` (đã dùng xuyên suốt từ Bài 3 đến Bài 11).
- Tách Component/Custom Hook hợp lý — không để 1 Component vừa lo UI vừa ôm quá nhiều logic (Bài 7).
- Quản lý State đúng chỗ: local State (Bài 4) → Lifting State Up (Bài 5) → Global State (Bài 9) → Server State (Bài 10-11) — không dùng nhầm loại cho tình huống không phù hợp.
- TypeScript chặt chẽ, không dùng `any` (nguyên tắc xuyên suốt từ Bài 1).

### 10.3 Capstone Project

Giảng viên công bố đề bài **Capstone Project** (làm tại nhà, nộp sau khóa học). Xem chi tiết đề bài, yêu cầu tối thiểu và tiêu chí chấm điểm tại [capstone-project.md](capstone-project.md).

### 10.4 Lộ trình học tiếp

Sau khóa học, các hướng đào sâu tiếp theo: **Next.js** (framework full-stack dựa trên React, đã nhắc ở Bài 1), **react-hook-form + zod** (thư viện quản lý Form chuyên nghiệp hơn cách viết thủ công ở Bài 7), **testing** với Vitest + React Testing Library, và các tính năng mới của **React 19** (Actions, `useActionState`) cho việc xử lý Form/Mutation theo hướng khai báo hơn nữa.

---

## 🧪 Bài tập thực hành cuối buổi

1. Chạy `pnpm run build` cho 1 dự án đã làm ở các bài trước, cố tình để lại 1 lỗi kiểu TypeScript (ví dụ truyền sai kiểu Props) và xác nhận lệnh build thất bại — sửa lỗi và build lại thành công.
2. Áp dụng `React.lazy` + `Suspense` (Phần 3) cho ít nhất 2 Route trong dự án Mini Shop, so sánh danh sách file trong `dist/` trước và sau.
3. Đẩy 1 dự án lên GitHub, deploy lên Vercel, xác nhận link demo hoạt động.
4. Thử refresh 1 route con trên bản deploy vừa xong (chưa có `vercel.json`) để tận mắt thấy lỗi 404 (Phần 7.1), sau đó thêm `vercel.json`, push lại và xác nhận lỗi đã hết.

## 🧪 Bài tập HomeWork

Xem tại file [homeworks.md](homeworks.md)

---

## 🏁 Capstone Project

Xem đề bài đầy đủ tại [capstone-project.md](capstone-project.md).
