# ⭐ Bài 12: Deploy Ứng Dụng React Với Vercel

> 🎯 Mục tiêu: Học viên biết chuẩn bị dự án trước khi deploy, build và kiểm tra bản production bằng Vite, đưa code lên GitHub, deploy thành công lên Vercel, xử lý đúng lỗi 404 của React Router, cấu hình biến môi trường, và tổng kết toàn bộ kiến thức khoá học qua đề Capstone Project.

---

## Phần 1: Chuẩn Bị Dự Án Trước Khi Deploy

### 1.0 Định nghĩa

> **Deploy (triển khai) là quá trình đưa ứng dụng từ máy cá nhân (môi trường development) lên 1 server công khai (môi trường production) để người dùng thật có thể truy cập qua internet.**

### 1.1 Rà soát `console.log`, code test còn sót lại

Trước khi deploy, nên rà soát và xoá các `console.log()` dùng để debug (ví dụ những dòng đã thêm để quan sát State trong các bài trước), code thử nghiệm tạm thời, hoặc các Component/route chỉ dùng để test — những thứ này không nguy hiểm nhưng làm console của người dùng thật bị "rác" và có thể vô tình lộ thông tin nội bộ không cần thiết.

### 1.2 Tạo file `.env` và quy tắc đặt tên biến môi trường với Vite

```bash
# .env
VITE_API_BASE_URL=https://api.example.com
VITE_APP_NAME=My React App
```

```tsx
// lib/axiosInstance.ts - sử dụng biến môi trường (ôn lại Bài 10, Phần 3.1)
import axios from 'axios';

const axiosInstance = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
});

export default axiosInstance;
```

> ⚠️ Với dự án dùng Vite, **mọi biến môi trường muốn dùng được ở phía Client (trình duyệt) đều bắt buộc phải có tiền tố `VITE_`** — đây là quy ước bảo mật có chủ đích: Vite chỉ "lộ" ra client những biến bắt đầu bằng `VITE_`, các biến khác (ví dụ dùng cho server riêng) sẽ không bao giờ bị nhúng vào code build ra, tránh vô tình public dữ liệu nhạy cảm (database password, secret key...) lên trình duyệt.

### 1.3 Kiểm tra `.gitignore` đã loại trừ `.env` và `node_modules`

```text
# .gitignore
node_modules
dist
.env
.env.local
```

File `.env` thường chứa các giá trị nhạy cảm (API key, endpoint nội bộ) — **tuyệt đối không commit lên GitHub** (kể cả repository private), vì lịch sử Git vẫn lưu lại vĩnh viễn nếu đã từng commit dù sau đó xoá đi. `node_modules` cũng không nên commit vì có thể được cài lại hoàn toàn từ `package.json` (`pnpm install`), tránh repository nặng nề không cần thiết.

---

## Phần 2: Build Ứng Dụng React Với Vite

### 2.1 Chạy `pnpm run build` và giải thích thư mục `dist`

```bash
pnpm run build
```

Lệnh này (định nghĩa sẵn trong `package.json` từ lúc tạo dự án bằng Vite ở Bài 1) sẽ:

1. Biên dịch toàn bộ code TypeScript/JSX thành JavaScript thuần mà trình duyệt hiểu được.
2. Gộp (bundle), rút gọn (minify) code để giảm dung lượng tải xuống.
3. Xuất toàn bộ kết quả vào thư mục **`dist/`** — đây chính là "sản phẩm cuối cùng" sẽ được đưa lên server, hoàn toàn khác với thư mục `src/` (chỉ dùng lúc phát triển).

```text
dist/
├── index.html
├── assets/
│   ├── index-a1b2c3d4.js   # code JS đã gộp + minify
│   └── index-e5f6g7h8.css  # CSS đã gộp + minify
```

### 2.2 Dùng `pnpm run preview` để xem thử bản build

```bash
pnpm run preview
```

Lệnh `dev` (`pnpm run dev`, đã dùng xuyên suốt khoá học) chạy ứng dụng ở chế độ **development** — có Hot Module Replacement (Bài 1), thông báo lỗi chi tiết, nhưng **không đại diện đúng hiệu năng thực tế**. `pnpm run preview` khởi chạy 1 server nhỏ để phục vụ **đúng thư mục `dist/` vừa build** — đây là cách chính xác nhất để kiểm tra ứng dụng sẽ chạy như thế nào khi thật sự lên production, trước khi đưa lên Vercel.

### 2.3 Kiểm tra dung lượng file build và cảnh báo tối ưu từ Vite

