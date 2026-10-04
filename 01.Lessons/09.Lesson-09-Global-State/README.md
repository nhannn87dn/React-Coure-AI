# ⭐ Bài 9: Quản Lý Global State Với Zustand

> 🎯 Mục tiêu: Học viên hiểu vấn đề Prop Drilling, thấy được giới hạn của Context API thuần, và làm chủ Zustand — từ tạo Store, đọc/cập nhật State trong Component, dùng middleware `persist`, đến xây dựng hoàn chỉnh 1 Giỏ hàng.

---

## Phần 1: Vấn Đề Prop Drilling

### 1.0 Định nghĩa

> **Prop Drilling là tình huống 1 giá trị Props phải được truyền qua nhiều tầng Component trung gian chỉ để đến được Component con sâu bên trong, dù các tầng trung gian đó không hề dùng đến giá trị này.**

### 1.1 Ví dụ thực tế: truyền thông tin user qua 3-4 tầng Props

```tsx
interface User {
  name: string;
  avatar: string;
}

function App() {
  const user: User = { name: 'An', avatar: '/avatar.png' };
  return <Layout user={user} />;
}

// Layout không dùng "user", chỉ truyền tiếp xuống Sidebar
function Layout({ user }: { user: User }) {
  return <Sidebar user={user} />;
}

// Sidebar cũng không dùng "user", chỉ truyền tiếp xuống UserMenu
function Sidebar({ user }: { user: User }) {
  return <UserMenu user={user} />;
}

// Chỉ UserMenu ở tận cùng mới thực sự CẦN "user"
function UserMenu({ user }: { user: User }) {
  return <p>Xin chào, {user.name}</p>;
}
```

`Layout` và `Sidebar` hoàn toàn không quan tâm đến `user`, nhưng vẫn buộc phải khai báo props `user` và truyền tiếp — chỉ vì `UserMenu` ở tận cây con sâu bên dưới mới thực sự cần đến.

### 1.2 Vì sao Prop Drilling gây khó bảo trì

Khi dự án lớn dần (cây Component sâu 5-6 tầng, nhiều loại dữ liệu cần chia sẻ như user, theme, giỏ hàng...), Prop Drilling gây ra:

- Phải sửa **code ở mọi tầng trung gian** mỗi khi thêm 1 props mới cần truyền xuống sâu.
- Component trung gian bị "nhiễm" props nó không dùng, khó hiểu Component đó thực sự cần gì.
- Dễ quên truyền props ở 1 tầng nào đó, gây lỗi khó phát hiện.

---

## Phần 2: Giải Pháp "Vanilla" Của React — Context API Thuần

### 2.1 Tạo Context bằng `createContext()` và bọc bằng Provider

```tsx
// contexts/UserContext.tsx
import { createContext } from 'react';

interface User {
  name: string;
  avatar: string;
}

const UserContext = createContext<User | null>(null);

export default UserContext;
```

```tsx
// App.tsx
import UserContext from './contexts/UserContext';

function App() {
  const user = { name: 'An', avatar: '/avatar.png' };

  return (
    <UserContext.Provider value={user}>
      <Layout />
    </UserContext.Provider>
  );
}
```

### 2.2 Đọc giá trị Context bằng `useContext()`

```tsx
// Layout và Sidebar giờ đây KHÔNG cần biết gì về "user" nữa
function Layout() {
  return <Sidebar />;
}

function Sidebar() {
  return <UserMenu />;
}

// UserMenu đọc thẳng Context, không cần nhận qua Props
import { useContext } from 'react';
import UserContext from './contexts/UserContext';

function UserMenu() {
  const user = useContext(UserContext);
  return <p>Xin chào, {user?.name}</p>;
}
```

`Layout` và `Sidebar` không còn phải khai báo/truyền props `user` nữa — `UserMenu` đọc trực tiếp từ `UserContext` dù nằm sâu bao nhiêu tầng, giải quyết đúng vấn đề Prop Drilling ở Phần 1.

---

## Phần 3: Hạn Chế Của Context Thuần

### 3.1 Vấn đề re-render thừa

Khi giá trị trong `<UserContext.Provider value={...}>` thay đổi, **mọi Component đang gọi `useContext(UserContext)`** đều bị re-render — kể cả khi Component đó chỉ cần 1 phần rất nhỏ của dữ liệu (hoặc thậm chí dữ liệu nó cần không hề đổi). Không có cơ chế nào để Component chỉ "lắng nghe" đúng phần dữ liệu mình cần như đã làm được với Props ở Bài 3.

