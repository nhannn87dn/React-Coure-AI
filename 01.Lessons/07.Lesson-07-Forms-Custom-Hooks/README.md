# ⭐ Bài 7: Forms & Custom Hooks

> 🎯 Mục tiêu: Học viên xây dựng được Form có validate với Controlled Component, sử dụng được `useRef` để truy cập DOM trực tiếp, và tự viết được Custom Hook có kiểu Generic để tái sử dụng logic.

---

## Phần 1: Xử Lý Sự Kiện Form (`onChange`, `onSubmit`)

### 1.0 Nhắc lại nhanh

Ở Bài 4 ta đã học Event Handling (`onClick`, `onKeyDown`...) và Object State. Bài này áp dụng cả 2 vào 1 trường hợp cụ thể và quan trọng bậc nhất trong ứng dụng thực tế: **Form nhập liệu**.

### 1.1 Bắt sự kiện `onChange` để đồng bộ giá trị input với State

```tsx
function EmailInput() {
  const [email, setEmail] = useState('');

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setEmail(e.target.value);
  };

  return <input type="email" value={email} onChange={handleChange} />;
}
```

`onChange` được gọi mỗi khi giá trị của `<input>` thay đổi (mỗi lần gõ phím). `e.target.value` chính là giá trị mới nhất người dùng vừa nhập — kiểu `React.ChangeEvent<HTMLInputElement>` (tương tự các kiểu Event đã học ở Bài 4) giúp TypeScript biết chắc `e.target` có thuộc tính `value` kiểu `string`.

### 1.2 Xử lý `onSubmit` và ngăn reload trang mặc định

```tsx
function LoginForm() {
  const [email, setEmail] = useState('');

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault(); // Đã học ở Bài 4 - chặn hành vi mặc định
    console.log('Submit với email:', email);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="email" value={email} onChange={(e) => setEmail(e.target.value)} />
      <button type="submit">Đăng nhập</button>
    </form>
  );
}
```

> ⚠️ Hành vi mặc định của thẻ `<form>` khi submit là **tự động reload lại trang** (rồi gửi dữ liệu lên server theo kiểu HTML truyền thống). Trong ứng dụng React, ta luôn muốn tự kiểm soát việc gửi dữ liệu bằng JavaScript, nên **luôn gọi `e.preventDefault()`** ngay dòng đầu tiên trong `handleSubmit`.

### 1.3 Validate dữ liệu đơn giản trước khi submit

```tsx
function LoginForm() {
  const [email, setEmail] = useState('');
  const [error, setError] = useState('');

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    if (email.trim() === '') {
      setError('Vui lòng nhập email');
      return;
    }
    if (!email.includes('@')) {
      setError('Email không hợp lệ');
      return;
    }

    setError('');
    console.log('Submit thành công với email:', email);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={email} onChange={(e) => setEmail(e.target.value)} />
      {error && <p style={{ color: 'red' }}>{error}</p>}
      <button type="submit">Đăng nhập</button>
    </form>
  );
}
```

Validate cơ bản chỉ là các phép kiểm tra JavaScript thông thường (kiểm tra rỗng, độ dài, có chứa ký tự nào đó...) đặt **trước** khi thực hiện hành động chính, kết hợp `return` sớm để dừng lại nếu dữ liệu không hợp lệ — không cần thư viện gì thêm ở giai đoạn này.

---

## Phần 2: Controlled Vs Uncontrolled Component

### 2.0 Nguyên lý Single Source of Truth

> **Single Source of Truth (nguồn sự thật duy nhất) nghĩa là: tại bất kỳ thời điểm nào, giá trị hiển thị trên input phải do DUY NHẤT 1 nơi quyết định — hoặc là State của React, hoặc là chính DOM — không để cả 2 nơi cùng "giữ" giá trị và có nguy cơ lệch nhau.**

### 2.1 Controlled Component — giá trị hoàn toàn do State React quản lý

```tsx
function ControlledInput() {
  const [value, setValue] = useState('');

  return <input value={value} onChange={(e) => setValue(e.target.value)} />;
}
```

