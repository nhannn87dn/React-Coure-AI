# ⭐ Bài 7: Forms & Custom Hooks

> 🎯 Mục tiêu: Học viên xử lý thành thạo Form trong React (Controlled Component), biết dùng `useRef` khi thực sự cần truy cập DOM trực tiếp, và tự viết được Custom Hook để tái sử dụng logic — kỹ năng quan trọng bậc nhất để code React không bị lặp lại.

---

## Phần 1: Xử Lý Sự Kiện Form (`onChange`, `onSubmit`)

### 1.1 Bắt sự kiện `onChange` để đồng bộ giá trị input với State

```tsx
import { useState } from 'react';

function EmailInput() {
  const [email, setEmail] = useState<string>('');

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setEmail(e.target.value);
  };

  return (
    <div>
      <input type="email" value={email} onChange={handleChange} />
      <p>Bạn đang gõ: {email}</p>
    </div>
  );
}
```

Mỗi lần người dùng gõ 1 ký tự, `onChange` kích hoạt, đọc giá trị mới nhất từ `e.target.value` và lưu vào State — đây chính là nền tảng của kỹ thuật **Controlled Input** sẽ giải thích kỹ ở Phần 2.

### 1.2 Xử lý `onSubmit` và ngăn reload trang mặc định

```tsx
function LoginForm() {
  const [email, setEmail] = useState<string>('');

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault(); // ôn lại Bài 4, Phần 6.2 - ngăn trình duyệt tự reload trang
    console.log('Submit với email:', email);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={email} onChange={(e) => setEmail(e.target.value)} />
      <button type="submit">Đăng nhập</button>
    </form>
  );
}
```

Theo mặc định, thẻ `<form>` HTML sẽ **tự reload trang** khi submit — hành vi này đến từ thời web chưa có JavaScript, gửi dữ liệu form thẳng lên server rồi load lại trang HTML mới. Trong SPA (Single Page Application, đã học ở Bài 8 sắp tới), ta không muốn điều đó xảy ra, nên luôn cần `e.preventDefault()`.

### 1.3 Validate dữ liệu đơn giản trước khi submit

```tsx
function LoginForm() {
  const [email, setEmail] = useState<string>('');
  const [error, setError] = useState<string>('');

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    if (email.trim() === '') {
      setError('Email không được để trống');
      return;
    }
    if (email.length < 5) {
      setError('Email quá ngắn');
      return;
    }

    setError('');
    console.log('Submit hợp lệ với email:', email);
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

> 💡 `{error && <p>...</p>}` là kỹ thuật conditional rendering đã học ở Bài 5 — chỉ hiển thị thông báo lỗi khi `error` khác chuỗi rỗng (chuỗi rỗng là falsy trong JavaScript).

---

## Phần 2: Controlled vs Uncontrolled Component

### 2.0 Định nghĩa

> **Single Source of Truth (nguồn dữ liệu duy nhất) là nguyên lý mà tại bất kỳ thời điểm nào, chỉ có đúng 1 nơi lưu trữ dữ liệu thật của form — hoặc là State của React, hoặc là chính DOM.**

### 2.1 Controlled Component — giá trị input do State React quản lý

```tsx
function ControlledInput() {
  const [value, setValue] = useState<string>('');

  return (
    // value luôn được React "áp đặt" từ State, onChange đồng bộ ngược lại State
    <input value={value} onChange={(e) => setValue(e.target.value)} />
  );
}
```

Với Controlled Component, **State chính là "nguồn sự thật" duy nhất**: giá trị hiển thị trên `<input>` luôn bằng đúng giá trị trong State (`value={value}`), và mọi thay đổi từ người dùng đều phải đi qua `onChange` để cập nhật lại State. Đây là cách tiếp cận phổ biến nhất trong React vì đồng bộ hoàn toàn với tư duy Declarative UI đã học ở Bài 1.

### 2.2 Uncontrolled Component — giá trị để DOM tự quản lý

```tsx
import { useRef } from 'react';

