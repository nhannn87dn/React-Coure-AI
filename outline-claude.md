# Outline lộ trình học React JS cho người mới

---

## ⭐ Bài 1: Giới Thiệu React & Ôn Tập JavaScript ESNext

### ✨ React là gì và tại sao nó thay đổi cách viết Frontend

#### 🔹Lịch sử ra đời của React và vấn đề nó giải quyết (thao tác DOM thủ công với jQuery/Vanilla JS)

#### 🔹Khái niệm "Declarative UI" vs "Imperative UI" - so sánh bằng ví dụ code song song

#### 🔹Hệ sinh thái xoay quanh React (Next.js, React Native, Remix) - cái nhìn tổng quan

#### 🔹So sánh nhanh React với Vue/Angular - vì sao chọn React để học đầu tiên

### ✨ Cài đặt môi trường phát triển (Node.js, Vite, VS Code Extensions)

#### 🔹Cài đặt Node.js và kiểm tra phiên bản (node -v, npm -v)

#### 🔹Tạo dự án React đầu tiên với Vite (pnpm create vite@latest)

#### 🔹Cấu trúc thư mục mặc định của dự án Vite (src, public, main.jsx, App.jsx)

#### 🔹Cài đặt VS Code Extensions cần thiết (ES7+ React Snippets, Prettier, ESLint)

#### 🔹Chạy dự án với pnpm run dev và làm quen với Hot Module Replacement (HMR)

### ✨ Ôn tập Arrow Function, Destructuring, Spread/Rest Operator

#### 🔹Arrow Function - cú pháp rút gọn và sự khác biệt về `this` so với function thường

#### 🔹Destructuring Object - lấy nhanh nhiều giá trị từ object, đổi tên biến, giá trị mặc định

#### 🔹Destructuring Array - áp dụng khi làm việc với useState (const [count, setCount] = ...)

#### 🔹Spread Operator (...) - copy object/array, merge nhiều object không làm thay đổi bản gốc

#### 🔹Rest Operator - gom nhiều tham số hoặc phần còn lại của object/array vào 1 biến

### ✨ Ôn tập Template Literals, Optional Chaining (?.) và Nullish Coalescing (??)

#### 🔹Template Literals - nối chuỗi và nhúng biến bằng dấu backtick, so sánh với concat truyền thống

#### 🔹Optional Chaining (?.) - tránh lỗi "Cannot read property of undefined" khi truy cập object lồng nhau

#### 🔹Nullish Coalescing (??) - gán giá trị mặc định, phân biệt với toán tử `||` khi giá trị là 0 hoặc chuỗi rỗng

#### 🔹Kết hợp ?. và ?? trong tình huống thực tế: hiển thị dữ liệu API có thể null/undefined

### ✨ Ôn tập Array Methods quan trọng (map, filter, reduce, find)

#### 🔹map() - biến đổi mảng dữ liệu, nền tảng để render danh sách trong React

#### 🔹filter() - lọc phần tử theo điều kiện, ứng dụng cho search/filter UI

#### 🔹find() - tìm 1 phần tử duy nhất thỏa điều kiện, phân biệt với filter()

#### 🔹reduce() - tính tổng/gộp dữ liệu, ví dụ tính tổng tiền giỏ hàng

#### 🔹Kết hợp nhiều Array Method liên tiếp (method chaining) trong 1 biểu thức

### ✨ Ôn tập Module (import/export) và Promise/Async-Await cơ bản

#### 🔹Named Export vs Default Export - khi nào dùng loại nào

#### 🔹Cú pháp import với alias (as) và import toàn bộ module (* as)

#### 🔹Promise là gì - 3 trạng thái pending/fulfilled/rejected

#### 🔹Async/Await - cú pháp giúp code bất đồng bộ trông như đồng bộ

#### 🔹Xử lý lỗi với try/catch khi dùng Async/Await

---

## ⭐ Bài 2: Component Đầu Tiên & JSX Cơ Bản

### ✨ Component là gì - tư duy chia nhỏ giao diện (Component-Based Thinking)

#### 🔹Function Component - cách viết một Component chuẩn (PascalCase, return JSX)

#### 🔹Tư duy chia nhỏ UI thành các khối độc lập, tái sử dụng được (ví dụ: Header, Sidebar, Button)

#### 🔹Component cha - con (Parent - Child) và cách chúng lồng nhau tạo thành cây giao diện