Ở **Controlled Component**, thẻ `<input>` **không tự quyết định** giá trị nó hiển thị — giá trị luôn đến từ Props `value` (chính là State `value` của React). Mỗi lần gõ phím, `onChange` cập nhật State, State mới lại được truyền ngược vào `value` — vòng lặp này khiến React luôn là "người quyết định cuối cùng" input đang hiển thị gì. Đây chính là cách làm bạn đã dùng xuyên suốt từ Bài 4 đến giờ.

### 2.2 Uncontrolled Component — giá trị để DOM tự quản lý, lấy qua `ref` khi cần

```tsx
function UncontrolledInput() {
  const inputRef = useRef<HTMLInputElement>(null);

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    console.log('Giá trị input:', inputRef.current?.value); // Đọc trực tiếp từ DOM, không qua State
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={inputRef} defaultValue="" />
      <button type="submit">Gửi</button>
    </form>
  );
}
```

Ở **Uncontrolled Component**, React **không** kiểm soát giá trị hiển thị của input trong lúc gõ — DOM tự quản lý lấy (giống HTML thuần túy). Muốn lấy giá trị, ta phải "hỏi thẳng" DOM thông qua `ref` (`useRef` — sẽ học kỹ ở Phần 3) tại thời điểm cần dùng (ví dụ lúc submit), thay vì theo dõi giá trị liên tục qua `onChange`.

### 2.3 So sánh ưu/nhược điểm và khi nào nên chọn cách nào

| Tiêu chí | Controlled | Uncontrolled |
|---|---|---|
| Ai giữ giá trị | State React | DOM tự giữ |
| Validate ngay khi gõ (real-time) | Dễ — đã có giá trị trong State mỗi lần gõ | Khó — phải chủ động đọc DOM mỗi lần |
| Hiệu năng với Form rất nhiều trường | Mỗi lần gõ đều gây re-render | Không gây re-render khi gõ |
| Trường hợp phù hợp | Cần validate/tính toán theo thời gian thực (đếm ký tự, bật/tắt nút Submit) | Form đơn giản, chỉ cần lấy giá trị 1 lần lúc submit, hoặc tích hợp thư viện DOM bên ngoài |

**Quy tắc thực tế của khóa học:** mặc định luôn dùng **Controlled Component** (đã quen thuộc từ Bài 4), chỉ chuyển sang Uncontrolled khi có lý do cụ thể (ví dụ mục 3.3 — tự động focus, hoặc Form rất nhiều trường không cần validate real-time).

### 2.4 Xử lý Form có nhiều trường nhập liệu chỉ với 1 State Object

```tsx
type RegisterForm = {
  fullName: string;
  email: string;
  password: string;
};

function RegisterFormComponent() {
  const [form, setForm] = useState<RegisterForm>({
    fullName: '',
    email: '',
    password: '',
  });

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setForm((prev) => ({ ...prev, [name]: value })); // Computed Property Name - đã ôn ở Bài 1
  };

  return (
    <form>
      <input name="fullName" value={form.fullName} onChange={handleChange} placeholder="Họ tên" />
      <input name="email" value={form.email} onChange={handleChange} placeholder="Email" />
      <input name="password" type="password" value={form.password} onChange={handleChange} placeholder="Mật khẩu" />
    </form>
  );
}
```

Thay vì viết 1 `useState` và 1 hàm `handleChange` riêng cho **mỗi** trường input, ta gộp toàn bộ Form vào **1 Object State**, dùng chung **1 hàm `handleChange`** duy nhất — thuộc tính `name` của thẻ `<input>` (khớp với tên field trong `RegisterForm`) quyết định field nào trong Object State sẽ được cập nhật, nhờ cú pháp `[name]: value` (Computed Property Name, đã ôn ở Bài 1). Nhớ dùng Spread Operator `{...prev, ...}` (đã học kỹ ở Bài 4) để giữ nguyên các field còn lại.

---

## Phần 3: `useRef` — Truy Cập DOM Trực Tiếp Khi Cần Thiết