### 3.2 Code cồng kềnh khi cần logic cập nhật phức tạp

Nếu cần nhiều loại State khác nhau (user, giỏ hàng, theme...) hoặc logic cập nhật phức tạp (thêm/xoá/sửa item trong giỏ hàng), Context thuần buộc bạn phải tự viết thêm `useReducer`, tự tạo nhiều Context lồng nhau, và tự quản lý nhiều `Provider` bọc lồng vào nhau — code nhanh chóng trở nên rườm rà so với 1 vài dòng State cần quản lý thực sự.

---

## Phần 4: So Sánh Nhanh — Context API vs Zustand

### 4.1 Khi nào dùng cái nào

- **Context API**: phù hợp cho dữ liệu **ít thay đổi** theo thời gian (theme sáng/tối, ngôn ngữ hiển thị, thông tin user sau khi đăng nhập) — nơi vấn đề re-render thừa (Phần 3.1) không quá nghiêm trọng.
- **Zustand**: phù hợp cho dữ liệu **thay đổi liên tục** với nhiều thao tác cập nhật phức tạp (giỏ hàng, danh sách thông báo, trạng thái UI phức tạp) — nơi cần tối ưu re-render và tổ chức logic State rõ ràng.

### 4.2 Bảng so sánh nhanh

| Tiêu chí | Context API | Zustand |
|---|---|---|
| Cần bọc `<Provider>` | Bắt buộc | Không cần |
| Re-render tối ưu theo phần dữ liệu | Không (Phần 3.1) | Có (Selector Pattern, Phần 6.2) |
| Độ phức tạp code khi logic lớn dần | Tăng nhanh (Phần 3.2) | Vẫn gọn nhờ tách State/Action rõ ràng |
| Cần cài thêm thư viện | Không (có sẵn trong React) | Có (`zustand`) |

---

## Phần 5: Cài Đặt và Tạo Store Đầu Tiên Với Zustand

### 5.1 Cài đặt thư viện `zustand`

```bash
pnpm add zustand
```

### 5.2 Tạo Store bằng hàm `create()`

```tsx
// store/useCounterStore.ts
import { create } from 'zustand';

interface CounterState {
  count: number;
  increment: () => void;
  decrement: () => void;
  reset: () => void;
}

const useCounterStore = create<CounterState>((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
  reset: () => set({ count: 0 }),
}));

export default useCounterStore;
```

Khác với `useState` (Bài 4) — nơi State và hàm cập nhật (`setState`) tách rời nhau — Zustand gộp **cả State lẫn Action (hàm cập nhật)** vào **cùng 1 nơi duy nhất**: object trả về từ `create()`. Hàm `set` hoạt động tương tự Functional Update đã học ở Bài 4 (Phần 3.3): nhận vào `state` hiện tại, trả về phần State cần cập nhật.

### 5.3 Cấu trúc file store chuẩn để dễ maintain

```text
src/
├── store/
│   ├── useCounterStore.ts
│   ├── useCartStore.ts
│   └── useUserStore.ts
```

Quy ước phổ biến: mỗi Store quản lý 1 "miền dữ liệu" riêng biệt (giỏ hàng, user, giao diện...), đặt tên file bắt đầu bằng `use...Store` để nhất quán với quy ước Custom Hook đã học ở Bài 7 — vì bản chất, mỗi Store Zustand cũng chính là 1 Custom Hook.

---

## Phần 6: Đọc và Cập Nhật State Trong Component Với `useStore`

### 6.1 Gọi Hook Store trực tiếp trong Component

```tsx
// components/Counter.tsx
import useCounterStore from '../store/useCounterStore';

function Counter() {
  const count = useCounterStore((state) => state.count);
  const increment = useCounterStore((state) => state.increment);

  return (
    <div>
      <p>Số lượng: {count}</p>
      <button onClick={increment}>Tăng</button>
    </div>
  );
}
```

Không cần `<Provider>` bọc quanh ứng dụng như Context API (Phần 2.1) — `Counter` gọi thẳng `useCounterStore` ở bất kỳ đâu trong cây Component, dù nằm sâu bao nhiêu tầng, và không hề gặp lại vấn đề Prop Drilling (Phần 1).

### 6.2 Selector Pattern — chỉ lấy đúng phần State cần thiết