#### 🔹Import và Export Component để sử dụng ở file khác

### ✨ Viết JSX Cơ Bản - Ngôn Ngữ Bên Trong Component

#### 🔹JSX trông giống HTML - cách viết thẻ, text, và các thuộc tính quen thuộc (src, href, id...)

#### 🔹className thay cho class - lý do liên quan đến từ khóa reserved trong JavaScript

#### 🔹Thẻ tự đóng bắt buộc với thẻ không có children (`<img />`, `<input />`, `<br />`)

#### 🔹Quy tắc bắt buộc: 1 Component chỉ được return 1 root element (dùng Fragment `<>...</>`)

### ✨ Nhúng Biểu Thức JavaScript Vào JSX

#### 🔹Cú pháp dấu ngoặc nhọn {} - nhúng biến, phép tính, gọi hàm

#### 🔹Nhúng thuộc tính động vào style và các Prop khác (ví dụ: src ảnh lấy từ biến)

#### 🔹Comment trong JSX - cú pháp `{/* ... */}` khác với HTML comment

### ✨ Cấu trúc một dự án React tạo bằng Vite

#### 🔹Vai trò của file index.html, main.jsx và App.jsx trong luồng khởi chạy ứng dụng

#### 🔹Thư mục src/assets, src/components - quy ước tổ chức code phổ biến

#### 🔹File vite.config.js và package.json - các thông tin quan trọng cần biết

#### 🔹Import CSS vào Component - cách Vite xử lý file .css

### ✨ Thực hành: Xây dựng trang giới thiệu cá nhân tĩnh từ nhiều Component

#### 🔹Tách trang thành các Component nhỏ (Header, Introduction, Footer)

#### 🔹Viết nội dung JSX tĩnh cho từng Component, nhúng vài biến động bằng {}

#### 🔹Ghép các Component lại trong App.jsx để hoàn thiện 1 trang hoàn chỉnh

---

## ⭐ Bài 3: JSX Nâng Cao & Props

### ✨ JSX Thực Chất Là Gì - Cách Nó Biên Dịch Thành JavaScript

#### 🔹JSX không phải HTML - bản chất là cú pháp đường (syntactic sugar) cho React.createElement()

#### 🔹Xem thử kết quả biên dịch JSX qua Babel Repl (minh họa trực quan) - liên hệ lại các Component đã viết ở Bài 2

#### 🔹Ôn lại quy tắc camelCase (onClick, tabIndex, backgroundColor trong style) - vì sao JSX không dùng y hệt tên thuộc tính HTML

### ✨ Những Giới Hạn Của JSX Cần Lưu Ý

#### 🔹Những gì KHÔNG thể nhúng trực tiếp vào JSX (if/else, for loop) và cách xử lý thay thế (ternary, biến trung gian)

#### 🔹Lỗi thường gặp khi quên đóng thẻ hoặc quên bọc nhiều phần tử trong 1 root element

### ✨ Virtual DOM và Cơ Chế Reconciliation (Giải Thích Trực Quan)

#### 🔹Real DOM là gì và vì sao thao tác trực tiếp Real DOM tốn hiệu năng

#### 🔹Virtual DOM là gì - bản sao nhẹ (lightweight copy) của Real DOM trong bộ nhớ

#### 🔹Diffing Algorithm - cách React so sánh Virtual DOM cũ và mới để tìm thay đổi

#### 🔹Reconciliation - quá trình cập nhật Real DOM chỉ ở phần thay đổi, không render lại toàn bộ trang

### ✨ Props Là Gì Và Cách Truyền Dữ Liệu Giữa Component Cha - Con

#### 🔹Props là gì - cơ chế truyền dữ liệu một chiều (one-way data flow) từ cha xuống con

#### 🔹Cú pháp truyền Props khi gọi Component (giống truyền attribute trong HTML)

#### 🔹Nhận Props trong Component con qua tham số đầu tiên của hàm

#### 🔹Nguyên tắc Immutability (Tính vô biến - Read-only) của Props

### ✨ Kỹ thuật làm việc với Props

#### 🔹Truyền và nhận Props linh hoạt (Primitive types, Objects, Arrays, Functions)

#### 🔹Rút gọn code nhận Props với Destructuring và Default Props

#### 🔹Kiểm tra kiểu dữ liệu của Props với PropTypes

### ✨ Props.children Và Composition Pattern