Sau khi chạy `pnpm run build`, Vite thường in ra bảng thống kê dung lượng từng file, kèm cảnh báo nếu có file JavaScript quá lớn (ví dụ "Some chunks are larger than 500 kB after minification"). Đây là gợi ý để cân nhắc các kỹ thuật tối ưu nâng cao hơn (code-splitting, lazy loading route) — nằm ngoài phạm vi khoá học này nhưng nên biết để tìm hiểu thêm khi dự án lớn dần.

---

## Phần 3: Đưa Dự Án Lên GitHub

### 3.1 Khởi tạo Git repository nếu dự án chưa có

```bash
git init
```

### 3.2 Quy trình `git add`, `git commit`, `git push`

```bash
git add .
git commit -m "feat: hoàn thiện ứng dụng React trước khi deploy"
git branch -M main
git remote add origin https://github.com/<username>/<repo-name>.git
git push -u origin main
```

### 3.3 Viết commit message rõ ràng theo quy ước cơ bản

Quy ước phổ biến (Conventional Commits) đặt tiền tố trước nội dung commit để dễ theo dõi lịch sử thay đổi:

```text
feat: thêm chức năng đăng nhập
fix: sửa lỗi giỏ hàng không cập nhật tổng tiền
chore: cập nhật biến môi trường
docs: cập nhật README hướng dẫn cài đặt
```

- `feat`: thêm tính năng mới.
- `fix`: sửa lỗi.
- `chore`: thay đổi không ảnh hưởng logic (cấu hình, dọn dẹp).
- `docs`: chỉ thay đổi tài liệu.

---

## Phần 4: Giới Thiệu Vercel — Đăng Ký và Kết Nối GitHub

### 4.1 Đăng ký tài khoản Vercel bằng GitHub

