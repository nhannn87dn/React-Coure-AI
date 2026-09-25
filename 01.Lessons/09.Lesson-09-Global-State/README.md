# ⭐ Bài 9: Quản Lý Global State Với Zustand

> 🎯 Mục tiêu: Học viên giải thích được vấn đề Prop Drilling, sử dụng được Context API và Zustand (có kiểu TypeScript) để quản lý Global State, áp dụng được Selector Pattern/`persist`, và biết khi nào cần tối ưu re-render bằng `memo`/`useMemo`/`useCallback`.

---

## Phần 1: Vấn Đề Prop Drilling

### 1.0 Nhắc lại nhanh

Ở Bài 5, ta đã học **Lifting State Up**: khi 2 Component cần chung dữ liệu, nâng State lên Component cha gần nhất, truyền xuống qua Props. Cách này hoạt động tốt khi cây Component **nông** (2-3 cấp). Nhưng nếu dữ liệu cần dùng ở 1 Component nằm **sâu 4-5 cấp**, mà các Component ở giữa hoàn toàn không cần dùng đến nó, ta sẽ gặp vấn đề mới.

### 1.1 Ví dụ thực tế: Lifting State Up quá cao

```tsx
type User = { id: number; name: string };

function App() {
  const [user] = useState<User>({ id: 1, name: 'An' });
  return <Header user={user} />; // App không dùng "user", chỉ truyền tiếp
}

function Header({ user }: { user: User }) {
  return <Toolbar user={user} />; // Header cũng không dùng "user", chỉ truyền tiếp
}

function Toolbar({ user }: { user: User }) {
  return <UserMenu user={user} />; // Toolbar cũng không dùng, chỉ truyền tiếp
}

function UserMenu({ user }: { user: User }) {
  return <p>Xin chào, {user.name}</p>; // Chỉ Component NÀY thực sự cần "user"
}
```

> **Prop Drilling là hiện tượng phải "khoan xuyên" 1 Props qua nhiều tầng Component trung gian không hề dùng đến nó, chỉ để đưa dữ liệu tới đúng Component thực sự cần ở tầng sâu hơn.**

### 1.2 Vì sao Prop Drilling gây khó bảo trì

- Mỗi Component trung gian (`Header`, `Toolbar`) **phải khai báo Props `user`** dù bản thân nó không dùng — chỉ để "chuyển tiếp", làm phình to `type` Props và code không liên quan đến chức năng thật của Component đó.
- Muốn đổi tên hoặc cấu trúc của `User`, phải sửa **mọi Component trung gian** trên đường đi, dù chúng không thực sự quan tâm đến `user`.
- Khi dự án lớn dần, thêm 1 Component trung gian mới ở giữa đường đi đồng nghĩa phải nhớ "khoan" tiếp Props đó qua Component mới — rất dễ quên và gây lỗi.

---

## Phần 2: Giải Pháp "Vanilla" — Context API

### 2.0 Định nghĩa

> **Context API là cơ chế có sẵn của React, cho phép 1 giá trị được "phát" (broadcast) xuống TOÀN BỘ cây Component bên dưới nó, và bất kỳ Component con nào (dù sâu bao nhiêu tầng) cũng có thể "đọc" trực tiếp giá trị đó mà không cần nhận qua Props.**

### 2.1 Tạo Context có kiểu và bọc cây Component bằng Provider

```tsx
// context/AuthContext.tsx
import { createContext } from 'react';

type User = { id: number; name: string };

type AuthContextType = {
  user: User | null;
  login: (user: User) => void;
  logout: () => void;
};

export const AuthContext = createContext<AuthContextType | null>(null);
```

```tsx
// App.tsx
function App() {
  const [user, setUser] = useState<User | null>(null);

  const login = (user: User) => setUser(user);
  const logout = () => setUser(null);

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      <Header /> {/* Header và mọi Component bên trong đều "nhìn thấy" được Context này */}
    </AuthContext.Provider>
  );
}
```