#### 🔹props.children là gì - cách Component "bọc" nội dung bên trong nó

#### 🔹Ứng dụng thực tế: xây dựng Component Modal/Card dùng chung cho nhiều loại nội dung

#### 🔹Composition Pattern - ưu điểm so với việc truyền quá nhiều Props rời rạc

### ✨ Thực hành: Xây dựng Card component tái sử dụng, nhận dữ liệu qua Props

#### 🔹Thiết kế Props cần thiết cho Card (title, image, description, children)

#### 🔹Viết Component Card và style cơ bản bằng CSS thuần

#### 🔹Tái sử dụng Card với nhiều bộ dữ liệu khác nhau trong cùng 1 trang (truyền Props khác nhau)

---

## ⭐ Bài 4: State & Event Handling

### ✨ Tại sao cần State - Component không tự "nhớ" dữ liệu

#### 🔹 State là gì và sự khác biệt cốt lõi giữa State vs Props

#### 🔹 Vấn đề: thay đổi biến JavaScript thường không làm giao diện re-render

#### 🔹 Khái niệm "re-render" là gì trong vòng đời của React

### ✨ Sử dụng useState Hook

#### 🔹 Cú pháp const [state, setState] = useState(initialValue)

#### 🔹 Quy tắc Hook - chỉ gọi useState ở top-level của Component, không gọi trong if/loop

#### 🔹 Immutability - vì sao không được thay đổi trực tiếp State (ví dụ lỗi khi push() vào mảng State)

### ✨ Lưu Ý Quan Trọng: Cập Nhật State Không Ngay Lập Tức

#### 🔹 Demo: gọi console.log(state) ngay sau setState() và thấy giá trị chưa đổi - state chỉ cập nhật ở lần render kế tiếp

#### 🔹 Lỗi thường gặp: gọi setCount(count + 1) nhiều lần liên tiếp nhưng chỉ tăng 1 lần

#### 🔹 Cách khắc phục: dùng Functional Update setCount(c => c + 1) khi cần cập nhật dựa trên giá trị trước đó

### ✨ Quản lý nhiều State trong một Component

#### 🔹 Khai báo nhiều useState độc lập cho từng loại dữ liệu (thay vì gộp vào 1 object lớn)

#### 🔹 Cập nhật 1 phần của object State - lỗi thường gặp khi quên spread phần còn lại

### ✨ Quản Lý State Dạng Dữ Liệu Phức Tạp (Object State và Array State)

#### 🔹 Cập nhật Object State đúng cách - dùng spread {...state, key: newValue} thay vì gán trực tiếp state.key = newValue

#### 🔹 Cập nhật Object State lồng nhau (nested object) - vì sao spread 1 lớp là chưa đủ, cần spread từng cấp

#### 🔹 Cập nhật Array State - thêm phần tử mới bằng [...array, newItem] thay vì dùng push()

#### 🔹 Xóa phần tử khỏi Array State bằng filter() - tạo mảng mới thay vì splice() trực tiếp

#### 🔹 Sửa 1 phần tử trong Array State bằng map() - tìm đúng phần tử theo id rồi trả về bản sao đã cập nhật

#### 🔹 Lỗi thường gặp: React không re-render vì thay đổi trực tiếp Object/Array mà không tạo tham chiếu mới

### ✨ Xử lý sự kiện (Event Handling) trong React

#### 🔹 Khái niệm Synthetic Events trong React

##### Xử Lý Sự Kiện Mouse Trong React

- Gắn sự kiện onClick, onDoubleClick trực tiếp trong JSX

- Truyền tham số vào Event Handler bằng Arrow Function inline

- Các sự kiện mouse khác thường gặp: onMouseEnter, onMouseLeave (ứng dụng cho hover effect)

##### Xử Lý Sự Kiện Keyboard Trong React

- Gắn sự kiện onKeyDown, onKeyUp và truy cập Event Object để lấy phím vừa nhấn (e.key)
- Ứng dụng thực tế: bắt sự kiện nhấn phím Enter để thực hiện hành động (ví dụ: submit khi Enter)

-  Ngăn hành vi mặc định của trình duyệt khi cần thiết (e.preventDefault())

### ✨ Thực hành: Counter, Toggle Switch và Mini-game phản xạ bằng phím

#### 🔹 Xây dựng Counter với nút tăng/giảm/reset sử dụng useState và sự kiện Click

#### 🔹 Xây dựng Toggle Switch (ẩn/hiện nội dung) bằng boolean State