### 3.1 Cú pháp `useRef<HTMLInputElement>(null)`

```tsx
function FocusableInput() {
  const inputRef = useRef<HTMLInputElement>(null);

  return <input ref={inputRef} />;
}
```

`useRef(initialValue)` trả về **1 object duy nhất** có dạng `{ current: initialValue }`, và object này **giữ nguyên trong suốt vòng đời Component** (không bị tạo lại mỗi lần render như biến thường). Gắn `ref={inputRef}` vào 1 phần tử JSX, React sẽ tự động gán `inputRef.current` trỏ đến đúng DOM node thật của phần tử đó.

> 💡 Kiểu Generic `<HTMLInputElement>` báo cho TypeScript biết `inputRef.current` sẽ là 1 phần tử `<input>` thật (có `.value`, `.focus()`...), không phải `<button>` hay `<div>`. Giá trị khởi tạo luôn là `null` vì tại thời điểm `useRef` chạy, DOM node **chưa tồn tại** — React chỉ gán giá trị thật vào sau khi render xong.

Vì `current` có thể là `null` (trước khi render xong, hoặc phần tử bị ẩn theo điều kiện — Bài 5), luôn dùng Optional Chaining (`?.`, đã ôn ở Bài 1) khi truy cập:

```tsx
inputRef.current?.focus(); // An toàn - không crash nếu current đang là null
```

### 3.2 Phân biệt `useRef` với `useState`

> **Khác biệt cốt lõi: thay đổi `.current` của `useRef` KHÔNG làm Component re-render, còn gọi `setState` của `useState` LUÔN kích hoạt re-render (đã học ở Bài 4).**

```tsx
function RenderCounter() {
  const renderCount = useRef(0);
  renderCount.current = renderCount.current + 1; // Thay đổi trực tiếp, không qua hàm setState nào

  console.log('Component đã render:', renderCount.current, 'lần');
  // Component KHÔNG re-render chỉ vì dòng code trên
}
```

Dùng `useState` khi giá trị đó **cần hiển thị lên giao diện** (thay đổi phải kéo theo re-render). Dùng `useRef` khi cần "ghi nhớ" 1 giá trị xuyên suốt các lần render nhưng **không** muốn nó kích hoạt re-render — phổ biến nhất là để giữ tham chiếu đến 1 DOM node, hoặc giữ ID của `setInterval`/`setTimeout` (đã dùng ở Bài 6, lúc đó khai báo bằng biến cục bộ trong Effect — `useRef` là cách khác để giữ giá trị đó nếu cần truy cập từ nhiều Effect/Event Handler khác nhau).

### 3.3 Ứng dụng thực tế: tự động focus vào input, đọc giá trị không qua State

```tsx
function SearchBox() {
  const inputRef = useRef<HTMLInputElement>(null);

  useEffect(() => {
    inputRef.current?.focus(); // Tự động focus ngay khi Component mount (Bài 6)
  }, []);

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    console.log('Tìm kiếm:', inputRef.current?.value); // Đọc giá trị TRỰC TIẾP từ DOM, không cần useState
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={inputRef} placeholder="Nhập từ khóa..." />
      <button type="submit">Tìm</button>
    </form>
  );
}
```

Ở đây, ô input là **Uncontrolled** (mục 2.2): ta không cần theo dõi từng ký tự gõ vào (không cần validate real-time), chỉ cần đọc giá trị **1 lần duy nhất** lúc submit — dùng `useRef` giúp tránh re-render thừa mỗi lần gõ phím so với việc bắt buộc phải dùng `useState` + `onChange`.

---

## Phần 4: Tại Sao Cần Custom Hook — Tránh Lặp Code Logic

### 4.0 Vấn đề: nhiều Component dùng chung 1 đoạn logic Hook giống hệt nhau

Giả sử 2 Component khác nhau đều cần chức năng "bật/tắt" (ví dụ 1 cái ẩn/hiện mật khẩu, 1 cái mở/đóng Modal):