`<AuthContext.Provider value={...}>` bọc quanh 1 phần (hoặc toàn bộ) cây Component — mọi Component nằm **bên trong** cặp thẻ này đều có thể đọc `value` mà không cần đi qua Props, dù nằm sâu bao nhiêu tầng.

> 💡 Kiểu `AuthContextType | null` (Union Type — đã học ở Bài 1): giá trị khởi tạo của `createContext` là `null` vì tại thời điểm định nghĩa, chưa có `Provider` nào "cung cấp" giá trị thật — TypeScript buộc ta xử lý trường hợp này khi đọc Context (mục 2.2).

### 2.2 Đọc giá trị Context qua `useContext()`, đóng gói thành Custom Hook `useAuth()`

```tsx
import { useContext } from 'react';
import { AuthContext } from './AuthContext';

// Custom Hook (đã học ở Bài 7) - đóng gói useContext + kiểm tra null
function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth phải được gọi bên trong AuthContext.Provider');
  }
  return context;
}
```

```tsx
// UserMenu.tsx - Component nằm sâu 4 tầng dưới App, KHÔNG cần nhận Props "user"
function UserMenu() {
  const { user, logout } = useAuth(); // Đọc thẳng từ Context, không qua Header/Toolbar

  if (!user) return null;

  return (
    <div>
      <p>Xin chào, {user.name}</p>
      <button onClick={logout}>Đăng xuất</button>
    </div>
  );
}
```

So với mục 1.1: `Header` và `Toolbar` giờ **không cần nhận hay truyền tiếp Props `user`** nữa — Prop Drilling đã được giải quyết. Đóng gói `useContext(AuthContext)` + kiểm tra `null` vào 1 Custom Hook `useAuth()` (đúng kỹ thuật đã học ở Bài 7) giúp mọi nơi gọi Context đều tự động có kiểu dữ liệu đúng (không phải `AuthContextType | null` nữa, mà chắc chắn là `AuthContextType`), không cần lặp lại đoạn kiểm tra `null` ở mỗi Component.

---

## Phần 3: Hạn Chế Của Context Thuần Và So Sánh Với Zustand

### 3.1 Vấn đề re-render thừa

> **Bất kỳ Component nào gọi `useContext(SomeContext)` đều BẮT BUỘC re-render mỗi khi `value` của Provider đó thay đổi — dù Component đó chỉ thực sự cần MỘT phần rất nhỏ trong `value` ấy.**

```tsx
// AuthContext value chứa cả "user" và "theme"
<AuthContext.Provider value={{ user, theme, login, logout, toggleTheme }}>
```

Nếu 1 Component chỉ đọc `theme` (không quan tâm `user`), nó vẫn re-render mỗi khi `user` thay đổi — vì Context không có cách nào biết Component đó "chỉ cần 1 phần", nó luôn coi `value` là 1 khối duy nhất.

### 3.2 Code trở nên cồng kềnh khi cần nhiều loại State/logic phức tạp

Muốn thêm 1 loại dữ liệu Global State mới (ví dụ giỏ hàng), theo cách Context thuần phải lặp lại **toàn bộ quy trình**: tạo Context mới, tạo Provider mới, bọc thêm 1 lớp `<CartContext.Provider>` quanh cây Component, viết Custom Hook mới... Nếu ứng dụng cần 4-5 loại Global State khác nhau, cây Component sẽ bị bọc bởi rất nhiều lớp Provider lồng nhau.

### 3.3 Context phù hợp khi nào, Zustand phù hợp khi nào

| Tiêu chí | Context API (thuần) | Zustand |
|---|---|---|
| Tần suất State thay đổi | Phù hợp cho dữ liệu **ít thay đổi** (theme, ngôn ngữ) | Phù hợp cho dữ liệu **thay đổi liên tục** (giỏ hàng, form nhiều bước) |
| Kiểm soát re-render | Khó — mọi Component dùng Context đều re-render cùng lúc (mục 3.1) | Dễ — có sẵn Selector Pattern (Phần 5) để chỉ re-render đúng nơi cần |
| Cần bọc `<Provider>` | Có, phải bọc quanh cây Component | Không cần — gọi Hook trực tiếp ở bất kỳ đâu (Phần 5) |
| Độ phức tạp code khi thêm State mới | Tăng dần (mục 3.2) | Không đổi — chỉ thêm field vào Store có sẵn hoặc tạo Store mới độc lập |
| Middleware hỗ trợ (`persist`...) | Không có sẵn, phải tự viết | Có sẵn (Phần 6) |