#### 🔹 Xây dựng Mini-game đơn giản: nhấn đúng phím được yêu cầu để cộng điểm (kết hợp State + Keyboard Event)

---

## ⭐ Bài 5: Conditional Rendering & Render Danh Sách

### ✨ Các cách render có điều kiện (if/else, ternary, &&)

#### 🔹Dùng if/else thông thường trước return để chọn JSX render ra

#### 🔹Toán tử Ternary (điều kiện ? A : B) - viết inline ngay trong JSX

#### 🔹Toán tử && - ẩn/hiện phần tử khi không cần nhánh else

#### 🔹Lỗi thường gặp: && với giá trị 0 sẽ render ra số 0 ngoài ý muốn

### ✨ Render danh sách với Array.map() và vai trò của Key

#### 🔹Dùng map() để biến mảng dữ liệu thành mảng JSX Element

#### 🔹Prop key là gì - giúp React nhận diện phần tử nào thay đổi/thêm/xóa

#### 🔹Vì sao nên dùng ID duy nhất làm key, tránh dùng index khi danh sách có thể thay đổi thứ tự

### ✨ Kết hợp Conditional Rendering và List Rendering

#### 🔹Hiển thị thông báo "Không có dữ liệu" khi mảng rỗng trước khi map()

#### 🔹Lọc dữ liệu bằng filter() trước khi map() để render danh sách theo điều kiện

#### 🔹Kết hợp nhiều điều kiện lồng nhau trong cùng 1 danh sách (ví dụ: phân loại theo trạng thái)

### ✨ Lỗi thường gặp khi dùng Key sai cách

#### 🔹Dùng index làm key gây lỗi hiển thị sai khi thêm/xóa/sắp xếp lại danh sách

#### 🔹Cảnh báo "Warning: each child in a list should have a unique key" - nguyên nhân và cách sửa

#### 🔹Key phải duy nhất trong phạm vi danh sách anh em (sibling), không cần duy nhất toàn cục

### ✨ Thực hành: Danh sách Todo với trạng thái hoàn thành/chưa hoàn thành

#### 🔹Khởi tạo mảng Todo mẫu trong State với cấu trúc {id, text, completed}

#### 🔹Render danh sách Todo bằng map(), style khác nhau cho item đã hoàn thành

#### 🔹Thêm chức năng click để toggle trạng thái completed của từng Todo

---

## ⭐ Bài 6: useEffect Hook & Component Life Cycle

### ✨ Khái niệm Side Effect và Component Life Cycle

#### 🔹Định nghĩa Side Effect - các thao tác "giao tiếp với thế giới bên ngoài" Component (API, DOM, Timer)

#### 🔹Vì sao không nên gọi Side Effect trực tiếp trong thân hàm Component

#### 🔹useEffect là "cửa thoát hiểm" để đồng bộ Component với hệ thống bên ngoài

#### 🔹Component Life Cycle: Mounting, Updating, Unmounting

### ✨ useEffect cơ bản - cú pháp và Dependency Array

#### 🔹Cú pháp useEffect(() => {...}, [dependencies])

#### 🔹Effect chạy sau khi Component render xong (không chặn giao diện)

#### 🔹Đọc State/Props mới nhất bên trong Effect

### ✨ Phân biệt 3 dạng dependency: [], [deps], không có mảng

#### 🔹Không truyền mảng dependency - Effect chạy lại sau MỌI lần render

#### 🔹Mảng rỗng [] - Effect chỉ chạy đúng 1 lần khi Component mount

#### 🔹Mảng có giá trị [deps] - Effect chạy lại mỗi khi giá trị trong mảng thay đổi

### ✨ Cleanup Function - tránh Memory Leak

#### 🔹Cú pháp return function cleanup bên trong useEffect

#### 🔹Khi nào Cleanup Function được gọi (trước lần Effect kế tiếp, hoặc khi Component unmount)

#### 🔹Ví dụ Memory Leak thực tế nếu quên cleanup Timer hoặc Event Listener

### ✨ Trường hợp sử dụng thực tế: gọi API, subscribe event, đặt Timer

#### 🔹Gọi API lấy dữ liệu khi Component mount (kết hợp async function bên trong Effect)

#### 🔹Subscribe/Unsubscribe sự kiện DOM (ví dụ: window resize, scroll)