```tsx
// ✅ Đúng - chỉ subscribe đúng "count", Component chỉ re-render khi count đổi
const count = useCounterStore((state) => state.count);

// ⚠️ Cẩn trọng - lấy nguyên object Store, Component re-render mỗi khi BẤT KỲ field nào trong Store đổi
const store = useCounterStore();
```

Tham số truyền vào `useCounterStore` là 1 hàm **selector** — chỉ định rõ Component này cần đọc đúng phần nào của Store. Đây chính là cách Zustand giải quyết vấn đề re-render thừa đã nêu ở Phần 3.1: 2 Component cùng dùng chung 1 Store nhưng subscribe 2 field khác nhau sẽ **không** re-render lẫn nhau khi chỉ 1 trong 2 field đổi giá trị.

### 6.3 Gọi Action từ Store để cập nhật State

```tsx
function CounterButtons() {
  const increment = useCounterStore((state) => state.increment);
  const decrement = useCounterStore((state) => state.decrement);
  const reset = useCounterStore((state) => state.reset);

  return (
    <div>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

Vì Action (`increment`, `decrement`, `reset`) đã được định nghĩa sẵn ngay trong Store (Phần 5.2), Component chỉ cần gọi thẳng chúng — không cần tự viết logic cập nhật State ở từng Component như khi dùng `useState`/Props truyền function xuống (đã học ở Bài 4 và Bài 3, Phần 5.1).

---

## Phần 7: Middleware Phổ Biến — `persist` (Lưu State Vào `localStorage`)

### 7.1 Middleware là gì trong Zustand

> **Middleware là 1 lớp "bọc thêm" hành vi cho Store, chèn vào giữa lúc bạn gọi Action và lúc State thực sự được cập nhật — ví dụ tự động đồng bộ State ra `localStorage` mỗi khi nó đổi.**

### 7.2 Cấu hình `persist()`

```tsx
// store/useCartStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface CartItem {
  id: number;
  name: string;
  price: number;
  quantity: number;
}

interface CartState {
  items: CartItem[];
  addItem: (item: CartItem) => void;
}

const useCartStore = create<CartState>()(
  persist(
    (set) => ({
      items: [],
      addItem: (item) =>
        set((state) => ({ items: [...state.items, item] })), // ôn lại Bài 4, Phần 5.3
    }),
    { name: 'cart-storage' } // key lưu trong localStorage
  )
);

export default useCartStore;
```

So với Custom Hook `useLocalStorage` tự viết thủ công ở Bài 7 (Phần 5.2), `persist()` làm đúng việc tương tự nhưng tích hợp sẵn cho toàn bộ Store — không cần tự viết `useEffect` để đồng bộ `localStorage` thủ công nữa.

### 7.3 Ứng dụng thực tế: giữ giỏ hàng sau khi reload trang

Nhờ `persist`, ngay cả khi người dùng **F5 refresh trang** hoặc đóng/mở lại trình duyệt, giá trị `items` trong `useCartStore` vẫn được khôi phục lại đúng như trước — vì Middleware đã tự động lưu nó vào `localStorage` mỗi khi Store thay đổi, và đọc lại ngay khi Store được khởi tạo.

---

## Phần 8: Thực Hành — Giỏ Hàng (Shopping Cart) Dùng Zustand

### 8.1 Thiết kế Store cho giỏ hàng

```tsx
// store/useCartStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

export interface CartItem {
  id: number;
  name: string;
  price: number;
  quantity: number;
}

interface CartState {
  items: CartItem[];
  addItem: (item: Omit<CartItem, 'quantity'>) => void;
  removeItem: (id: number) => void;
  updateQuantity: (id: number, quantity: number) => void;
}

const useCartStore = create<CartState>()(
  persist(
    (set) => ({
      items: [],

      addItem: (item) =>
        set((state) => {
          const existing = state.items.find((i) => i.id === item.id);

          if (existing) {
            // Sản phẩm đã có trong giỏ - tăng số lượng (ôn lại Bài 4, Phần 5.5: map())
            return {
              items: state.items.map((i) =>
                i.id === item.id ? { ...i, quantity: i.quantity + 1 } : i
              ),
            };
          }

          // Sản phẩm mới - thêm vào giỏ với quantity = 1
          return { items: [...state.items, { ...item, quantity: 1 }] };
        }),

      removeItem: (id) =>
        set((state) => ({
          items: state.items.filter((i) => i.id !== id), // ôn lại Bài 4, Phần 5.4
        })),

      updateQuantity: (id, quantity) =>
        set((state) => ({
          items: state.items.map((i) => (i.id === id ? { ...i, quantity } : i)),
        })),
    }),
    { name: 'cart-storage' }
  )
);