```tsx
// PasswordInput.tsx
function PasswordInput() {
  const [isVisible, setIsVisible] = useState(false);
  const toggle = () => setIsVisible((prev) => !prev);
  // ...
}

// Modal.tsx
function Modal() {
  const [isOpen, setIsOpen] = useState(false);
  const toggle = () => setIsOpen((prev) => !prev);
  // ... y hệt logic ở trên, chỉ đổi tên biến
}
```

Cùng 1 đoạn logic `useState` + hàm toggle được **copy-paste** ở nhiều nơi — nếu sau này muốn sửa cách hoạt động (ví dụ thêm giới hạn không cho toggle liên tục), phải sửa ở **từng Component** một.

### 4.1 Custom Hook là gì

> **Custom Hook là 1 hàm JavaScript/TypeScript bình thường, có tên bắt đầu bằng `use`, bên trong được phép gọi các Hook khác của React (`useState`, `useEffect`...). Nó cho phép đóng gói và tái sử dụng logic Stateful (có State) giữa nhiều Component, mà mỗi Component gọi Custom Hook vẫn có 1 bản sao State độc lập riêng.**

### 4.2 Custom Hook giúp tách UI ra khỏi Logic như thế nào

```tsx
// hooks/useToggle.ts
function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);
  const toggle = () => setValue((prev) => !prev);
  return [value, toggle] as const; // "as const" giữ đúng kiểu tuple [boolean, () => void]
}
```

```tsx
// PasswordInput.tsx - chỉ còn lo phần UI, logic đã tách riêng
function PasswordInput() {
  const [isVisible, toggle] = useToggle(false);

  return (
    <div>
      <input type={isVisible ? 'text' : 'password'} />
      <button type="button" onClick={toggle}>{isVisible ? 'Ẩn' : 'Hiện'}</button>
    </div>
  );
}
```

`PasswordInput` giờ không còn "biết" bên trong `useToggle` hoạt động ra sao — nó chỉ quan tâm "tôi nhận được 1 giá trị boolean và 1 hàm để đổi giá trị đó". Đây chính là cách Custom Hook tách phần **Logic** (State + cách cập nhật) ra khỏi phần **UI** (JSX hiển thị) — 2 mối quan tâm khác nhau được giữ riêng biệt, dễ đọc và dễ tái sử dụng.

> 💡 `as const` ở cuối `return [value, toggle] as const` giúp TypeScript hiểu đây là 1 **tuple** có thứ tự cố định (`[boolean, () => void]`), giống hệt cách `useState` của React trả về `[value, setValue]` mà bạn destructure được theo đúng thứ tự.

---

## Phần 5: Xây Dựng Custom Hook Thực Tế — `useToggle`, `useLocalStorage`

### 5.1 `useToggle` — đóng gói logic bật/tắt boolean

Đã viết đầy đủ ở mục 4.2. Dùng lại cho bất kỳ nhu cầu bật/tắt nào: ẩn/hiện mật khẩu, mở/đóng Modal, mở/đóng Sidebar...

```tsx
function Modal() {
  const [isOpen, toggle] = useToggle(false);
  return (
    <div>
      <button onClick={toggle}>{isOpen ? 'Đóng' : 'Mở'} Modal</button>
      {isOpen && <div className="modal">Nội dung Modal</div>}
    </div>
  );
}
```

### 5.2 `useLocalStorage<T>` với Generic — đồng bộ State bất kỳ kiểu nào với `localStorage`

```tsx
// hooks/useLocalStorage.ts
function useLocalStorage<T>(key: string, initialValue: T) {
  const [value, setValue] = useState<T>(() => {
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initialValue; // Đọc dữ liệu cũ (nếu có) ngay khi khởi tạo State
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value)); // Tự động lưu mỗi khi "value" thay đổi
  }, [key, value]);

  return [value, setValue] as const;
}
```

```tsx
type Theme = 'light' | 'dark';

function ThemeSwitcher() {
  const [theme, setTheme] = useLocalStorage<Theme>('app-theme', 'light');

  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      Giao diện hiện tại: {theme}
    </button>
  );
}
```