Truy cập [vercel.com](https://vercel.com), chọn **"Sign in with GitHub"** — Vercel sẽ yêu cầu quyền truy cập vào các repository GitHub của bạn để có thể đọc code và tự động deploy sau này.

### 4.2 Import Repository từ GitHub vào Vercel Dashboard

Trong Vercel Dashboard, chọn **"Add New... → Project"**, sau đó chọn đúng repository vừa push ở Phần 3 để import vào.

### 4.3 Vercel tự động nhận diện dự án Vite/React

Vercel đọc file `package.json` trong repository và **tự động nhận diện** đây là 1 dự án Vite, từ đó tự đề xuất sẵn cấu hình Build Command và Output Directory phù hợp (sẽ kiểm tra kỹ ở Phần 5) — thường không cần chỉnh sửa thủ công với 1 dự án Vite tiêu chuẩn.

---

## Phần 5: Deploy Dự Án Lên Vercel — Build Command & Output Directory

### 5.1 Kiểm tra và chỉnh Build Command

Trong phần cấu hình project trên Vercel, đảm bảo **Build Command** đúng bằng lệnh đã dùng ở Phần 2.1:

```text
Build Command: pnpm run build
```

Nếu Vercel tự nhận diện sai (ví dụ mặc định `npm run build` trong khi dự án dùng `pnpm`), cần chỉnh lại thủ công cho khớp với `package.json` của dự án.

### 5.2 Kiểm tra Output Directory đúng là `dist`

```text
Output Directory: dist
```

Đây chính là thư mục đã giải thích ở Phần 2.1 — Vercel sẽ lấy toàn bộ nội dung trong `dist/` để phục vụ cho người dùng cuối. Nếu khai báo sai tên thư mục, Vercel sẽ báo lỗi không tìm thấy file để deploy.

### 5.3 Theo dõi quá trình Deploy qua Log và nhận link demo

Sau khi bấm **"Deploy"**, Vercel hiển thị Log build theo thời gian thực (tương tự chạy `pnpm run build` ngay trên terminal của bạn). Khi hoàn tất, Vercel cấp cho dự án 1 đường link dạng `https://<project-name>.vercel.app` để truy cập ứng dụng đã deploy thành công.

---

## Phần 6: Xử Lý Lỗi Thường Gặp Khi Deploy React Router

### 6.1 Nguyên nhân lỗi 404 khi refresh trang

Nhớ lại Bài 8 (Phần 1.1): React Router chỉ **giả lập** việc đổi "trang" bằng JavaScript, thực chất toàn bộ ứng dụng chỉ có **đúng 1 file `index.html`**. Khi người dùng đang ở URL `https://your-app.vercel.app/products/5` rồi nhấn **F5 refresh** (hoặc gõ thẳng URL đó vào trình duyệt), trình duyệt gửi request thật sự lên server để tìm file tại đường dẫn `/products/5` — nhưng server (Vercel) **không có file vật lý nào tên như vậy** (nó chỉ tồn tại "ảo" trong JavaScript của React Router), nên trả về lỗi **404**.

### 6.2 Tạo file `vercel.json` với cấu hình `rewrites`

```json
// vercel.json (đặt ở thư mục gốc của dự án, ngang hàng với package.json)
{
  "rewrites": [
    { "source": "/(.*)", "destination": "/index.html" }
  ]
}
```

Cấu hình này báo cho Vercel: **bất kỳ đường dẫn nào** không khớp file tĩnh thật sự (JS, CSS, ảnh...) đều trả về `index.html` — sau đó chính `index.html` sẽ tải React lên, và React Router (đọc đúng URL hiện tại trên trình duyệt) sẽ tự hiển thị đúng Component tương ứng, y hệt hành vi khi điều hướng bằng `Link` (Bài 8, Phần 4.1).

### 6.3 Deploy lại và kiểm tra refresh trang ở route con

Sau khi thêm `vercel.json`, commit và push code (Phần 3.2) — Vercel sẽ tự động deploy lại phiên bản mới (cơ chế Continuous Deployment, giải thích kỹ ở Phần 8.2). Truy cập lại 1 route con (ví dụ `/products/5`) và thử F5 — lần này trang tải đúng bình thường, không còn lỗi 404.

---

## Phần 7: Cấu Hình Biến Môi Trường Trên Vercel

### 7.1 Khai báo biến trong Project Settings

Vào **Project Settings → Environment Variables** trên Vercel Dashboard, thêm đúng các biến đã khai báo trong `.env` ở Phần 1.2 (ví dụ `VITE_API_BASE_URL`) — vì `.env` đã bị loại khỏi Git (Phần 1.3), Vercel **không hề biết** các giá trị này trừ khi bạn khai báo thủ công tại đây.

### 7.2 Phân biệt môi trường Production/Preview/Development

Vercel cho phép gán 1 biến môi trường cho từng loại môi trường riêng biệt:

- **Production**: áp dụng cho bản deploy chính thức (nhánh `main`).
- **Preview**: áp dụng cho các bản deploy thử từ Pull Request (Phần 8.3) — thường trỏ đến API môi trường staging/test.
- **Development**: áp dụng khi chạy `vercel dev` cục bộ (ít dùng trong phạm vi khoá học này).

Nhờ đó, ứng dụng ở môi trường Preview có thể gọi API test khác hoàn toàn với API thật ở Production, tránh ảnh hưởng dữ liệu thật khi đang thử nghiệm tính năng mới.

### 7.3 Redeploy lại dự án để biến môi trường mới có hiệu lực

> ⚠️ Biến môi trường được **nhúng vào code ngay lúc build** (Phần 2.1), không phải đọc động lúc chạy. Vì vậy sau khi thêm/sửa biến môi trường trên Vercel Dashboard, phải **redeploy lại** dự án (vào tab Deployments, chọn bản deploy gần nhất, bấm "Redeploy") thì thay đổi mới thực sự có hiệu lực trên ứng dụng đang chạy.

---

## Phần 8: Custom Domain và Tự Động Deploy (CI/CD Cơ Bản)

### 8.1 Gắn Custom Domain riêng

Nếu đã sở hữu 1 tên miền riêng, vào **Project Settings → Domains** trên Vercel, thêm tên miền đó và cấu hình lại bản ghi DNS theo hướng dẫn Vercel cung cấp (thường là thêm 1 bản ghi `CNAME` hoặc `A` trỏ về Vercel) — sau khi DNS cập nhật xong (có thể mất vài phút đến vài giờ), ứng dụng sẽ truy cập được qua tên miền riêng thay vì `*.vercel.app`.

### 8.2 Cơ chế tự động Deploy khi Push code mới (Continuous Deployment)

Từ lúc project đã được kết nối với GitHub (Phần 4.2), **mọi lần `git push` lên nhánh `main`** đều tự động kích hoạt 1 lượt build và deploy mới trên Vercel — hoàn toàn không cần thao tác thủ công lặp lại các bước ở Phần 5. Đây chính là mô hình CI/CD (Continuous Integration/Continuous Deployment) cơ bản nhất: code mới → tự động kiểm tra build → tự động lên production.

### 8.3 Preview Deployment tự động cho mỗi Pull Request

Khi làm việc nhóm và tạo 1 Pull Request (thay vì push thẳng vào `main`), Vercel tự động tạo ra **1 bản deploy Preview riêng biệt** với đường link duy nhất cho đúng nhánh đó — cho phép cả nhóm xem trước tính năng đang phát triển ngay trên 1 URL thật, trước khi quyết định merge vào `main` (và từ đó mới kích hoạt deploy Production như Phần 8.2).

---

## Phần 9: Tổng Kết Best Practices Toàn Khoá Học và Capstone Project

### 9.1 Ôn lại nhanh các kỹ năng đã học qua 12 buổi

```text
Bài 1  → Nền tảng: React là gì, ESNext (arrow function, destructuring, spread, map/filter/reduce)
Bài 2  → Component đầu tiên, JSX cơ bản
Bài 3  → JSX biên dịch thành gì, Virtual DOM/Reconciliation, Props & Composition Pattern
Bài 4  → State với useState, Immutability, Event Handling
Bài 5  → Conditional Rendering, render danh sách với map()/key
Bài 6  → useEffect, Cleanup Function, Component Life Cycle
Bài 7  → Forms (Controlled/Uncontrolled), useRef, Custom Hook
Bài 8  → React Router: Nested Routes, useParams, Protected Route
Bài 9  → Global State với Zustand: Store, Selector Pattern, persist
Bài 10 → Data Fetching cơ bản: fetch/Axios, Race Condition
Bài 11 → TanStack Query: useQuery, useMutation, invalidateQueries
Bài 12 → Deploy: build, GitHub, Vercel, CI/CD
```

Một dự án thực tế hoàn chỉnh thường đi qua đúng dòng chảy này: dựng Component (Bài 2-3) → quản lý dữ liệu cục bộ (Bài 4-6) → xử lý input người dùng (Bài 7) → điều hướng nhiều trang (Bài 8) → chia sẻ dữ liệu toàn cục (Bài 9) → kết nối dữ liệu thật từ server (Bài 10-11) → đưa lên production cho người dùng thật sử dụng (Bài 12).

### 9.2 Checklist Best Practices

- **Cấu trúc thư mục**: tách rõ `components/`, `pages/`, `hooks/`, `store/`, `lib/` — không dồn hết logic vào 1 file `App.tsx` khổng lồ.
- **Tách Component/Hook**: Component chỉ nên tập trung vào JSX (UI), logic phức tạp nên tách ra Custom Hook (Bài 7) để dễ test và tái sử dụng.
- **Quản lý State hợp lý**: dùng đúng công cụ cho đúng loại dữ liệu — `useState` cho State cục bộ (Bài 4), Zustand cho Global State thực sự cần chia sẻ (Bài 9), TanStack Query cho Server State (Bài 11) — không nhồi mọi thứ vào 1 chỗ.
- **Kiểu dữ liệu rõ ràng**: tận dụng TypeScript (`interface`/`type` đã dùng xuyên suốt khoá học) để bắt lỗi sớm ngay lúc viết code, thay vì đợi chạy thử mới phát hiện.
- **Bảo mật cơ bản**: không commit `.env` (Phần 1.3), luôn cấu hình biến môi trường đúng chỗ trên Vercel (Phần 7).

### 9.3 Công bố đề Capstone Project

Capstone Project là bài tập tổng hợp toàn bộ kiến thức 12 buổi, làm tại nhà và nộp sau buổi học cuối — yêu cầu và tiêu chí chấm điểm cụ thể sẽ được công bố trực tiếp trong buổi học, dựa trên đúng checklist Best Practices ở Phần 9.2 và toàn bộ kỹ năng đã tổng kết ở Phần 9.1.

---

## 🧪 Bài tập thực hành cuối buổi

1. Chạy `pnpm run build` cho 1 dự án đã làm ở các bài trước, sau đó `pnpm run preview` và kiểm tra ứng dụng hoạt động đúng ở chế độ production trước khi deploy thật.
2. Đưa dự án đó lên 1 repository GitHub mới, đảm bảo `.gitignore` đã loại trừ đúng `.env` và `node_modules` (Phần 1.3) trước khi commit.
3. Deploy dự án lên Vercel theo đúng quy trình Phần 4-5, sau đó thêm `vercel.json` để xử lý lỗi 404 với React Router (Phần 6) — kiểm chứng bằng cách refresh trực tiếp ở 1 route con.
4. Thêm ít nhất 1 biến môi trường `VITE_...` vào Vercel Dashboard (Phần 7), redeploy lại, và xác nhận ứng dụng đọc đúng giá trị đó trên bản production (ví dụ hiển thị `import.meta.env.VITE_APP_NAME` ra giao diện để kiểm tra).

## 🧪 Bài tập Homework