function UncontrolledInput() {
  const inputRef = useRef<HTMLInputElement>(null);

  const handleSubmit = () => {
    // Chỉ đọc giá trị từ DOM khi CẦN, không đồng bộ qua State liên tục
    console.log('Giá trị input:', inputRef.current?.value);
  };

  return (
    <div>
      <input ref={inputRef} defaultValue="" />
      <button onClick={handleSubmit}>Lấy giá trị</button>
    </div>
  );
}
```

Với Uncontrolled Component, **chính DOM tự quản lý giá trị** của nó (giống HTML thuần truyền thống) — React không theo dõi từng ký tự gõ vào, chỉ đọc giá trị qua `ref` (Phần 3) tại đúng thời điểm cần (ví dụ lúc submit).

### 2.3 So sánh ưu/nhược điểm

| Tiêu chí | Controlled | Uncontrolled |
|---|---|---|
| Nguồn dữ liệu thật | State React | DOM |
| Validate realtime khi gõ | Dễ (đọc State mỗi lần đổi) | Khó hơn (phải tự đọc DOM) |
| Re-render mỗi lần gõ phím | Có | Không |
| Phù hợp | Form cần validate/logic phức tạp | Form đơn giản, ít tương tác |

### 2.4 Xử lý Form có nhiều trường nhập liệu chỉ với 1 State Object

```tsx
interface RegisterForm {
  fullName: string;
  email: string;
  phone: string;
}

function RegisterFormComponent() {
  const [formData, setFormData] = useState<RegisterForm>({
    fullName: '',
    email: '',
    phone: '',
  });

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    // Cập nhật đúng field theo "name" - ôn lại Bài 4 (Phần 5.1): spread giữ nguyên field khác
    setFormData({ ...formData, [name]: value });
  };

  return (
    <form>
      <input name="fullName" value={formData.fullName} onChange={handleChange} />
      <input name="email" value={formData.email} onChange={handleChange} />
      <input name="phone" value={formData.phone} onChange={handleChange} />
    </form>
  );
}
```

Kỹ thuật `[name]: value` (computed property name — 1 tính năng của JavaScript ES6) kết hợp với thuộc tính `name` trên mỗi `<input>` cho phép dùng **chung 1 hàm `handleChange`** cho mọi trường nhập liệu, thay vì phải viết riêng 1 hàm `onChange` cho từng field.

---

## Phần 3: `useRef` — Truy Cập DOM Trực Tiếp Khi Cần Thiết

### 3.0 Định nghĩa

> **`useRef` là 1 Hook tạo ra 1 "hộp chứa" (`{ current: ... }`) giữ nguyên giá trị qua các lần render, có thể gắn trực tiếp vào 1 phần tử DOM, và việc thay đổi giá trị của nó KHÔNG kích hoạt re-render.**

### 3.1 Cú pháp `useRef()` và gắn ref vào phần tử JSX

```tsx
import { useRef } from 'react';

function FocusableInput() {
  const inputRef = useRef<HTMLInputElement>(null);

  const focusInput = () => {
    inputRef.current?.focus(); // truy cập trực tiếp DOM element thật
  };

  return (
    <div>
      <input ref={inputRef} />
      <button onClick={focusInput}>Focus vào ô input</button>
    </div>
  );
}
```

`useRef<HTMLInputElement>(null)` khai báo `inputRef.current` sẽ mang kiểu `HTMLInputElement | null` — kiểu `null` ban đầu vì tại thời điểm khai báo, phần tử DOM **chưa tồn tại** (chỉ có sau khi React mount xong, đây là lý do luôn cần dùng dấu `?.` — Optional Chaining đã ôn ở Bài 1 — khi truy cập `inputRef.current`).

### 3.2 Phân biệt `useRef` với `useState`

| Tiêu chí | `useState` | `useRef` |
|---|---|---|
| Thay đổi giá trị có re-render? | Có | Không |
| Đọc giá trị mới nhất ngay lập tức? | Không (Bài 4, Phần 3.1) | Có (`.current` cập nhật tức thì) |
| Dùng cho | Dữ liệu ảnh hưởng đến giao diện | Giá trị "ngầm", không cần hiển thị lại |

```tsx
function RenderCounter() {
  const renderCount = useRef<number>(0);
  renderCount.current += 1; // thay đổi trực tiếp, KHÔNG gây re-render

  return <p>Component này đã render (không đếm chính xác vì log, chỉ minh hoạ)</p>;
}
```

### 3.3 Ứng dụng thực tế: tự động focus, đọc giá trị không qua State

```tsx
function SearchBar() {
  const inputRef = useRef<HTMLInputElement>(null);

  useEffect(() => {
    // Tự động focus vào ô tìm kiếm ngay khi Component mount - ôn lại Bài 6, Phần 3.2
    inputRef.current?.focus();
  }, []);

  const handleSearch = () => {
    // Đọc giá trị input không qua State (Uncontrolled Form, Phần 2.2)
    console.log('Từ khoá tìm kiếm:', inputRef.current?.value);
  };

  return (
    <div>
      <input ref={inputRef} placeholder="Tìm kiếm..." />
      <button onClick={handleSearch}>Tìm</button>
    </div>
  );
}
```

---

## Phần 4: Tại Sao Cần Custom Hook — Tránh Lặp Code Logic

### 4.1 Vấn đề: nhiều Component dùng chung 1 đoạn logic Hook giống hệt nhau

```tsx
// Component A
function ProfileMenu() {
  const [isOpen, setIsOpen] = useState<boolean>(false);
  const toggle = () => setIsOpen((v) => !v);
  return <button onClick={toggle}>{isOpen ? 'Đóng' : 'Mở'} menu</button>;
}

