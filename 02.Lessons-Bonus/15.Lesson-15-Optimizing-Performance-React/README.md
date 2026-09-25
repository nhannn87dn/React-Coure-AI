# ⭐ Bài 15 (Bonus): Tối Ưu Hiệu Năng Ứng Dụng React

> 🎯 Mục tiêu: Đo được hiệu năng bằng công cụ (Lighthouse, Core Web Vitals, React DevTools Profiler) trước khi tối ưu, tìm và xử lý được các nguyên nhân re-render thừa phổ biến, áp dụng được `useTransition`/`useDeferredValue` và virtualization cho giao diện nặng, tối ưu được tốc độ tải trang (bundle, ảnh, dữ liệu), và bật được React Compiler.
>
> 📖 Bài này là tài liệu **tự học**, không dạy trên lớp và không có bài tập/homework nộp.
>
> 🔁 Kiến thức cần nắm trước: `memo`/`useMemo`/`useCallback` và Profiler (Bài 9), Selector Zustand + `useShallow` (Bài 9), cache của TanStack Query (Bài 11), `React.lazy` + build production (Bài 12).

---

## Phần 1: Đo Trước, Tối Ưu Sau

### 1.0 Nhắc lại nhanh

Ở Bài 9, ta đã học nguyên tắc "đo trước, tối ưu sau" và làm quen với `React.memo`/`useMemo`/`useCallback`. Bài này đi sâu hơn: đo bằng công cụ cụ thể nào, những nguyên nhân re-render thừa phổ biến nhất, và các kỹ thuật tối ưu nằm ngoài phạm vi `memo` (Concurrent Rendering, virtualization, tối ưu tải trang).

### 1.1 Hai kiểu "chậm"

> **"Chậm" trong 1 ứng dụng web thực ra là 2 vấn đề khác nhau: (1) tải trang chậm — người dùng phải chờ lâu mới THẤY được nội dung, và (2) tương tác bị giật — trang đã hiển thị nhưng phản hồi trễ khi gõ phím/cuộn/click.** Cách đo và cách tối ưu cho 2 vấn đề này khác nhau — cần xác định rõ đang gặp vấn đề nào trước khi bắt tay tối ưu.

### 1.2 Core Web Vitals

- **LCP (Largest Contentful Paint):** thời gian để phần tử **lớn nhất** trên màn hình (thường là ảnh banner hoặc tiêu đề chính) hiển thị xong — đo cho vấn đề (1).
- **INP (Interaction to Next Paint):** thời gian từ lúc người dùng thao tác (click, gõ phím) đến lúc giao diện phản hồi — đo cho vấn đề (2).
- **CLS (Cumulative Layout Shift):** mức độ bố cục trang bị "nhảy" bất ngờ (ví dụ ảnh tải xong đẩy nội dung khác xuống) — ảnh hưởng trải nghiệm dù không hẳn là "chậm".

### 1.3 Đo bằng Lighthouse trên bản build production

```bash
pnpm run build && pnpm run preview # Đã học ở Bài 12
```

> ⚠️ **Luôn đo hiệu năng trên bản `preview` (build production), KHÔNG đo trên `pnpm run dev`.** Môi trường dev cố tình đánh đổi tốc độ chạy để lấy tốc độ code nhanh hơn (HMR, không minify — đã học ở Bài 1, Bài 12) — số liệu đo được ở dev **không phản ánh đúng** trải nghiệm người dùng thật sẽ gặp.

Mở Chrome DevTools → tab **Lighthouse** → chọn "Performance" → Analyze page load, chạy trên URL của `pnpm run preview`.

---

## Phần 2: Tìm Re-render Thừa Với React DevTools Profiler

### 2.1 Ghi phiên Profiler

Mở tab **Profiler** trong React DevTools (đã cài từ Bài 4, dùng lần đầu ở Bài 9) → bật "Record why each component rendered" trong phần cài đặt (⚙️) → nhấn nút tròn để bắt đầu ghi → thao tác với ứng dụng → dừng ghi. Biểu đồ **Flamegraph** hiển thị cây Component theo từng "commit" (mỗi lần React cập nhật DOM thật), biểu đồ **Ranked** liệt kê Component theo thời gian render giảm dần — dùng để nhanh chóng tìm ra Component nào tốn thời gian nhất.

### 2.2 Nguyên nhân phổ biến