**Quy tắc thực tế của khóa học:** dùng **Zustand** cho các loại Global State thay đổi thường xuyên và có nhiều Action đi kèm (giỏ hàng, trạng thái đăng nhập — Phần 8). Dùng **Context** cho dữ liệu hiếm khi đổi và không cần Action phức tạp (ví dụ theme sáng/tối — sẽ thực hành ở phần Homework).

---

## Phần 4: Cài Đặt Và Tạo Store Đầu Tiên Với Zustand

### 4.1 Cài đặt

```bash
pnpm add zustand
```

### 4.2 Tạo Store có kiểu bằng `create<T>()(...)`

```ts
// stores/useCartStore.ts
import { create } from 'zustand';

type CartItem = {
  id: number;
  name: string;
  price: number;
  quantity: number;
};

type CartState = {
  items: CartItem[];
  addItem: (item: Omit<CartItem, 'quantity'>) => void;
  removeItem: (id: number) => void;
};

export const useCartStore = create<CartState>()((set) => ({
  items: [],
  addItem: (item) =>
    set((state) => {
      const existing = state.items.find((i) => i.id === item.id);
      if (existing) {
        return {
          items: state.items.map((i) =>
            i.id === item.id ? { ...i, quantity: i.quantity + 1 } : i
          ),
        };
      }
      return { items: [...state.items, { ...item, quantity: 1 }] };
    }),
  removeItem: (id) =>
    set((state) => ({ items: state.items.filter((i) => i.id !== id) })),
}));
```

`create<CartState>()((set) => ({...}))` định nghĩa **cả State lẫn Action trong cùng 1 nơi** — khác hẳn `useState` (chỉ có State, Action phải viết riêng bên ngoài trong từng Component). Hàm `set` hoạt động tương tự Functional Update của `useState` (đã học ở Bài 4): nhận vào `state` hiện tại, trả về **phần State cần cập nhật** — Zustand tự động merge phần trả về vào State tổng, và bạn vẫn phải tuân thủ Immutability (spread, `map`, `filter` — đã học kỹ ở Bài 4) y hệt như với `useState`.

> 💡 Cú pháp `create<CartState>()(...)` có 2 cặp ngoặc liền nhau — đây là cách viết đặc thù của Zustand để TypeScript suy luận kiểu đúng cho `set`/`get`, không phải lỗi đánh máy.

### 4.3 Cấu trúc file Store chuẩn

```text
src/
└── stores/
    ├── useCartStore.ts
    ├── useAuthStore.ts
    └── useWishlistStore.ts
```

Mỗi loại Global State độc lập nên có **1 file Store riêng** — không cần gộp mọi thứ vào 1 Store khổng lồ, tương tự nguyên tắc "tách nhiều `useState` độc lập cho từng loại dữ liệu" đã học ở Bài 4.

---

## Phần 5: Đọc Và Cập Nhật State Trong Component Với `useStore`

### 5.1 Gọi Hook Store trực tiếp trong Component

```tsx
function CartSummary() {
  const items = useCartStore((state) => state.items); // Chỉ lấy "items"

  const total = items.reduce((sum, item) => sum + item.price * item.quantity, 0); // Derived State - Bài 5

  return <p>Tổng tiền: {total}đ</p>;
}
```

Khác biệt lớn nhất so với Context (Phần 2): `useCartStore(...)` gọi **trực tiếp** ở bất kỳ Component nào, **không cần** bọc `<Provider>` quanh cây Component — Zustand tự quản lý Store ở phạm vi toàn ứng dụng ngay từ khi file Store được import lần đầu.

### 5.2 Selector Pattern — chỉ lấy đúng phần State cần thiết