// Component B - LẶP LẠI y hệt logic bật/tắt boolean
function Sidebar() {
  const [isOpen, setIsOpen] = useState<boolean>(false);
  const toggle = () => setIsOpen((v) => !v);
  return <button onClick={toggle}>{isOpen ? 'Đóng' : 'Mở'} sidebar</button>;
}
```

Cả 2 Component có UI khác nhau hoàn toàn, nhưng phần **logic bật/tắt boolean** lại giống hệt nhau — nếu có Component thứ 3, thứ 4 cần logic tương tự, ta lại phải copy-paste tiếp, khiến việc bảo trì (ví dụ sửa 1 lỗi logic) phải sửa ở nhiều nơi.

### 4.2 Custom Hook là gì

> **Custom Hook bản chất chỉ là 1 hàm JavaScript/TypeScript thường, có tên bắt đầu bằng `use`, và được phép gọi các Hook khác của React bên trong nó (`useState`, `useEffect`...).**

Cái tên `use...` không phải quy ước hình thức — React (và ESLint plugin `eslint-plugin-react-hooks`) dựa vào tiền tố này để biết hàm nào cần áp dụng **Rules of Hooks** (Phần 6), giúp phát hiện lỗi sớm.

### 4.3 Custom Hook giúp tách UI ra khỏi Logic như thế nào

```tsx
// hooks/useToggle.ts - chỉ chứa LOGIC, không có JSX
function useToggle(initialValue: boolean = false) {
  const [value, setValue] = useState<boolean>(initialValue);
  const toggle = () => setValue((v) => !v);
  return { value, toggle };
}
```

```tsx
// Component A - chỉ còn lại UI, logic đã được "rút" ra ngoài
function ProfileMenu() {
  const { value: isOpen, toggle } = useToggle();
  return <button onClick={toggle}>{isOpen ? 'Đóng' : 'Mở'} menu</button>;
}
```

Component giờ đây chỉ tập trung vào việc **hiển thị JSX**, còn toàn bộ logic State được đóng gói gọn gàng trong 1 hàm tái sử dụng được ở bất kỳ đâu.

---

## Phần 5: Xây Dựng Custom Hook Thực Tế — `useToggle`, `useLocalStorage`

### 5.1 Viết `useToggle`

```tsx
// hooks/useToggle.ts
import { useState } from 'react';

function useToggle(initialValue: boolean = false) {
  const [value, setValue] = useState<boolean>(initialValue);

  const toggle = () => setValue((v) => !v);

  return { value, toggle } as const;
}

export default useToggle;
```

### 5.2 Viết `useLocalStorage`

```tsx
// hooks/useLocalStorage.ts
import { useState, useEffect } from 'react';