#### 🔹Đặt và dọn dẹp setInterval/setTimeout đúng cách

### ✨ Lỗi phổ biến: Infinite Loop với useEffect

#### 🔹Nguyên nhân: cập nhật State bên trong Effect nhưng State đó lại nằm trong dependency

#### 🔹Cách phát hiện Infinite Loop qua Console (log liên tục) hoặc tab Network gọi API liên tục

#### 🔹Hướng khắc phục: kiểm tra lại dependency array và tách logic hợp lý

---

## ⭐ Bài 7: Forms & Custom Hooks

### ✨ Xử Lý Sự Kiện Form (onChange, onSubmit)

#### 🔹 Bắt sự kiện onChange để đồng bộ giá trị input với State (Controlled Input cơ bản)

#### 🔹 Xử lý onSubmit và ngăn reload trang mặc định của thẻ form

#### 🔹 Validate dữ liệu đơn giản trước khi submit (kiểm tra rỗng, độ dài)

### ✨ Controlled vs Uncontrolled Component

#### 🔹 Nguyên lý Single Source of Truth trong quản lý dữ liệu Form

#### 🔹 Controlled Component - giá trị input hoàn toàn do State React quản lý (value + onChange)

#### 🔹 Uncontrolled Component - giá trị input để DOM tự quản lý, lấy dữ liệu qua ref khi cần

#### 🔹 So sánh ưu/nhược điểm và khi nào nên chọn cách nào

#### 🔹 Xử lý Form có nhiều trường nhập liệu (Multi-input Form) chỉ với 1 State Object

### ✨ useRef - truy cập DOM trực tiếp khi cần thiết

#### 🔹 Cú pháp useRef() và gắn ref vào phần tử JSX

#### 🔹 Phân biệt useRef với useState - useRef không gây re-render khi thay đổi giá trị .current

#### 🔹 Ứng dụng thực tế: tự động focus vào input, đọc giá trị input không qua State (Uncontrolled Form)

### ✨ Tại sao cần Custom Hook - tránh lặp code logic

#### 🔹 Vấn đề: nhiều Component dùng chung 1 đoạn logic Hook giống hệt nhau

#### 🔹 Custom Hook là gì - bản chất chỉ là 1 hàm JavaScript thường bắt đầu bằng "use"

#### 🔹 Custom Hook giúp tách UI (JSX) ra khỏi Logic (State, Effect) như thế nào

### ✨ Xây dựng Custom Hook thực tế: useToggle, useLocalStorage

#### 🔹 Viết useToggle - đóng gói logic bật/tắt boolean dùng lại ở nhiều Component

#### 🔹 Viết useLocalStorage - đồng bộ State với localStorage của trình duyệt

#### 🔹 Sử dụng lại 2 Custom Hook này trong nhiều Component khác nhau để thấy lợi ích tái sử dụng

### ✨ Rules of Hooks - những quy tắc bắt buộc phải nhớ

#### 🔹 Chỉ gọi Hook ở top-level, không gọi bên trong if/for/nested function

#### 🔹 Chỉ gọi Hook từ Function Component hoặc từ Custom Hook khác

#### 🔹 Vì sao vi phạm quy tắc này gây lỗi khó debug (thứ tự Hook bị xáo trộn giữa các lần render)

### ✨ Thực hành: Xây dựng Form đăng nhập hoàn chỉnh với validate và Custom Hook

#### 🔹 Xây dựng Form đăng nhập với 2 trường input, validate và hiển thị thông báo lỗi

#### 🔹 Áp dụng useToggle để ẩn/hiện mật khẩu

#### 🔹 Áp dụng useLocalStorage để lưu lại "Ghi nhớ đăng nhập"

---

## ⭐ Bài 8: React Router - Điều Hướng & Multi-Page App

### ✨ Single Page Application (SPA) là gì và vai trò của Router

#### 🔹SPA là gì - chỉ load 1 lần HTML, JavaScript thay đổi nội dung mà không reload trang

#### 🔹Vấn đề nếu không có Router - làm sao thay đổi "trang" mà URL vẫn đúng

#### 🔹Cài đặt thư viện react-router-dom

### ✨ Cài đặt và cấu hình React Router (createBrowserRouter)

#### 🔹Cú pháp createBrowserRouter() định nghĩa danh sách route

#### 🔹Bọc ứng dụng bằng RouterProvider tại file main.jsx

#### 🔹Cấu trúc 1 route object cơ bản: path và element