> 💡 Nhờ **Generic** (`<T>`, đã học ở Bài 1), `useLocalStorage` dùng được cho **bất kỳ kiểu dữ liệu nào** — ở đây là `Theme` (Union Type), nhưng cũng có thể là `number`, `User[]`, hay bất kỳ `type` nào khác — không cần viết lại logic đọc/ghi `localStorage` mỗi lần.
>
> ⚠️ `useState(() => {...})` — truyền vào 1 **hàm** thay vì giá trị trực tiếp — gọi là **Lazy Initialization**. Hàm này chỉ chạy đúng **1 lần** ở lần render đầu tiên (đọc `localStorage` 1 lần), thay vì chạy lại ở **mọi** lần render nếu viết trực tiếp `useState(localStorage.getItem(key))` — tránh đọc `localStorage` không cần thiết.

### 5.3 Sử dụng lại 2 Custom Hook trong nhiều Component khác nhau

Điểm mạnh lớn nhất vừa thấy ở mục 5.1 và 5.2: cả `useToggle` và `useLocalStorage` đều được viết **đúng 1 lần**, nhưng dùng lại được ở bất kỳ Component nào cần đúng loại logic đó — mỗi nơi gọi vẫn có **State độc lập riêng** (gọi `useToggle` ở `PasswordInput` và ở `Modal` là 2 State hoàn toàn tách biệt, không ảnh hưởng lẫn nhau).

---

## Phần 6: Rules Of Hooks — Ôn Lại Và Công Cụ Hỗ Trợ

### 6.1 Ôn nhanh 2 quy tắc

Đã học quy tắc đầu ở Bài 4 (mục 2.3): **chỉ gọi Hook ở top-level** của Component, không lồng trong `if`/`for`/hàm khác. Quy tắc thứ 2, liên quan trực tiếp đến Custom Hook: **chỉ gọi Hook từ Function Component hoặc từ 1 Custom Hook khác** — không gọi Hook từ 1 hàm JavaScript thông thường (hàm không phải Component, không bắt đầu bằng `use`).

```tsx
// ❌ Sai - gọi useState từ 1 hàm JavaScript thường, không phải Component hay Custom Hook
function calculateTotal() {
  const [total, setTotal] = useState(0); // Vi phạm quy tắc 2
  return total;
}
```

### 6.2 Vì sao vi phạm quy tắc này gây lỗi khó debug

Như đã giải thích ở Bài 4: React dựa vào **thứ tự gọi Hook** giữa các lần render (không phải tên biến) để biết Hook nào ứng với State nào ở bên trong. Gọi Hook không đúng chỗ (trong điều kiện, trong hàm thường không phải Component/Custom Hook) khiến React có thể **hiểu nhầm thứ tự**, dẫn đến 1 Component đọc nhầm State của Hook khác — lỗi này thường không có thông báo rõ ràng, biểu hiện ra ngoài chỉ là "giao diện hiển thị sai dữ liệu" rất khó lần ra nguyên nhân.

### 6.3 ESLint plugin `react-hooks` tự phát hiện vi phạm

Dự án Vite tạo bằng `--template react-ts` (Bài 1) đã cài sẵn plugin **`eslint-plugin-react-hooks`** trong `eslint.config.js`. Plugin này tự động:

- Báo lỗi ngay khi gọi Hook sai vị trí (vi phạm 2 quy tắc ở mục 6.1).
- Cảnh báo khi mảng dependency của `useEffect` **thiếu** 1 giá trị đang được dùng bên trong (đã nhắc ở Bài 6, mục 3.3).

> 💡 Luôn để ý các cảnh báo gạch vàng/đỏ từ ESLint ngay trong VS Code (nhờ extension ESLint đã cài ở Bài 1) — phần lớn lỗi liên quan đến Hook có thể được phát hiện **trước khi chạy thử**, tương tự cách TypeScript bắt lỗi kiểu dữ liệu.

---

## Phần 7: Thực Hành — Form Đăng Nhập Hoàn Chỉnh Với Validate Và Custom Hook

### 7.1 Form đăng nhập với 2 trường input, validate và hiển thị lỗi