- **State đặt quá cao:** State chỉ 1 Component con sâu bên trong cần dùng, nhưng lại đặt ở Component cha trên cùng — mọi Component nằm giữa đường đi đều re-render theo, dù không thực sự liên quan (đã thấy dạng nhẹ của vấn đề này ở Bài 9, Phần 7.1).
- **Object/function tạo mới mỗi lần render:** truyền `style={{ color: 'red' }}` hoặc `onClick={() => ...}` viết trực tiếp trong JSX — Props luôn "trông khác" ở mỗi lần render, khiến `React.memo` (Bài 9) ở Component con mất tác dụng.
- **`value` của Context thay đổi:** mọi Component gọi `useContext` re-render theo, dù chỉ cần 1 phần rất nhỏ của `value` (đã học ở Bài 9, mục 3.1).

### 2.3 Tối ưu không cần memo: "Move State Down"

```tsx
// ❌ State đặt ở cha - mỗi lần gõ, TOÀN BỘ Sidebar (không liên quan ô input) re-render theo
function App() {
  const [keyword, setKeyword] = useState('');
  return (
    <div>
      <input value={keyword} onChange={(e) => setKeyword(e.target.value)} />
      <ExpensiveSidebar />
    </div>
  );
}
```

```tsx
// ✅ Đúng - tách ô input thành 1 Component riêng, State "keyword" không còn ở App nữa
function App() {
  return (
    <div>
      <SearchInput />
      <ExpensiveSidebar /> {/* Không còn nằm cùng cây re-render với SearchInput */}
    </div>
  );
}

function SearchInput() {
  const [keyword, setKeyword] = useState('');
  return <input value={keyword} onChange={(e) => setKeyword(e.target.value)} />;
}
```

**Move State Down** (đẩy State xuống đúng Component thực sự cần nó) thường đơn giản và hiệu quả hơn nhiều so với thêm `memo` — đây luôn là bước nên thử **trước tiên**. Kỹ thuật liên quan: truyền Component "nặng" qua `children` (Composition Pattern — Bài 3) để nó không nằm trong cây re-render của Component cha giữ State.

---

## Phần 3: Memoization Đúng Cách Và React Compiler

### 3.0 Ôn nhanh và các trường hợp dùng sai

Đã học ở Bài 9: `React.memo` bỏ qua re-render khi Props không đổi, `useMemo` ghi nhớ kết quả tính toán, `useCallback` giữ nguyên tham chiếu function. Lỗi phổ biến nhất (nhắc lại từ Bài 9, mục 7.2): `React.memo` **vô dụng** nếu Props truyền vào vẫn là object/function mới mỗi lần — phải dùng kèm `useMemo`/`useCallback` ở Component cha.

### 3.1 Context Splitting

```tsx
// ❌ 1 Context duy nhất gộp cả dữ liệu ít đổi và hay đổi
const AppContext = createContext<{ user: User; theme: Theme; notifications: Notification[] } | null>(null);
```

```tsx
// ✅ Tách theo tần suất thay đổi - Component chỉ cần "theme" không re-render khi "notifications" đổi
const UserContext = createContext<User | null>(null);
const ThemeContext = createContext<Theme | null>(null);
const NotificationContext = createContext<Notification[] | null>(null);
```

**Context Splitting** giải quyết đúng vấn đề re-render thừa của Context đã học ở Bài 9 (mục 3.1): tách 1 Context lớn thành nhiều Context nhỏ theo tần suất thay đổi, để Component chỉ `useContext` đúng phần mình cần. Với Zustand, vấn đề này vốn đã được giải quyết sẵn qua **Selector Pattern + `useShallow`** (Bài 9, Phần 5) — đây cũng là 1 lý do Zustand phù hợp hơn Context cho dữ liệu thay đổi liên tục.

### 3.2 React Compiler

```bash
pnpm add -D babel-plugin-react-compiler
```

```ts
// vite.config.ts
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [
    react({
      babel: { plugins: ['babel-plugin-react-compiler'] },
    }),
  ],
});
```

> **React Compiler tự động phân tích code và chèn memoization tương đương `useMemo`/`useCallback`/`React.memo` vào lúc build — không cần tự viết tay.** Sau khi bật, mở lại React DevTools Profiler (mục 2.1) để so sánh: các Component trước đây re-render thừa (mục 2.2) giờ được bỏ qua tự động.

Để Compiler hoạt động đúng, code cần tuân thủ nghiêm ngặt **Rules of Hooks** (Bài 4, Bài 7) và **nguyên tắc bất biến của State/Props** (không mutate trực tiếp — Bài 4) — Compiler dựa vào các quy tắc này để suy luận an toàn khi nào có thể bỏ qua re-render.

---

## Phần 4: Giữ Giao Diện Mượt Với Concurrent Rendering

### 4.1 `useTransition`