function useLocalStorage<T>(key: string, initialValue: T) {
  const [value, setValue] = useState<T>(() => {
    // Đọc giá trị đã lưu (nếu có) ngay từ lần khởi tạo State đầu tiên
    const saved = localStorage.getItem(key);
    return saved ? (JSON.parse(saved) as T) : initialValue;
  });

  useEffect(() => {
    // Mỗi khi value đổi, tự động lưu lại vào localStorage - ôn lại Bài 6
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue] as const;
}

export default useLocalStorage;
```

> 💡 `useLocalStorage<T>` dùng **generic type** của TypeScript (giống `useState<T>` ở Bài 4) để Hook này dùng được cho bất kỳ kiểu dữ liệu nào (`string`, `number`, `object`...) mà vẫn giữ đúng kiểu khi sử dụng, thay vì phải viết lại Hook riêng cho từng kiểu dữ liệu.

### 5.3 Sử dụng lại 2 Custom Hook trong nhiều Component khác nhau

```tsx
// components/PasswordField.tsx
import useToggle from '../hooks/useToggle';

function PasswordField() {
  const { value: isPasswordVisible, toggle } = useToggle(false);

  return (
    <div>
      <input type={isPasswordVisible ? 'text' : 'password'} />
      <button type="button" onClick={toggle}>
        {isPasswordVisible ? 'Ẩn mật khẩu' : 'Hiện mật khẩu'}
      </button>
    </div>
  );
}
```

```tsx
// components/ThemeSwitcher.tsx
import useLocalStorage from '../hooks/useLocalStorage';

function ThemeSwitcher() {
  const [theme, setTheme] = useLocalStorage<'light' | 'dark'>('app-theme', 'light');

  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      Giao diện hiện tại: {theme}
    </button>
  );
}
```

`useToggle` được dùng lại trong `PasswordField` (khác hoàn toàn use-case bật/tắt menu ở Phần 4.1), còn `useLocalStorage` giúp `ThemeSwitcher` tự động nhớ lựa chọn theme của người dùng ngay cả sau khi tải lại trang — không cần viết lại logic `localStorage.getItem`/`setItem` thủ công ở từng nơi cần dùng.

---

## Phần 6: Rules of Hooks — Những Quy Tắc Bắt Buộc Phải Nhớ

### 6.1 Chỉ gọi Hook ở top-level

```tsx
// ❌ Sai - gọi Hook bên trong if
function Component({ condition }: { condition: boolean }) {
  if (condition) {
    const [value, setValue] = useState(0); // vi phạm!
  }
  return <div>...</div>;
}
```

Quy tắc này đã được nhắc sơ lược ở Bài 4 (Phần 2.2) — giờ áp dụng y hệt cho **mọi Hook**, kể cả Custom Hook tự viết: không gọi Hook bên trong `if`, `for`, hay hàm lồng bên trong Component.

### 6.2 Chỉ gọi Hook từ Function Component hoặc từ Custom Hook khác

```tsx
// ❌ Sai - gọi useState trong 1 hàm JavaScript thường, không phải Component/Custom Hook
function calculateTotal() {
  const [total, setTotal] = useState(0); // Lỗi!
  return total;
}

// ✅ Đúng - đặt tên bắt đầu bằng "use" để trở thành Custom Hook hợp lệ
function useTotal() {
  const [total, setTotal] = useState(0);
  return total;
}
```

### 6.3 Vì sao vi phạm quy tắc này gây lỗi khó debug

React không lưu State theo "tên biến", mà lưu theo **đúng thứ tự Hook được gọi** qua mỗi lần render (giống 1 danh sách được đánh số thứ tự: Hook số 1 là `useState` đầu tiên, Hook số 2 là `useEffect` tiếp theo...). Nếu Hook bị gọi có điều kiện (ví dụ nằm trong `if`), thứ tự này có thể **thay đổi giữa các lần render** — khiến React "gán nhầm" State của Hook này cho Hook khác, sinh ra lỗi rất khó phát hiện (giá trị hiển thị sai, State "nhảy lung tung" không rõ nguyên nhân).

---

## Phần 7: Thực Hành — Form Đăng Nhập Hoàn Chỉnh Với Validate và Custom Hook

### 7.1 Xây dựng Form đăng nhập với validate

```tsx
// components/LoginForm.tsx
import { useState } from 'react';
import useToggle from '../hooks/useToggle';
import useLocalStorage from '../hooks/useLocalStorage';

interface LoginFormData {
  email: string;
  password: string;
}

function LoginForm() {
  const [formData, setFormData] = useState<LoginFormData>({ email: '', password: '' });
  const [error, setError] = useState<string>('');

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setFormData({ ...formData, [name]: value });
  };

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    if (!formData.email.includes('@')) {
      setError('Email không hợp lệ');
      return;
    }
    if (formData.password.length < 6) {
      setError('Mật khẩu phải có ít nhất 6 ký tự');
      return;
    }

    setError('');
    console.log('Đăng nhập thành công với:', formData);
  };

  // Phần 7.2, 7.3 sẽ bổ sung tiếp bên dưới trong cùng Component này
  return (
    <form onSubmit={handleSubmit}>
      <input name="email" placeholder="Email" value={formData.email} onChange={handleChange} />
      <input name="password" type="password" placeholder="Mật khẩu" value={formData.password} onChange={handleChange} />
      {error && <p style={{ color: 'red' }}>{error}</p>}
      <button type="submit">Đăng nhập</button>
    </form>
  );
}