### ✨ Định nghĩa Routes, Nested Routes và trang 404

#### 🔹Tạo nhiều Route cho các trang khác nhau (Home, About, Contact)

#### 🔹Nested Routes - Route lồng nhau dùng chung Layout (Outlet component)

#### 🔹Cấu hình Route bắt lỗi 404 khi không khớp path nào (path: "*")

### ✨ Component Link/NavLink và điều hướng bằng code với useNavigate

#### 🔹Thẻ Link thay cho thẻ `<a>` - điều hướng không reload trang

#### 🔹NavLink - tự động thêm class active khi đang ở đúng route

#### 🔹useNavigate - điều hướng bằng code (ví dụ sau khi submit form thành công)

### ✨ Lấy tham số từ URL với useParams

#### 🔹Định nghĩa Dynamic Route với cú pháp path có dấu hai chấm (/product/:id)

#### 🔹Dùng useParams() để đọc giá trị tham số động trong Component

#### 🔹Ứng dụng thực tế: trang chi tiết sản phẩm dựa theo ID trên URL

### ✨ Bảo vệ Route (Protected Route) - ví dụ trang yêu cầu đăng nhập

#### 🔹Khái niệm Protected Route - chặn truy cập nếu chưa đăng nhập

#### 🔹Viết Component bọc (Wrapper) kiểm tra điều kiện rồi Navigate hoặc render Outlet

#### 🔹Áp dụng Protected Route cho các Route cần bảo vệ trong cấu hình Router

---

## ⭐ Bài 9: Quản Lý Global State Với Zustand

### ✨ Vấn đề Prop Drilling - demo trực quan bằng cây Component 3-4 cấp truyền Props xuyên suốt

#### 🔹Ví dụ thực tế: truyền thông tin user từ App xuống Component cháu chắt qua 3-4 tầng Props

#### 🔹Vì sao Prop Drilling gây khó bảo trì và dễ lỗi khi dự án lớn dần

### ✨ Giải pháp "vanilla" của React - dùng Context API thuần để giải quyết Prop Drilling

#### 🔹Tạo Context bằng createContext() và bọc cây Component bằng Provider

#### 🔹Đọc giá trị Context ở Component con bất kỳ bằng useContext(), không cần truyền qua từng tầng

### ✨ Hạn chế của Context thuần - re-render toàn bộ cây con khi Context thay đổi, code cồng kềnh khi cần update logic phức tạp

#### 🔹Vấn đề re-render thừa - mọi Component dùng Context đều render lại dù chỉ cần 1 phần dữ liệu

#### 🔹Code trở nên cồng kềnh khi cần thêm nhiều loại State khác nhau hoặc logic cập nhật phức tạp

### ✨ So sánh nhanh: Context API vs Zustand (khi nào dùng cái nào)

#### 🔹Context phù hợp cho dữ liệu ít thay đổi (theme, ngôn ngữ), Zustand phù hợp cho State thay đổi liên tục

#### 🔹Bảng so sánh nhanh: hiệu năng, độ phức tạp code, khả năng mở rộng

### ✨ Cài đặt và tạo Store đầu tiên với Zustand

#### 🔹Cài đặt thư viện zustand qua pnpm

#### 🔹Tạo Store bằng hàm create() - định nghĩa State và Action trong cùng 1 nơi

#### 🔹Cấu trúc file store chuẩn để dễ maintain khi dự án lớn lên

### ✨ Đọc và cập nhật State trong Component với useStore

#### 🔹Gọi Hook Store trực tiếp trong Component để lấy State cần dùng

#### 🔹Selector Pattern - chỉ lấy đúng phần State cần thiết để tránh re-render thừa

#### 🔹Gọi Action từ Store để cập nhật State, không cần Provider bọc ngoài như Context

### ✨ Middleware phổ biến: persist (lưu State vào localStorage)

#### 🔹Middleware là gì trong Zustand - cách "bọc thêm" hành vi cho Store

#### 🔹Cấu hình persist() để tự động lưu và khôi phục State từ localStorage

#### 🔹Ứng dụng thực tế: giữ giỏ hàng/thông tin đăng nhập sau khi reload trang

### ✨ Thực hành: Giỏ hàng (Shopping Cart) dùng Zustand

#### 🔹Thiết kế Store cho giỏ hàng: State (danh sách sản phẩm) và Action (thêm/xóa/cập nhật số lượng)