```tsx
function ProductSearch({ products }: { products: Product[] }) {
  const [keyword, setKeyword] = useState('');
  const [filtered, setFiltered] = useState(products);
  const [isPending, startTransition] = useTransition();

  const handleChange = (value: string) => {
    setKeyword(value); // Cập nhật NGAY - ô input không bị giật
    startTransition(() => {
      setFiltered(products.filter((p) => p.name.includes(value))); // Đánh dấu "không khẩn cấp"
    });
  };

  return (
    <div>
      <input value={keyword} onChange={(e) => handleChange(e.target.value)} />
      {isPending && <p>Đang lọc...</p>}
      <ProductGrid products={filtered} />
    </div>
  );
}
```

> **`useTransition` cho phép đánh dấu 1 lần cập nhật State là "không khẩn cấp" — React ưu tiên xử lý các cập nhật khẩn cấp khác (như hiển thị ký tự vừa gõ vào ô input) trước, rồi mới xử lý phần được đánh dấu `startTransition` khi rảnh.** Với danh sách lớn cần lọc lại theo mỗi ký tự gõ vào, cách này giữ ô input phản hồi mượt dù việc lọc danh sách tốn thời gian.

### 4.2 `useDeferredValue`

```tsx
function ProductSearch({ products }: { products: Product[] }) {
  const [keyword, setKeyword] = useState('');
  const deferredKeyword = useDeferredValue(keyword); // Giá trị "trễ" cho phần render nặng

  const filtered = products.filter((p) => p.name.includes(deferredKeyword));

  return (
    <div>
      <input value={keyword} onChange={(e) => setKeyword(e.target.value)} />
      <ProductGrid products={filtered} />
    </div>
  );
}
```

`useDeferredValue` giải quyết vấn đề tương tự `useTransition` nhưng theo hướng khác: thay vì đánh dấu **hành động cập nhật** là không khẩn cấp, nó tạo ra **1 phiên bản "trễ"** của chính giá trị đó — phần UI dùng `deferredKeyword` có thể "chậm theo" 1 nhịp so với `keyword` thật, để nhường ưu tiên render cho phần hiển thị `keyword` (ô input) trước.

> 💡 So sánh với `useDebounce` (Custom Hook tự viết ở Bài 7): `useDebounce` trì hoãn theo **thời gian** (chờ hết X mili-giây mới cập nhật) — phù hợp khi cần giảm số lần gọi API. `useDeferredValue` trì hoãn theo **độ ưu tiên render** của chính React (không có mốc thời gian cố định) — phù hợp khi vấn đề là 1 phép tính/render nặng ở phía client, không phải số lần gọi mạng.

---

## Phần 5: Render Danh Sách Lớn Với Virtualization

### 5.1 Vì sao render hàng nghìn phần tử vẫn chậm dù đã có Virtual DOM

Virtual DOM và Reconciliation (Bài 5) giúp React chỉ **cập nhật** đúng phần Real DOM đã thay đổi — nhưng nếu danh sách có **10.000 phần tử hiển thị cùng lúc**, dù không có gì thay đổi, trình duyệt vẫn phải giữ **10.000 node DOM thật** trong bộ nhớ và tính toán layout cho tất cả — bản thân số lượng node Real DOM quá lớn mới là vấn đề, không phải do Reconciliation kém hiệu quả.

### 5.2 Ý tưởng Windowing

> **Virtualization (windowing) chỉ render những phần tử đang thực sự nằm trong vùng nhìn thấy (viewport) của người dùng, cộng thêm 1 vùng đệm nhỏ — các phần tử còn lại (phía trên/dưới màn hình) hoàn toàn không tồn tại trong Real DOM cho đến khi người dùng cuộn tới.**

### 5.3 `@tanstack/react-virtual`

```bash
pnpm add @tanstack/react-virtual
```

```tsx
import { useVirtualizer } from '@tanstack/react-virtual';
import { useRef } from 'react'; // useRef - đã học ở Bài 7

function VirtualProductList({ products }: { products: Product[] }) {
  const parentRef = useRef<HTMLDivElement>(null);

  const virtualizer = useVirtualizer({
    count: products.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 80, // Ước lượng chiều cao mỗi item (px)
  });

  return (
    <div ref={parentRef} style={{ height: 600, overflow: 'auto' }}>
      <div style={{ height: virtualizer.getTotalSize(), position: 'relative' }}>
        {virtualizer.getVirtualItems().map((item) => (
          <div
            key={item.key} // key - đã học ở Bài 5
            style={{ position: 'absolute', top: item.start, height: item.size, width: '100%' }}
          >
            <ProductCard product={products[item.index]} />
          </div>
        ))}
      </div>
    </div>
  );
}
```