export default LoginForm;
```

### 7.2 Áp dụng `useToggle` để ẩn/hiện mật khẩu

```tsx
function LoginForm() {
  const [formData, setFormData] = useState<LoginFormData>({ email: '', password: '' });
  const [error, setError] = useState<string>('');
  const { value: isPasswordVisible, toggle: togglePasswordVisible } = useToggle(false);

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setFormData({ ...formData, [name]: value });
  };

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    if (!formData.email.includes('@')) {
      setError('Email không hợp lệ');
      return;
    }
    if (formData.password.length < 6) {
      setError('Mật khẩu phải có ít nhất 6 ký tự');
      return;
    }
    setError('');
    console.log('Đăng nhập thành công với:', formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="email" placeholder="Email" value={formData.email} onChange={handleChange} />

      <input
        name="password"
        type={isPasswordVisible ? 'text' : 'password'}
        placeholder="Mật khẩu"
        value={formData.password}
        onChange={handleChange}
      />
      <button type="button" onClick={togglePasswordVisible}>
        {isPasswordVisible ? 'Ẩn' : 'Hiện'} mật khẩu
      </button>

      {error && <p style={{ color: 'red' }}>{error}</p>}
      <button type="submit">Đăng nhập</button>
    </form>
  );
}
```

### 7.3 Áp dụng `useLocalStorage` để lưu "Ghi nhớ đăng nhập"

```tsx
function LoginForm() {
  const [formData, setFormData] = useState<LoginFormData>({ email: '', password: '' });
  const [error, setError] = useState<string>('');
  const { value: isPasswordVisible, toggle: togglePasswordVisible } = useToggle(false);
  const [rememberedEmail, setRememberedEmail] = useLocalStorage<string>('remembered-email', '');

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setFormData({ ...formData, [name]: value });
  };

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    if (!formData.email.includes('@')) {
      setError('Email không hợp lệ');
      return;
    }
    if (formData.password.length < 6) {
      setError('Mật khẩu phải có ít nhất 6 ký tự');
      return;
    }
    setError('');
    setRememberedEmail(formData.email); // lưu lại email vào localStorage
    console.log('Đăng nhập thành công với:', formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="email"
        placeholder="Email"
        value={formData.email || rememberedEmail}
        onChange={handleChange}
      />
      <input
        name="password"
        type={isPasswordVisible ? 'text' : 'password'}
        placeholder="Mật khẩu"
        value={formData.password}
        onChange={handleChange}
      />
      <button type="button" onClick={togglePasswordVisible}>
        {isPasswordVisible ? 'Ẩn' : 'Hiện'} mật khẩu
      </button>
      {error && <p style={{ color: 'red' }}>{error}</p>}
      <button type="submit">Đăng nhập</button>
    </form>
  );
}

export default LoginForm;
```

Form hoàn chỉnh này kết hợp toàn bộ kiến thức của bài: Controlled Component với 1 State object cho nhiều field (Phần 2.4), validate trước submit (Phần 1.3), và 2 Custom Hook tái sử dụng được (Phần 5) — đúng tinh thần "tách UI ra khỏi Logic" đã học ở Phần 4.

---

## 🧪 Bài tập thực hành cuối buổi

1. Viết Custom Hook `useInputValue(initialValue: string)` trả về `{ value, onChange, reset }`, dùng để rút gọn việc quản lý 1 input đơn lẻ (không cần object phức tạp như Phần 2.4).
2. Thêm vào `LoginForm` ở Phần 7 một nút "Xoá thông tin đã ghi nhớ" — gọi `setRememberedEmail('')` để xoá dữ liệu khỏi `localStorage`.
3. Viết Custom Hook `useWindowWidth()` (dựa trên logic đã viết thủ công ở Bài 6, Phần 5.2), trả về chiều rộng cửa sổ hiện tại, rồi dùng lại Hook này ở 2 Component khác nhau để kiểm chứng khả năng tái sử dụng.
4. Cố tình đặt `useToggle` bên trong `if (formData.email !== '')` trong `LoginForm`, chạy thử và quan sát lỗi/cảnh báo React đưa ra — liên hệ lại nguyên nhân đã học ở Phần 6.3.

## 🧪 Bài tập Homework