#### 🔹Xây dựng Component hiển thị danh sách sản phẩm và nút "Thêm vào giỏ"

#### 🔹Xây dựng Component Giỏ hàng đọc State từ Store, tính tổng tiền

---

## ⭐ Bài 10: Data Fetching Cơ Bản (Fetch API & Axios)

### ✨ Gọi API với Fetch - cú pháp và xử lý lỗi

#### 🔹Cú pháp cơ bản fetch(url).then().catch() và cách dùng với Async/Await

#### 🔹Chuyển response về JSON bằng response.json()

#### 🔹Xử lý lỗi HTTP (fetch không tự throw lỗi khi status 404/500 - cách kiểm tra response.ok)

### ✨ Cài đặt và sử dụng Axios - vì sao Axios tiện hơn Fetch

#### 🔹Cài đặt axios qua pnpm và cú pháp gọi GET/POST cơ bản

#### 🔹So sánh Axios và Fetch: tự động parse JSON, tự throw lỗi khi status lỗi, cú pháp ngắn gọn hơn

#### 🔹Truyền params, headers và body trong request bằng Axios

### ✨ Tạo Axios Instance với base URL và Interceptor

#### 🔹Tạo instance riêng bằng axios.create() với baseURL dùng chung cho cả dự án

#### 🔹Request Interceptor - tự động gắn token xác thực vào mọi request

#### 🔹Response Interceptor - xử lý lỗi tập trung (ví dụ tự động logout khi gặp lỗi 401)

### ✨ Xử lý trạng thái Loading, Error, Success thủ công

#### 🔹Khai báo 3 State riêng: loading, error, data để phản ánh từng giai đoạn gọi API

#### 🔹Hiển thị Spinner/Skeleton khi loading, thông báo lỗi khi error, dữ liệu khi success

#### 🔹Cấu trúc code chuẩn: try/catch/finally kết hợp cập nhật 3 State trên

### ✨ Gọi API bên trong useEffect - lỗi race condition thường gặp

#### 🔹Vì sao cần gọi API trong useEffect thay vì trực tiếp trong thân Component

#### 🔹Race Condition là gì - dữ liệu trả về không đúng thứ tự khi params đổi liên tục (ví dụ gõ tìm kiếm nhanh)

#### 🔹Cách khắc phục cơ bản bằng biến cờ (flag) hoặc AbortController trong Cleanup Function

---

## ⭐ Bài 11: Data Fetching Nâng Cao Với TanStack Query

### ✨ Vấn đề của cách fetch thủ công - vì sao cần Server State Library

#### 🔹Những gì phải tự viết lặp lại nhiều lần: loading/error state, cache, refetch, retry khi lỗi

#### 🔹Khái niệm Server State khác với Client State như thế nào (dữ liệu không thuộc sở hữu hoàn toàn của Client)

### ✨ Cài đặt TanStack Query và cấu hình QueryClientProvider

#### 🔹Cài đặt @tanstack/react-query qua pnpm

#### 🔹Khởi tạo QueryClient và bọc ứng dụng bằng QueryClientProvider tại main.jsx/App.jsx

#### 🔹Cài đặt React Query Devtools để quan sát trạng thái Cache trực quan

### ✨ useQuery - fetch dữ liệu với Cache tự động và Query Key

#### 🔹Cú pháp cơ bản useQuery({queryKey, queryFn})

#### 🔹Query Key là gì - vai trò định danh và cache dữ liệu theo key

#### 🔹Nhận về sẵn các trạng thái isLoading, isError, data mà không cần tự khai báo State

### ✨ useMutation - xử lý POST/PUT/DELETE dữ liệu

#### 🔹Cú pháp cơ bản useMutation({mutationFn}) và gọi bằng hàm mutate()

#### 🔹Xử lý các callback onSuccess, onError, onSettled sau khi Mutation hoàn tất

#### 🔹Hiển thị trạng thái isPending khi đang xử lý Mutation (ví dụ disable nút Submit)

### ✨ Invalidate Query - tự động cập nhật dữ liệu sau khi Mutation

#### 🔹Vấn đề: dữ liệu cũ trong Cache không tự cập nhật sau khi thêm/sửa/xóa

#### 🔹Dùng queryClient.invalidateQueries() để báo Cache đó đã "cũ", cần fetch lại

#### 🔹Ứng dụng thực tế: danh sách Todo tự cập nhật ngay sau khi thêm Todo mới qua useMutation