```tsx
type LoginFormData = {
  email: string;
  password: string;
};

function LoginForm() {
  const [form, setForm] = useState<LoginFormData>({ email: '', password: '' });
  const [error, setError] = useState('');

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setForm((prev) => ({ ...prev, [name]: value }));
  };

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    if (form.email.trim() === '' || form.password.trim() === '') {
      setError('Vui lòng nhập đầy đủ email và mật khẩu');
      return;
    }

    setError('');
    console.log('Đăng nhập với:', form);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="email" value={form.email} onChange={handleChange} placeholder="Email" />
      <input name="password" type="password" value={form.password} onChange={handleChange} placeholder="Mật khẩu" />
      {error && <p style={{ color: 'red' }}>{error}</p>}
      <button type="submit">Đăng nhập</button>
    </form>
  );
}
```

### 7.2 Áp dụng `useToggle` để ẩn/hiện mật khẩu

```tsx
function LoginForm() {
  const [form, setForm] = useState<LoginFormData>({ email: '', password: '' });
  const [showPassword, toggleShowPassword] = useToggle(false); // Custom Hook - mục 5.1

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setForm((prev) => ({ ...prev, [name]: value }));
  };

  return (
    <form>
      <input name="email" value={form.email} onChange={handleChange} placeholder="Email" />
      <input
        name="password"
        type={showPassword ? 'text' : 'password'}
        value={form.password}
        onChange={handleChange}
        placeholder="Mật khẩu"
      />
      <button type="button" onClick={toggleShowPassword}>
        {showPassword ? 'Ẩn mật khẩu' : 'Hiện mật khẩu'}
      </button>
      <button type="submit">Đăng nhập</button>
    </form>
  );
}
```

### 7.3 Áp dụng `useLocalStorage` để lưu lại "Ghi nhớ đăng nhập"

```tsx
function LoginForm() {
  const [rememberMe, setRememberMe] = useLocalStorage<boolean>('remember-me', false); // Custom Hook - mục 5.2

  return (
    <form>
      {/* ... các input email/password ... */}
      <label>
        <input
          type="checkbox"
          checked={rememberMe}
          onChange={(e) => setRememberMe(e.target.checked)}
        />
        Ghi nhớ đăng nhập
      </label>
      <button type="submit">Đăng nhập</button>
    </form>
  );
}
```

Vì `useLocalStorage` tự động đồng bộ với `localStorage` (mục 5.2), giá trị `rememberMe` sẽ **giữ nguyên** ngay cả khi người dùng tải lại (refresh) trang — không cần viết thêm bất kỳ logic lưu/đọc thủ công nào ở `LoginForm`.

---

## 🧪 Bài tập thực hành cuối buổi

1. Viết Component `ContactForm` với 3 trường: `name`, `email`, `message` — dùng **1 Object State** và **1 hàm `handleChange`** chung (mục 2.4). Validate: cả 3 trường không được để trống khi submit.
2. Viết Custom Hook `useToggle` (mục 5.1) từ đầu (chưa xem lại bài học), áp dụng cho 1 Component `Accordion` đơn giản (click vào tiêu đề để mở/đóng nội dung bên dưới).
3. Viết Component `AutoFocusInput` dùng `useRef` + `useEffect` để tự động focus vào ô input ngay khi Component mount (mục 3.3).
4. Viết Custom Hook `useWindowWidth()` (không nhận tham số, trả về `number`) đóng gói lại logic theo dõi `window.innerWidth` đã làm ở bài thực hành Bài 6 — áp dụng Custom Hook để tái sử dụng logic đó ở ít nhất 2 Component khác nhau trong cùng 1 trang.

## 🧪 Bài tập HomeWork

Xem tại file [homeworks.md](homeworks.md)

---

## 🧩 Chuẩn Bị Mini Project

Cuối buổi học này, giảng viên sẽ công bố đề bài **Mini Project** (tổng hợp kiến thức Bài 1-7, làm tại nhà). Xem chi tiết đề bài, yêu cầu tối thiểu và tiêu chí chấm điểm tại [mini-project.md](mini-project.md).