Dù `products` có 10.000 phần tử, `virtualizer.getVirtualItems()` chỉ trả về đúng số phần tử đang hiển thị (thường vài chục) — số node Real DOM thực tế luôn nhỏ và cố định, bất kể tổng số phần tử trong dữ liệu là bao nhiêu.

---

## Phần 6: Tối Ưu Tải Trang — Bundle, Ảnh Và Dữ Liệu

### 6.1 Phân tích bundle

```bash
pnpm add -D rollup-plugin-visualizer
```

```ts
// vite.config.ts
import { visualizer } from 'rollup-plugin-visualizer';

export default defineConfig({
  plugins: [react(), visualizer({ open: true })],
});
```

Chạy `pnpm run build`, `rollup-plugin-visualizer` mở ra 1 biểu đồ trực quan hiển thị **kích thước từng thư viện** trong bundle — dễ dàng phát hiện 1 thư viện vô tình chiếm quá nhiều dung lượng. Nhắc lại từ Bài 12 (mục 2.4): luôn kiểm tra cảnh báo dung lượng file mà Vite in ra sau khi build.

```ts
// ❌ Import toàn bộ thư viện - cản trở tree-shaking
import _ from 'lodash';

// ✅ Import đúng phần cần dùng
import debounce from 'lodash/debounce';
```

**Tree-shaking** là khả năng công cụ build (Vite/Rollup) tự loại bỏ code không dùng đến khỏi bundle cuối cùng — chỉ hoạt động tốt khi import đúng cách như ví dụ trên.

### 6.2 Code splitting nâng cao: preload khi hover

```tsx
// Nhắc lại React.lazy từ Bài 12
const ProductDetailPage = lazy(() => import('./pages/ProductDetailPage'));

function ProductLink({ id }: { id: number }) {
  return (
    <Link
      to={`/products/${id}`}
      onMouseEnter={() => import('./pages/ProductDetailPage')} // Tải trước file JS khi rê chuột vào
    >
      Xem chi tiết
    </Link>
  );
}
```

Gọi `import(...)` thêm 1 lần nữa lúc `onMouseEnter` không tạo ra 2 bản sao — trình duyệt đã cache file JS đó, nên khi người dùng thực sự click, trang chi tiết hiển thị **ngay lập tức** thay vì phải chờ tải (đúng khoảng thời gian từ lúc hover đến lúc click).

### 6.3 Tối ưu ảnh

```tsx
<img src="/banner.webp" width={1200} height={400} loading="lazy" alt="Banner" />
```

- `loading="lazy"`: trình duyệt tự trì hoãn tải ảnh nằm ngoài vùng nhìn thấy — cùng ý tưởng với Virtualization (Phần 5) nhưng áp dụng riêng cho ảnh.
- Khai báo sẵn `width`/`height`: trình duyệt dành sẵn đúng khoảng không gian cho ảnh trước khi tải xong, tránh **CLS** (Phần 1.2) — bố cục không bị "nhảy" khi ảnh load xong.
- Định dạng **WebP/AVIF**: dung lượng nhỏ hơn đáng kể so với JPEG/PNG ở cùng chất lượng hiển thị.

### 6.4 Tối ưu dữ liệu với `staleTime` và `prefetchQuery`

```tsx
// staleTime - Query không tự động gọi lại API nếu dữ liệu còn "mới" trong khoảng thời gian này
useQuery({
  queryKey: ['products'],
  queryFn: fetchProducts,
  staleTime: 60 * 1000, // 1 phút - trong 1 phút này, mọi lần dùng lại Query không gọi API
});
```

```tsx
// prefetchQuery - tải trước dữ liệu trang chi tiết khi hover vào link, giống ý tưởng mục 6.2
const queryClient = useQueryClient(); // Bài 11

<Link
  to={`/products/${id}`}
  onMouseEnter={() => {
    queryClient.prefetchQuery({
      queryKey: ['products', id],
      queryFn: () => fetchProduct(id),
    });
  }}
>
  Xem chi tiết
</Link>
```

Nhắc lại từ Bài 11: TanStack Query mặc định luôn coi dữ liệu là "cũ" ngay sau khi tải xong trừ khi chỉ định `staleTime`. Kết hợp `staleTime` (giảm số lần gọi lại API không cần thiết) và `prefetchQuery` (tải trước dữ liệu trước khi người dùng thực sự cần) giúp cảm giác chuyển trang gần như tức thời.

---

## Phần 7: Ví Dụ Tổng Hợp — Tối Ưu Một Trang Danh Sách Chậm