### ✨ So sánh thực tế: Trước và sau khi dùng TanStack Query

#### 🔹Đối chiếu số dòng code và độ phức tạp giữa cách fetch thủ công (⭐ Bài 10) và TanStack Query

#### 🔹Demo tính năng tự động refetch khi quay lại tab trình duyệt (refetchOnWindowFocus)

#### 🔹Tổng kết khi nào nên dùng TanStack Query, khi nào fetch thủ công vẫn đủ dùng

---

## ⭐ Bài 12: Deploy Ứng Dụng React Với Vercel

### ✨ Chuẩn bị dự án trước khi Deploy (build production, kiểm tra lỗi, biến môi trường .env)

#### 🔹Rà soát console.log, code test còn sót lại trước khi deploy

#### 🔹Tạo file .env và quy tắc đặt tên biến môi trường với Vite (tiền tố VITE_)

#### 🔹Kiểm tra .gitignore đã loại trừ file .env và node_modules chưa

### ✨ Build ứng dụng React với Vite (pnpm run build) và kiểm tra kết quả với pnpm run preview

#### 🔹Chạy pnpm run build và giải thích thư mục dist được tạo ra

#### 🔹Dùng pnpm run preview để xem thử bản build trước khi đưa lên Vercel

#### 🔹Kiểm tra dung lượng file build và các cảnh báo tối ưu (nếu có) từ Vite

### ✨ Đưa dự án lên GitHub - quy trình chuẩn (commit, push, .gitignore)

#### 🔹Khởi tạo Git repository (git init) nếu dự án chưa có

#### 🔹Quy trình git add, git commit, git push lên GitHub Repository

#### 🔹Viết commit message rõ ràng theo quy ước cơ bản

### ✨ Giới thiệu Vercel - đăng ký tài khoản và kết nối với GitHub Repository

#### 🔹Đăng ký tài khoản Vercel bằng GitHub (Sign in with GitHub)

#### 🔹Import Repository từ GitHub vào Vercel Dashboard

#### 🔹Vercel tự động nhận diện dự án Vite/React và đề xuất cấu hình mặc định

### ✨ Deploy dự án lên Vercel - cấu hình Build Command & Output Directory

#### 🔹Kiểm tra và chỉnh Build Command (pnpm run build) nếu Vercel nhận diện sai

#### 🔹Kiểm tra Output Directory đúng là "dist" đối với dự án Vite

#### 🔹Theo dõi quá trình Deploy qua Log trên Vercel Dashboard và nhận link demo

### ✨ Xử lý lỗi thường gặp khi Deploy React Router (404 khi refresh trang - cấu hình rewrites)

#### 🔹Nguyên nhân lỗi 404 - Vercel tìm file vật lý theo đường dẫn URL thay vì để React Router xử lý

#### 🔹Tạo file vercel.json với cấu hình rewrites trỏ mọi route về index.html

#### 🔹Deploy lại và kiểm tra refresh trang ở route con không còn lỗi 404

### ✨ Cấu hình biến môi trường (Environment Variables) trên Vercel cho các API Key

#### 🔹Vào Project Settings > Environment Variables trên Vercel Dashboard để khai báo biến

#### 🔹Phân biệt môi trường Production/Preview/Development khi khai báo biến

#### 🔹Redeploy lại dự án để biến môi trường mới có hiệu lực

### ✨ Custom Domain và tự động Deploy khi Push code mới (CI/CD cơ bản với Vercel)

#### 🔹Gắn Custom Domain riêng cho dự án (nếu học viên có domain sẵn)

#### 🔹Cơ chế tự động Deploy mỗi khi Push code mới lên nhánh chính (Continuous Deployment)

#### 🔹Preview Deployment tự động cho mỗi Pull Request - ứng dụng khi làm việc nhóm

### ✨ Tổng kết Best Practices toàn khóa học và giới thiệu đề ⭐ Bài Capstone Project (làm tại nhà, nộp sau)

#### 🔹Ôn lại nhanh các kỹ năng đã học qua 12 buổi theo dòng chảy 1 dự án thực tế hoàn chỉnh

#### 🔹Checklist Best Practices: cấu trúc thư mục, tách Component/Hook, quản lý State hợp lý

#### 🔹Công bố đề ⭐ Bài Capstone Project, tiêu chí chấm điểm và deadline nộp ⭐ Bài