export default useCartStore;
```

### 8.2 Component hiển thị danh sách sản phẩm và nút "Thêm vào giỏ"

```tsx
// components/ProductList.tsx
import useCartStore from '../store/useCartStore';

interface Product {
  id: number;
  name: string;
  price: number;
}

const products: Product[] = [
  { id: 1, name: 'Áo thun', price: 150000 },
  { id: 2, name: 'Quần jean', price: 350000 },
];

function ProductList() {
  const addItem = useCartStore((state) => state.addItem);

  return (
    <ul>
      {products.map((product) => (
        <li key={product.id}>
          {product.name} - {product.price.toLocaleString()}đ
          <button onClick={() => addItem(product)}>Thêm vào giỏ</button>
        </li>
      ))}
    </ul>
  );
}

export default ProductList;
```

### 8.3 Component Giỏ hàng đọc State từ Store, tính tổng tiền

```tsx
// components/CartSummary.tsx
import useCartStore from '../store/useCartStore';

function CartSummary() {
  const items = useCartStore((state) => state.items);
  const removeItem = useCartStore((state) => state.removeItem);
  const updateQuantity = useCartStore((state) => state.updateQuantity);

  const total = items.reduce((sum, item) => sum + item.price * item.quantity, 0); // ôn lại Bài 1: reduce()

  if (items.length === 0) {
    return <p>Giỏ hàng trống.</p>; // ôn lại Bài 5, Phần 3.1
  }

  return (
    <div>
      <h3>Giỏ hàng</h3>
      <ul>
        {items.map((item) => (
          <li key={item.id}>
            {item.name} — {item.price.toLocaleString()}đ x
            <input
              type="number"
              min={1}
              value={item.quantity}
              onChange={(e) => updateQuantity(item.id, Number(e.target.value))}
              style={{ width: 48 }}
            />
            <button onClick={() => removeItem(item.id)}>Xoá</button>
          </li>
        ))}
      </ul>
      <p>Tổng tiền: {total.toLocaleString()}đ</p>
    </div>
  );
}

export default CartSummary;
```

```tsx
// App.tsx
import ProductList from './components/ProductList';
import CartSummary from './components/CartSummary';

function App() {
  return (
    <div>
      <ProductList />
      <CartSummary />
    </div>
  );
}

export default App;
```

`ProductList` và `CartSummary` là 2 Component **hoàn toàn độc lập, không có quan hệ cha-con truyền Props** — nhưng vẫn chia sẻ đúng 1 nguồn dữ liệu giỏ hàng thông qua `useCartStore`, đúng tinh thần giải quyết Prop Drilling đã đặt ra từ đầu bài (Phần 1). Nhờ `persist` (Phần 7), giỏ hàng vẫn còn nguyên sau khi F5 lại trang.

---

## 🧪 Bài tập thực hành cuối buổi

1. Từ `useCartStore` ở Phần 8.1, viết thêm Action `clearCart` để xoá toàn bộ giỏ hàng, và 1 nút "Xoá tất cả" trong `CartSummary`.
2. Viết 1 Component `CartBadge` (đặt trong `Navbar`) chỉ subscribe đúng tổng **số lượng sản phẩm** trong giỏ (không phải tổng tiền) bằng Selector Pattern (Phần 6.2), hiển thị dạng "🛒 3" — dùng `&&` đã học ở Bài 5 để ẩn badge khi giỏ hàng trống.
3. Tạo thêm `useThemeStore` (State `theme: 'light' | 'dark'` và Action `toggleTheme`) có dùng `persist`, rồi so sánh trải nghiệm code với việc dùng Context API thuần cho cùng 1 tính năng — nêu ra ít nhất 2 điểm khác biệt bạn tự nhận thấy.
4. Thử xoá `persist(...)` khỏi `useCartStore` (chỉ giữ lại object State/Action thuần), build lại giỏ hàng, thêm sản phẩm rồi F5 trang — quan sát giỏ hàng bị mất, liên hệ lại vai trò của Middleware đã học ở Phần 7.

## 🧪 Bài tập Homework