```tsx
// ✅ Đúng - Selector chỉ lấy "items", Component này KHÔNG re-render khi các field khác của Store đổi
const items = useCartStore((state) => state.items);

// ❌ Không khuyến khích - lấy nguyên object Store, Component re-render bất kể field nào đổi
const store = useCartStore((state) => state);
```

> **Selector là 1 hàm truyền vào Hook Store, chỉ định rõ "tôi cần đúng phần dữ liệu này" — Zustand chỉ re-render Component khi PHẦN DỮ LIỆU ĐÓ thay đổi, không quan tâm các field khác trong Store có đổi hay không.** Đây chính là giải pháp trực tiếp cho vấn đề re-render thừa của Context đã nêu ở mục 3.1.

### 5.3 Lỗi thường gặp: Selector trả về object/array mới mỗi lần

```tsx
// ❌ Sai - mỗi lần Component re-render, {} tạo ra 1 OBJECT MỚI (dù nội dung giống hệt)
const { items, total } = useCartStore((state) => ({
  items: state.items,
  total: state.items.reduce((sum, i) => sum + i.price * i.quantity, 0),
})); // Zustand thấy tham chiếu khác -> re-render LIÊN TỤC, dù dữ liệu không đổi thật
```

Giống hệt bài học Immutability ở Bài 4 (React so sánh State bằng tham chiếu, không phải nội dung): Selector trả về **1 object literal mới** (`{ items, total }`) ở **mọi lần gọi**, nên dù `items`/`total` bên trong không đổi, Zustand vẫn thấy "tham chiếu object khác" và kích hoạt re-render — có thể dẫn đến vòng lặp re-render vô ích.

```tsx
// ✅ Đúng - dùng useShallow để so sánh NÔNG (shallow) từng field bên trong, thay vì so cả object
import { useShallow } from 'zustand/react/shallow';

const { items, total } = useCartStore(
  useShallow((state) => ({
    items: state.items,
    total: state.items.reduce((sum, i) => sum + i.price * i.quantity, 0),
  }))
);
```

`useShallow` bọc quanh Selector, báo cho Zustand: "so sánh **từng field bên trong** object trả về, đừng so sánh cả object" — chỉ re-render khi có ít nhất 1 field thực sự đổi giá trị.

### 5.4 Gọi Action từ Store để cập nhật State

```tsx
function ProductCard({ product }: { product: { id: number; name: string; price: number } }) {
  const addItem = useCartStore((state) => state.addItem); // Lấy riêng Action, cũng qua Selector

  return (
    <div>
      <h3>{product.name}</h3>
      <button onClick={() => addItem(product)}>Thêm vào giỏ</button>
    </div>
  );
}
```

Action cũng được lấy ra qua Selector giống hệt State — và vì bản thân hàm Action (`addItem`) không đổi tham chiếu giữa các lần render (Zustand đảm bảo điều này), lấy riêng Action theo cách trên **không gây re-render thừa** dù không cần `useShallow`.

---

## Phần 6: Middleware `persist` — Lưu State Vào `localStorage`

### 6.1 Middleware là gì trong Zustand

> **Middleware là 1 lớp "bọc thêm" hành vi cho Store, chèn vào giữa lúc Store được tạo và lúc Component sử dụng nó — mà không cần sửa lại logic State/Action bên trong.**

### 6.2 Cấu hình `persist()` để tự động lưu và khôi phục từ `localStorage`

```ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

type CartState = {
  items: CartItem[];
  addItem: (item: Omit<CartItem, 'quantity'>) => void;
  removeItem: (id: number) => void;
};

export const useCartStore = create<CartState>()(
  persist(
    (set) => ({
      items: [],
      addItem: (item) => set((state) => {/* ... giống mục 4.2 ... */}),
      removeItem: (id) => set((state) => ({ items: state.items.filter((i) => i.id !== id) })),
    }),
    { name: 'cart-storage' } // Tên key lưu trong localStorage
  )
);
```

So với `useLocalStorage` (Custom Hook đã tự viết ở Bài 7): `persist` làm đúng công việc tương tự — tự động đọc/ghi `localStorage` — nhưng áp dụng luôn cho **toàn bộ Global State** của Store, không phải viết lại logic đó cho từng Component riêng lẻ.

