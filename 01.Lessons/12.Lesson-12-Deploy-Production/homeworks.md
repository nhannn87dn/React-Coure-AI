# 📝 Bài Tập Về Nhà — Bài 12: Deploy Ứng Dụng React Với Vercel

> Mục tiêu: Deploy hoàn chỉnh Mini Shop lên Vercel, xử lý đúng biến môi trường và lỗi 404 khi refresh.

---

## Bài 1 — Deploy Mini Shop lên Vercel

**1. Yêu cầu:**

- Rà soát dự án Mini Shop (đã hoàn thiện qua Bài 8-11): xóa `console.log` thừa, xác nhận `.env` không bị commit lên Git (mục 1.3).
- Chạy `pnpm run build` cục bộ, sửa hết lỗi TypeScript nếu có, xác nhận `pnpm run preview` chạy đúng trước khi deploy.
- Đẩy code lên 1 GitHub Repository, import vào Vercel, deploy thành công.
- Cấu hình đầy đủ Environment Variables trên Vercel (ví dụ `VITE_API_URL`), redeploy để có hiệu lực (mục 8.3).
- Thêm file `vercel.json` với `rewrites` (mục 7.2), xác nhận refresh ở route con (ví dụ trang chi tiết sản phẩm) không còn lỗi 404.
- **Nộp bài:** gửi kèm cả link demo (`https://<project>.vercel.app`) và link GitHub Repository.

---

## Bài 2 — Bắt đầu Capstone Project

Xem đề bài đầy đủ, yêu cầu tối thiểu và tiêu chí chấm điểm tại [capstone-project.md](capstone-project.md).