### 7.1 Điểm xuất phát: code "trước khi tối ưu"

```tsx
// ❌ TRƯỚC: State đặt ở cha, Context ôm cả dữ liệu ít đổi lẫn hay đổi, render thẳng 10.000 phần tử
const AppContext = createContext<{ user: User; products: Product[] } | null>(null);

function App() {
  const [keyword, setKeyword] = useState(''); // Đặt quá cao - Sidebar re-render theo mỗi lần gõ
  const [user] = useState<User>({ id: 1, name: 'An' });
  const products = useProducts(); // 10.000 sản phẩm từ useQuery (Bài 11)

  const filtered = products.data?.filter((p) => p.name.includes(keyword)) ?? [];

  return (
    <AppContext.Provider value={{ user, products: filtered }}>
      <input value={keyword} onChange={(e) => setKeyword(e.target.value)} />
      <ExpensiveSidebar /> {/* Không liên quan "keyword" nhưng vẫn re-render theo App */}
      <ul>
        {filtered.map((p) => <li key={p.id}>{p.name}</li>)} {/* Render thẳng 10.000 <li> */}
      </ul>
    </AppContext.Provider>
  );
}
```

**Đo ban đầu (giả định):** Lighthouse Performance 45 điểm, INP > 300ms khi gõ tìm kiếm, Profiler cho thấy `ExpensiveSidebar` re-render ở mọi ký tự gõ.

### 7.2 Sau khi sửa — từng bước

**Bước 1 — Move State Down (Phần 2.3):** tách `keyword` ra khỏi `App`, đặt vào 1 Component `SearchInput` riêng — `ExpensiveSidebar` không còn nằm cùng cây re-render.

**Bước 2 — `useDeferredValue` (Phần 4.2):** phần lọc `filtered` dùng `deferredKeyword` thay vì `keyword` trực tiếp, để việc gõ phím không bị chặn bởi phép `filter()` trên 10.000 phần tử.

**Bước 3 — Virtualization (Phần 5.3):** thay `<ul>{filtered.map(...)}</ul>` bằng `VirtualProductList` dùng `useVirtualizer` — Real DOM chỉ còn vài chục `<li>` thay vì 10.000.

**Bước 4 — Lazy route + prefetch (Phần 6.2, 6.4):** trang chi tiết sản phẩm tải bằng `React.lazy`, prefetch dữ liệu khi hover vào link.

```tsx
// ✅ SAU
function App() {
  return (
    <AppContext.Provider value={{ user }}> {/* Tách "products" ra khỏi Context - không còn đổi liên tục */}
      <SearchInput /> {/* State "keyword" chỉ còn ở đây */}
      <ExpensiveSidebar /> {/* Không còn re-render theo tìm kiếm */}
    </AppContext.Provider>
  );
}

function SearchInput() {
  const [keyword, setKeyword] = useState('');
  const deferredKeyword = useDeferredValue(keyword);
  const products = useProducts();
  const filtered = products.data?.filter((p) => p.name.includes(deferredKeyword)) ?? [];

  return (
    <div>
      <input value={keyword} onChange={(e) => setKeyword(e.target.value)} />
      <VirtualProductList products={filtered} />
    </div>
  );
}
```

**Đo lại (giả định):** Lighthouse Performance 88 điểm, INP < 100ms khi gõ tìm kiếm, Profiler xác nhận `ExpensiveSidebar` không còn re-render khi gõ.

### 7.3 Áp dụng cho Capstone Project của chính bạn

Lặp lại đúng quy trình 4 bước trên (đo → move state down → memoization/Concurrent Rendering → virtualization/tải trang → đo lại) cho trang nặng nhất trong Capstone Project (Bài 12) — thường là trang danh sách có nhiều dữ liệu hoặc nhiều Component cùng lúc.

---

## 📚 Tài Liệu Tham Khảo

- [react.dev — React Compiler](https://react.dev/learn/react-compiler)
- [react.dev — useTransition](https://react.dev/reference/react/useTransition)
- [react.dev — useDeferredValue](https://react.dev/reference/react/useDeferredValue)
- [react.dev — React DevTools Profiler](https://react.dev/reference/react/Profiler)
- [web.dev — Core Web Vitals](https://web.dev/articles/vitals)
- [web.dev — Lighthouse](https://developer.chrome.com/docs/lighthouse/overview)
- [TanStack Virtual](https://tanstack.com/virtual/latest)
- [TanStack Query — Prefetching & Router Integration](https://tanstack.com/query/latest/docs/framework/react/guides/prefetching)