### 6.3 Ứng dụng thực tế: giữ giỏ hàng/thông tin đăng nhập sau khi reload

Nhờ `persist`, người dùng thêm sản phẩm vào giỏ hàng, sau đó **reload lại trang** (F5) hoặc **đóng và mở lại trình duyệt**, giỏ hàng vẫn còn nguyên — không cần viết thêm bất kỳ logic đọc/ghi thủ công nào ở Component.

---

## Phần 7: Tối Ưu Re-render Với `React.memo`, `useMemo`, `useCallback`

### 7.1 Component cha re-render kéo theo toàn bộ Component con re-render

```tsx
function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Đếm: {count}</button>
      <ExpensiveList /> {/* Component này KHÔNG liên quan gì đến "count" */}
    </div>
  );
}
```

> **Mặc định, khi 1 Component re-render, TOÀN BỘ Component con bên trong nó cũng re-render theo — kể cả khi Component con đó không hề dùng đến State vừa thay đổi.** Mỗi lần bấm nút (`count` đổi), `ExpensiveList` cũng bị gọi lại dù nó chẳng liên quan gì đến `count`.

Dùng React DevTools Profiler (mở tab **Profiler** bên cạnh tab Components đã dùng ở Bài 4) để ghi lại 1 lượt tương tác, quan sát trực quan Component nào bị re-render dù không cần thiết.

### 7.2 `React.memo`, `useMemo`, `useCallback`

```tsx
// React.memo - bỏ qua re-render nếu Props không đổi
const ExpensiveList = React.memo(function ExpensiveList() {
  console.log('ExpensiveList render');
  return <ul>{/* ... */}</ul>;
});
```

```tsx
// useMemo - ghi nhớ KẾT QUẢ của 1 phép tính nặng, chỉ tính lại khi dependency đổi
function ProductStats({ products }: { products: Product[] }) {
  const total = useMemo(
    () => products.reduce((sum, p) => sum + p.price, 0), // Phép tính giả định là "nặng"
    [products]
  );
  return <p>Tổng: {total}đ</p>;
}
```

```tsx
// useCallback - giữ nguyên THAM CHIẾU của 1 function giữa các lần render
function App() {
  const [count, setCount] = useState(0);

  const handleSelect = useCallback((id: number) => {
    console.log('Đã chọn:', id);
  }, []); // Tham chiếu "handleSelect" không đổi qua các lần render

  return <ProductList onSelect={handleSelect} />; // Props "onSelect" ổn định -> React.memo phát huy tác dụng
}
```

> ⚠️ `React.memo` chỉ có tác dụng khi **Props truyền xuống ổn định** giữa các lần render. Nếu Component cha truyền 1 function được tạo **mới mỗi lần render** (ví dụ `onSelect={(id) => console.log(id)}` viết trực tiếp trong JSX), `React.memo` ở Component con sẽ **vô dụng** — vì với Component con, Props đó luôn "trông có vẻ khác" (tham chiếu khác) dù logic bên trong giống hệt. Đây là lý do `useCallback` và `React.memo` thường phải **đi cùng nhau**.

### 7.3 Nguyên tắc: đo trước, tối ưu sau

> ⚠️ **Không thêm `React.memo`/`useMemo`/`useCallback` cho MỌI Component/giá trị/hàm.** Bản thân các Hook này cũng có chi phí (so sánh Props, lưu cache) — dùng tràn lan ở những nơi không cần thiết có thể khiến code **chậm hơn và khó đọc hơn** so với không dùng gì cả. Chỉ tối ưu sau khi đã **đo được** (qua Profiler — mục 7.1) rằng có 1 Component/phép tính thực sự gây chậm rõ rệt.

Từ **React 19**, **React Compiler** (công cụ biên dịch mới, đang được tích hợp dần vào hệ sinh thái React) có khả năng **tự động** thêm memo hóa cho code mà không cần viết `useMemo`/`useCallback` thủ công. Dù vậy, khóa học vẫn dạy cách dùng thủ công vì: (1) rất nhiều dự án thực tế đang chạy phiên bản chưa có Compiler, và (2) hiểu rõ cơ chế này là kiến thức nền tảng thường được hỏi khi phỏng vấn vị trí React Developer.

---

## Phần 8: Thực Hành — Giỏ Hàng (Shopping Cart) Dùng Zustand

### 8.1 Thiết kế Store cho giỏ hàng

Dùng lại đúng `useCartStore` đã xây dựng ở mục 4.2 và 6.2 — State là `items: CartItem[]`, Action gồm `addItem`, `removeItem`, và bổ sung thêm `updateQuantity` (tăng/giảm số lượng, áp dụng đúng kỹ thuật `map()` cập nhật 1 phần tử trong mảng đã học ở Bài 4).

### 8.2 Component hiển thị sản phẩm và giỏ hàng

Ghép `ProductCard` (mục 5.4 — gọi Action `addItem` qua Selector) và `CartSummary` (mục 5.1 — đọc `items` qua Selector, tính tổng tiền bằng Derived State) thành 1 trang hoàn chỉnh — 2 Component này **không có quan hệ cha-con trực tiếp** nào cả, nhưng vẫn chia sẻ đúng 1 nguồn dữ liệu duy nhất nhờ Zustand, không cần Lifting State Up (Bài 5) hay truyền Props xuyên nhiều tầng (Phần 1).

### 8.3 Chuyển trạng thái đăng nhập từ `useLocalStorage` sang Auth Store

```ts
// stores/useAuthStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

type User = { id: number; name: string };

type AuthState = {
  user: User | null;
  login: (user: User) => void;
  logout: () => void;
};

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      login: (user) => set({ user }),
      logout: () => set({ user: null }),
    }),
    { name: 'auth-storage' }
  )
);
```

```tsx
// ProtectedRoute.tsx - cập nhật lại so với Bài 8 (lúc đó dùng useLocalStorage)
function ProtectedRoute() {
  const user = useAuthStore((state) => state.user); // Không cần useAuth() Custom Hook + Context nữa

  if (!user) {
    return <Navigate to="/login" replace />;
  }

  return <Outlet />;
}
```

So với cách làm tạm thời ở Bài 8 (`useLocalStorage` + tự viết `useAuth()`), `useAuthStore` gọn hơn hẳn: mọi Component cần biết trạng thái đăng nhập (Navbar hiển thị tên user, `ProtectedRoute` kiểm tra quyền truy cập, trang Profile...) đều gọi thẳng `useAuthStore(...)` — không cần Context, không cần bọc `<Provider>`, và tự động có `persist` giữ đăng nhập qua các lần reload.

---

## 🧪 Bài tập thực hành cuối buổi

1. Tự tay dựng lại ví dụ Prop Drilling ở mục 1.1 (`App` → `Header` → `Toolbar` → `UserMenu`), sau đó giải quyết bằng Context API (Phần 2) — quan sát `Header`/`Toolbar` không cần khai báo Props `user` nữa.
2. Tạo `useCounterStore` bằng Zustand với State `count: number` và 3 Action `increment`, `decrement`, `reset`. Dựng 2 Component độc lập: 1 chỉ hiển thị `count` (dùng Selector — mục 5.2), 1 chỉ chứa 3 nút bấm (chỉ lấy Action qua Selector — mục 5.4). Xác nhận 2 Component này **không có quan hệ cha-con**.
3. Thêm `console.log('Component X render')` vào Component chỉ hiển thị `count` ở bài tập 2. Bấm 1 nút không liên quan (ví dụ nút tăng biến đếm khác bằng `useState` cục bộ ở 1 Component thứ 3) và xác nhận Component hiển thị `count` **không** bị re-render theo.
4. Thêm `persist` vào `useCounterStore`, F5 lại trang và xác nhận `count` vẫn giữ nguyên giá trị trước khi reload.

## 🧪 Bài tập HomeWork

Xem tại file [homeworks.md](homeworks.md)
