# Props và State trong React: từ cơ bản đến nâng cao

Tác giả: Jenni — Tài liệu học dành cho Tim.

Các ví dụ do tác giả xây dựng. Phần đầu dùng JavaScript/JSX; phần nâng cao dùng TypeScript/TSX. Mỗi ví dụ là một tình huống độc lập, trừ những đoạn có ghi rõ tên file liên quan. Với dự án React thông thường, có thể thay nội dung App.jsx bằng các ví dụ đầy đủ có export default. Trong Next.js App Router, component dùng state, event handler hoặc browser API cần nằm trong cây Client Component.


**Mục tiêu:** hiểu props và state khác nhau ở đâu, biết chọn nơi sở hữu dữ liệu, truyền dữ liệu xuống và báo hành động lên, đồng thời tránh các lỗi về snapshot, mutation và dữ liệu trùng lặp.

**Kiến thức cần có:** JavaScript cơ bản, function, object/array, destructuring và map/filter. Chưa cần biết Redux. Các ví dụ nhỏ ưu tiên trình bày luồng dữ liệu; CSS và backend chỉ xuất hiện khi cần giải thích.

**Bản đồ bài học**

| Phần | Chủ đề |
|---|---|
| 1–10 | Props, useState, props/state, callback, snapshot, batching |
| 11–21 | Immutable update, nơi giữ state, controlled form, composition |
| 22–32 | TypeScript, generic, render props, reducer, custom hook, Context |
| 33–39 | API bất đồng bộ, hiệu năng, ref, Next.js, ví dụ tổng hợp |
| 40–42 | Lỗi, bài tập và tài liệu chính thức |

## 1. Props là gì?

Props là viết tắt của properties. Đây là đầu vào mà component nhận từ nơi sử dụng nó. Component cha dùng props để cung cấp dữ liệu, cấu hình giao diện hoặc truyền hàm xử lý cho component con.

Nếu quen với backend, anh có thể hình dung props gần giống tham số đầu vào của một hàm. Một component có thể được dùng nhiều lần với các đầu vào khác nhau.

```jsx
function Greeting({ name }) {
  return <h2>Xin chào anh {name}!</h2>;
}

export default function App() {
  return (
    <main>
      <Greeting name="Tim" />
      <Greeting name="Nam" />
    </main>
  );
}
```

Kết quả là hai lời chào, tương ứng với Tim và Nam. `name` là tên prop; `"Tim"` là giá trị; `Greeting` là component nhận prop. React truyền một object props vào component.

```jsx
function Greeting(props) {
  return <h2>Xin chào anh {props.name}!</h2>;
}
```

Destructuring giúp lấy các thuộc tính cần dùng ngay trong tham số:

```jsx
function Greeting({ name, title }) {
  return <h2>{title} {name}</h2>;
}
```

Không viết `function Greeting(name, title)` để nhận hai props; cách đó không khớp với cách React truyền props.

## 2. Truyền các kiểu dữ liệu

Chuỗi có thể viết trong dấu nháy. Dùng `{}` khi cần truyền một biểu thức JavaScript.

```jsx
function Profile({ name, age, active, skills, address }) {
  return (
    <article>
      <h2>{name}</h2>
      <p>Tuổi: {age}</p>
      <p>{active ? 'Đang hoạt động' : 'Đã tạm ngưng'}</p>
      <p>Kỹ năng: {skills.join(', ')}</p>
      <p>Thành phố: {address.city}</p>
    </article>
  );
}

export default function App() {
  return (
    <Profile
      name="Tim"
      age={30}
      active={true}
      skills={['React', 'Next.js', '.NET']}
      address={{ city: 'TP.HCM' }}
    />
  );
}
```

| Cách viết | Giá trị nhận được |
|---|---|
| `age="30"` | Chuỗi `"30"` |
| `age={30}` | Số `30` |
| `active` | Boolean `true` |
| `active={false}` | Boolean `false` |
| `active="false"` | Chuỗi `"false"`, là giá trị truthy |
| `address={{ city: 'TP.HCM' }}` | Object |
| `onSave={handleSave}` | Hàm |
| `header={<h2>Thông tin</h2>}` | React element |

Boolean không được hiển thị trực tiếp thành chữ trong JSX. Để xem giá trị khi học, dùng `{String(active)}`. Object thường cũng không render trực tiếp được: dùng `{address.city}` hoặc `<pre>{JSON.stringify(address, null, 2)}</pre>`.

## 3. Giá trị mặc định, undefined và null

```jsx
function Badge({ label = 'Chưa có tên', tone = 'neutral' }) {
  return <span className={`badge badge-${tone}`}>{label}</span>;
}

export default function App() {
  return (
    <>
      <Badge />
      <Badge label="Đã duyệt" tone="success" />
      <Badge label={undefined} />
      <Badge label={null} />
    </>
  );
}
```

Giá trị mặc định áp dụng khi thiếu prop hoặc prop là `undefined`. Nó không thay thế `null`, `0`, `false` hay chuỗi rỗng. Dòng cuối tạo badge không có nội dung vì `null` không render thành chữ.

Nếu nghiệp vụ muốn coi cả null và undefined là thiếu dữ liệu:

```jsx
function CustomerName({ name }) {
  return <span>{name ?? 'Chưa cập nhật'}</span>;
}
```

`??` giữ nguyên `0`, `false`, `''`; `||` thay thế cả những giá trị falsy đó. Với function component hiện đại, ưu tiên default parameter thay vì dùng mẫu `Component.defaultProps` cũ.

## 4. State là gì? Vì sao biến thông thường chưa đủ?

State là bộ nhớ của một instance component giữa các lần render. Nó lưu những dữ liệu thay đổi theo tương tác hoặc quá trình hoạt động: nội dung ô tìm kiếm, tab đang chọn, trạng thái mở modal, danh sách công việc.

Một biến thường trong thân component không đáp ứng hai điều quan trọng: giữ dữ liệu đúng cách qua các lần render và thông báo cho React khi cần tính lại giao diện.

```jsx
// Không dùng cách này để quản lý giao diện phản ứng với dữ liệu.
function CounterWrong() {
  let count = 0;

  return <button onClick={() => { count += 1; }}>{count}</button>;
}
```

Nhấn nút làm biến trong closure thay đổi nhưng không yêu cầu React render lại. Khi component được render lại vì lý do khác, `let count = 0` lại chạy.

```jsx
import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(current => current + 1)}>
      Đã nhấn {count} lần
    </button>
  );
}
```

`useState(0)` trả về cặp `[giá trị hiện tại, hàm yêu cầu cập nhật]`. `0` là giá trị khởi tạo. Setter yêu cầu React xử lý state và render với giá trị mới nếu cần. React có thể bỏ qua cập nhật khi giá trị mới bằng giá trị hiện tại theo Object.is.

Không dùng `count++`, không gán trực tiếp `count = 10`, không gọi setter vô điều kiện ngay trong thân render.

## 5. State số, chuỗi và boolean

```jsx
import { useState } from 'react';

export default function ProfileDemo() {
  const [name, setName] = useState('Tim');
  const [age, setAge] = useState(30);
  const [showDetails, setShowDetails] = useState(false);

  return (
    <section>
      <label>
        Tên
        <input value={name} onChange={event => setName(event.target.value)} />
      </label>
      <p>Xin chào {name}, {age} tuổi.</p>
      <button onClick={() => setAge(current => current + 1)}>Tăng tuổi</button>
      <button onClick={() => setShowDetails(current => !current)}>
        {showDetails ? 'Ẩn' : 'Hiện'} thông tin
      </button>
      {showDetails && <p>Đang học Props và State.</p>}
    </section>
  );
}
```

Khi thay bằng một giá trị mới từ sự kiện, `setName(newValue)` là rõ ràng. Khi tính từ giá trị trước, ưu tiên updater như `setAge(current => current + 1)`.

Hook useState/useReducer phải được gọi ở cấp cao nhất của function component hoặc custom hook, không nằm trong if, vòng lặp hay event handler. Tên component viết hoa. Các instance của cùng component có state độc lập.

```jsx
function App() {
  return <><ProfileDemo /><ProfileDemo /></>;
}
```

Đổi tên trong ProfileDemo thứ nhất không đổi tên ở instance thứ hai.

## 6. Props khác state như thế nào?

| Tiêu chí | Props | State |
|---|---|---|
| Vai trò | Đầu vào của component | Dữ liệu component quản lý |
| Nguồn | Nơi sử dụng truyền xuống | Hook như `useState`, `useReducer` |
| Cách thay đổi | Chủ sở hữu truyền giá trị mới | Gọi setter/dispatch |
| Component nhận được sửa trực tiếp không? | Không | Cũng không sửa trực tiếp |
| Ví dụ | `product`, `onBuy`, `disabled` | `quantity`, `isOpen`, `keyword` |

Dữ liệu có thể đồng thời là state ở cha và prop ở con. Trong ví dụ trước, `customer` là state của App và là prop của CustomerCard. Tên gọi phụ thuộc vào góc nhìn sở hữu.

Props không có nghĩa là bất biến vĩnh viễn: mỗi lần render, component có thể nhận một bộ giá trị mới.

## 7. Props chỉ đọc: không sửa dữ liệu đầu vào

Ví dụ không đúng:

```jsx
function CustomerCard({ customer }) {
  customer.name = 'Tên mới'; // Sửa object do nơi khác sở hữu.
  return <p>{customer.name}</p>;
}
```

Không sửa trực tiếp prop, kể cả thuộc tính bên trong object hoặc phần tử trong array. Object truyền qua props không tự được deep clone hay deep freeze; do đó đoạn sai có thể thực sự làm thay đổi dữ liệu dùng chung.

Nơi sở hữu state chịu trách nhiệm cập nhật:

```jsx
import { useState } from 'react';

function CustomerCard({ customer, onRename }) {
  return (
    <article>
      <p>{customer.name}</p>
      <button onClick={() => onRename('Tim Nguyen')}>Đổi tên</button>
    </article>
  );
}

export default function App() {
  const [customer, setCustomer] = useState({ id: 1, name: 'Tim' });

  function handleRename(name) {
    setCustomer(current => ({ ...current, name }));
  }

  return <CustomerCard customer={customer} onRename={handleRename} />;
}
```

Với object lồng nhau, sao chép từng tầng cần thay đổi:

```jsx
setCustomer(current => ({
  ...current,
  address: { ...current.address, city: 'Nha Trang' },
}));
```

Đoạn này giả định `customer.address` đã tồn tại. Spread chỉ sao chép nông, không tự sao chép mọi tầng.

## 8. Function props và giao tiếp từ con lên cha

Cha có thể truyền hàm để con gọi khi người dùng thao tác.

```jsx
function BuyButton({ productId, onBuy }) {
  return (
    <button onClick={() => onBuy(productId)}>
      Mua sản phẩm
    </button>
  );
}

export default function App() {
  function handleBuy(productId) {
    alert(`Đã chọn sản phẩm ${productId}`);
  }

  return <BuyButton productId="P001" onBuy={handleBuy} />;
}
```

Luồng thực thi: cha truyền `handleBuy`; con nhận dưới tên `onBuy`; người dùng nhấn; con gọi hàm kèm mã sản phẩm; logic của cha chạy. Đây vẫn là truyền props từ cha xuống con. Con gọi lại hàm được cung cấp, không tự sửa state của cha.

Phân biệt ba cách viết:

```jsx
<button onClick={handleSave}>Lưu</button>
<button onClick={() => handleSave(customer.id)}>Lưu</button>
<button onClick={handleSave(customer.id)}>Lưu</button>
```

Hai dòng đầu truyền hàm. Dòng thứ ba gọi hàm ngay khi render, thường là lỗi. Ngoại lệ là trường hợp có chủ đích: `handleSave(id)` là factory trả về một event handler.

Tên `onBuy`, `onSelect`, `onValueChange` là API do anh thiết kế cho custom component. DOM element như `<button>` dùng tên sự kiện React hỗ trợ như `onClick`.

## 9. State là snapshot của một lần render

```jsx
import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
    console.log(count);
  }

  return <button onClick={handleClick}>{count}</button>;
}
```

Lần nhấn đầu log 0, trong khi lần render tiếp theo hiển thị 1. Setter không thay đổi biến count trong event handler đang chạy. Handler thuộc về một lần render và nhìn thấy snapshot của lần đó.

Không nên giải thích đơn giản thành “setState là Promise bất đồng bộ”. Setter useState không trả về Promise để `await` chờ state mới.

Nếu cần dùng ngay giá trị đã tính:

```jsx
function handleClick() {
  const nextCount = count + 1;
  setCount(nextCount);
  console.log(nextCount);
}
```

Nếu cần đồng bộ với hệ thống bên ngoài sau khi state đã được commit, cân nhắc Effect với dependencies phù hợp. Không thêm Effect chỉ để làm phép tính có thể làm trong render.

## 10. Batching và functional update

Giả sử count hiện tại bằng 0:

```jsx
function addThreeWrong() {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
}
```

Cả ba biểu thức `count + 1` đều tính từ cùng snapshot 0, nên đều yêu cầu đặt giá trị 1. Kết quả là 1.

```jsx
function addThree() {
  setCount(current => current + 1);
  setCount(current => current + 1);
  setCount(current => current + 1);
}
```

React xử lý hàng đợi updater: 0 → 1 → 2 → 3. Batching là việc React gom các cập nhật để tránh những lần render không cần thiết; nó không biến ba lần đặt cùng một giá trị thành ba phép cộng.

Updater phải là hàm thuần: chỉ tính và trả về giá trị mới. Không gọi API, phát thông báo, tạo ID ngẫu nhiên hay sửa dữ liệu ngoài hàm trong updater. Trong development Strict Mode, React có thể gọi lại initializer/updater để phát hiện logic không thuần; điều đó không có nghĩa hai lần click đã xảy ra.

## 11. Object state: thay thế chứ không tự merge

```jsx
import { useState } from 'react';

export default function CustomerForm() {
  const [form, setForm] = useState({
    fullName: 'Tim Nguyen',
    email: '',
    address: { city: 'TP.HCM', district: '' },
  });

  return (
    <section>
      <input
        aria-label="Họ tên"
        value={form.fullName}
        onChange={event => {
          const fullName = event.target.value;
          setForm(current => ({ ...current, fullName }));
        }}
      />
      <input
        aria-label="Thành phố"
        value={form.address.city}
        onChange={event => {
          const city = event.target.value;
          setForm(current => ({
            ...current,
            address: { ...current.address, city },
          }));
        }}
      />
      <pre>{JSON.stringify(form, null, 2)}</pre>
    </section>
  );
}
```

Với useState, `setForm({ fullName: 'Tên mới' })` thay toàn bộ object state, không tự giữ email/address. Điều này khác với cơ chế shallow merge của `this.setState` trong class component cũ.

Khi cập nhật trường lồng nhau, cần tạo object mới từ tầng gốc đến tầng bị thay đổi. Với form rất sâu, cân nhắc làm phẳng cấu trúc, chia nhỏ state hoặc thư viện hỗ trợ immutable update nếu dự án cần.

## 12. Array state: thêm, sửa, xóa, sắp xếp

Ví dụ các handler dưới đây nằm trong component đã khai báo state:

```jsx
const [tasks, setTasks] = useState([
  { id: 'T01', title: 'Học props', done: false },
  { id: 'T02', title: 'Học state', done: false },
]);

function addTask(title) {
  const task = { id: crypto.randomUUID(), title, done: false };
  setTasks(current => [...current, task]);
}

function renameTask(id, title) {
  setTasks(current => current.map(task =>
    task.id === id ? { ...task, title } : task
  ));
}

function toggleTask(id) {
  setTasks(current => current.map(task =>
    task.id === id ? { ...task, done: !task.done } : task
  ));
}

function removeTask(id) {
  setTasks(current => current.filter(task => task.id !== id));
}

function sortTasks() {
  setTasks(current => [...current].sort((a, b) =>
    a.title.localeCompare(b.title)
  ));
}
```

`crypto.randomUUID()` ở đây chạy trong handler, cần browser hỗ trợ và secure context. Tạo ID trước updater để updater luôn thuần.

| Thao tác | Cách phù hợp với state |
|---|---|
| Thêm cuối | `[...current, item]` |
| Thêm đầu | `[item, ...current]` |
| Xóa | `filter` |
| Sửa phần tử | `map` và tạo object mới cho phần tử đổi |
| Sắp xếp | Sao chép rồi `sort` |
| Đảo thứ tự | Sao chép rồi `reverse` |

Không dùng push/splice/sort trực tiếp trên array đang là state. `[...tasks]` chỉ sao chép array; object bên trong vẫn dùng chung tham chiếu, nên không được sửa `copy[0].done = true`.

## 13. Thiết kế cấu trúc state để tránh dữ liệu mâu thuẫn

### Chỉ lưu dữ liệu nguồn, tính dữ liệu suy ra

```jsx
const [quantity, setQuantity] = useState(1);
const unitPrice = 100000;
const total = quantity * unitPrice;
```

Không cần thêm total state nếu total luôn suy ra được. Dùng useMemo chỉ khi phép tính có chi phí đáng kể hoặc cần ổn định tham chiếu phục vụ tối ưu đã đo.

### Lưu ID được chọn thay vì sao chép cả object

```jsx
const [customers, setCustomers] = useState(initialCustomers);
const [selectedId, setSelectedId] = useState(null);
const selectedCustomer = customers.find(item => item.id === selectedId) ?? null;
```

`initialCustomers` giả định là dữ liệu đầu vào đã có. Nếu danh sách cập nhật, selectedCustomer được suy ra từ bản mới; tránh một object selectedCustomer state riêng giữ bản sao cũ.

### Gom dữ liệu thay đổi cùng nhau

State độc lập như keyword và isHelpOpen có thể tách. Các trường liên quan chặt trong một form có thể gom thành object. Không có quy tắc rằng càng ít useState càng tốt.

### Mô tả trạng thái loại trừ nhau bằng union

```tsx
type SaveState =
  | { status: 'idle' }
  | { status: 'saving' }
  | { status: 'success' }
  | { status: 'error'; message: string };
```

Mô hình này tránh tổ hợp vừa saving vừa success do nhiều boolean rời rạc. Thiết kế theo các trạng thái nghiệp vụ thực sự cần biểu diễn.

## 14. Chọn nơi sở hữu state và kết hợp props với state

Ví dụ modal: nếu chỉ một component mở/đóng nó, state có thể nằm ngay tại component đó. Nếu toolbar mở modal và danh sách cũng cần phản ánh thao tác, tìm cha chung gần nhất.

```jsx
import { useState } from 'react';

function CustomerSelector({ customers, selectedId, onSelect }) {
  return (
    <select
      aria-label="Chọn khách hàng"
      value={selectedId}
      onChange={event => onSelect(event.target.value)}
    >
      <option value="">Chưa chọn</option>
      {customers.map(customer => (
        <option key={customer.id} value={customer.id}>{customer.name}</option>
      ))}
    </select>
  );
}

function CustomerDetail({ customer }) {
  return <p>{customer ? `Đang xem: ${customer.name}` : 'Hãy chọn khách hàng'}</p>;
}

export default function App() {
  const customers = [
    { id: 'C01', name: 'Tim' },
    { id: 'C02', name: 'Lan' },
  ];
  const [selectedId, setSelectedId] = useState('');
  const selectedCustomer = customers.find(item => item.id === selectedId);

  return (
    <>
      <CustomerSelector
        customers={customers}
        selectedId={selectedId}
        onSelect={setSelectedId}
      />
      <CustomerDetail customer={selectedCustomer} />
    </>
  );
}
```

State đi xuống dưới dạng props; sự kiện đi về chủ sở hữu bằng callback. Mỗi dữ liệu cần một nguồn quyết định rõ ràng. Chỉ đưa state lên cao tới mức cần chia sẻ, tránh đặt mọi thứ ở App khiến vùng render và sự phụ thuộc lớn hơn cần thiết.

## 15. Controlled component và lifting state up

Component controlled nhận giá trị hiện tại và callback yêu cầu thay đổi. Khi hai component cần cùng một dữ liệu, đặt state tại cha chung gần nhất.

```jsx
import { useState } from 'react';

function SearchBox({ value, onValueChange }) {
  return (
    <label>
      Tìm khách hàng
      <input
        value={value}
        onChange={event => onValueChange(event.target.value)}
      />
    </label>
  );
}

function SearchSummary({ keyword }) {
  return <p>Từ khóa hiện tại: {keyword || '(trống)'}</p>;
}

export default function App() {
  const [keyword, setKeyword] = useState('');

  return (
    <>
      <SearchBox value={keyword} onValueChange={setKeyword} />
      <SearchSummary keyword={keyword} />
    </>
  );
}
```

App giữ nguồn dữ liệu chung. SearchBox báo giá trị mới, App cập nhật state, cả hai con nhận props mới.

Đối với text input controlled, khởi tạo bằng `''` thay vì `undefined`; cập nhật value trong onChange, hoặc dùng readOnly nếu chỉ đọc. Checkbox dùng `checked` và `event.target.checked`.

Uncontrolled input dùng `defaultValue` để đặt giá trị khởi đầu và DOM quản lý giá trị tiếp theo:

```jsx
import { useRef } from 'react';

export default function App() {
  const inputRef = useRef(null);

  return (
    <>
      <input ref={inputRef} aria-label="Tên" defaultValue="Tim" />
      <button onClick={() => alert(inputRef.current?.value)}>Đọc tên</button>
    </>
  );
}
```

Thay đổi `defaultValue` không biến input thành controlled và không điều khiển nội dung người dùng đã nhập như `value`.

## 16. Không sao chép props vào state một cách máy móc

```jsx
function CustomerEditor({ name }) {
  const [localName, setLocalName] = useState(name);
  // ...
}
```

`useState(name)` chỉ dùng name làm giá trị khởi tạo của lần mount. Nếu cha truyền name mới, localName không tự thay đổi. Trong ví dụ rút gọn này cần import useState nếu triển khai.

Chọn theo nhu cầu:

1. Chỉ hiển thị: dùng trực tiếp `name`.
2. Cha điều khiển form: dùng `value` và `onValueChange`.
3. Cần bản nháp độc lập: dùng state riêng và xác định chính sách khi dữ liệu gốc thay đổi.
4. Chuyển khách hàng và muốn bỏ bản nháp cũ: có thể đổi key.

```jsx
<CustomerEditor key={customer.id} name={customer.name} />
```

Đổi key remount component, reset toàn bộ state bên trong. Nếu cùng customer.id nhưng dữ liệu server thay đổi, key này không reset; cần quyết định có giữ bản nháp hay đồng bộ lại. Không thêm useEffect đồng bộ tự động nếu điều đó có thể ghi đè phần người dùng đang sửa.

Dữ liệu suy ra nên tính lúc render:

```jsx
function FullName({ firstName, lastName }) {
  const fullName = `${firstName} ${lastName}`;
  return <p>{fullName}</p>;
}
```

Không cần thêm state và effect chỉ để ghép tên.

## 17. children: truyền nội dung vào component

`children` chứa nội dung đặt giữa hai thẻ component.

```jsx
function Card({ title, children }) {
  return (
    <section className="card">
      <h2>{title}</h2>
      <div>{children}</div>
    </section>
  );
}

export default function App() {
  return (
    <Card title="Thông tin khách hàng">
      <p>Họ tên: Tim Nguyen</p>
      <button>Xem chi tiết</button>
    </Card>
  );
}
```

Card quản lý khung; nơi sử dụng quyết định nội dung. Đây là composition: ghép component để tạo giao diện lớn hơn. `children` có thể là text, element, danh sách node hoặc nội dung rỗng; không nên mặc định nó luôn là một object có `.props`.

## 18. Named slots: nhiều vùng giao diện

Khi có nhiều vùng với vai trò khác nhau, đặt tên props rõ ràng:

```jsx
function Panel({ title, actions, children, footer }) {
  return (
    <section>
      <header>
        <h2>{title}</h2>
        <div>{actions}</div>
      </header>
      <div>{children}</div>
      {footer != null && <footer>{footer}</footer>}
    </section>
  );
}

export default function App() {
  return (
    <Panel
      title="Danh sách lệnh"
      actions={<button>Làm mới</button>}
      footer={<small>Đang hiển thị 10 lệnh</small>}
    >
      <p>Nội dung bảng lệnh ở đây.</p>
    </Panel>
  );
}
```

Named slots phù hợp với Card, Modal, PageLayout, bảng có toolbar. Đừng tạo nhiều boolean để mô tả mọi tổ hợp giao diện khi có thể truyền nội dung trực tiếp.

## 19. Array props, render danh sách và key

```jsx
function CustomerRow({ customerId, name }) {
  return <li>{customerId} — {name}</li>;
}

function CustomerList({ customers }) {
  if (customers.length === 0) return <p>Chưa có khách hàng.</p>;

  return (
    <ul>
      {customers.map(customer => (
        <CustomerRow
          key={customer.id}
          customerId={customer.id}
          name={customer.name}
        />
      ))}
    </ul>
  );
}

export default function App() {
  const customers = [
    { id: 'C001', name: 'Tim' },
    { id: 'C002', name: 'Lan' },
  ];

  return <CustomerList customers={customers} />;
}
```

`key` giúp React nhận diện phần tử giữa các lần render; component con không đọc được `props.key`. Nếu cần ID, truyền thêm `customerId` hoặc `id`.

Key chỉ cần duy nhất giữa các phần tử cùng danh sách. Tránh index khi có thêm, xóa, sắp xếp; tránh `Math.random()` vì key thay đổi sẽ làm mất tính ổn định của component. Key cũng có thể dùng để chủ động reset state khi chuyển sang một thực thể khác.

## 20. Conditional props và lỗi render số 0

```jsx
function SaveButton({ isSaving, canSave }) {
  return (
    <button disabled={isSaving || !canSave}>
      {isSaving ? 'Đang lưu...' : 'Lưu'}
    </button>
  );
}
```

Một lỗi dễ gặp:

```jsx
// Nếu count = 0, React hiển thị 0.
{count && <span>Có dữ liệu</span>}

// Điều kiện rõ ràng hơn.
{count > 0 && <span>Có dữ liệu</span>}
```

Ẩn nút bằng props quyền hạn chỉ kiểm soát giao diện. Backend vẫn cần kiểm tra quyền cho từng thao tác.

## 21. Spread props và thứ tự ghi đè

```jsx
const buttonProps = { disabled: true, title: 'Không thể lưu' };

<button {...buttonProps} disabled={false}>Lưu</button>
<button disabled={false} {...buttonProps}>Lưu</button>
```

Dòng đầu enabled; dòng sau disabled. Giá trị nằm sau thắng khi cùng tên.

Spread phù hợp với wrapper component, nhưng tránh truyền cả DTO lớn vào một thẻ DOM. Tách props thuộc component trước:

```jsx
function Button({ variant = 'primary', children, ...buttonProps }) {
  return (
    <button
      {...buttonProps}
      className={`btn btn-${variant} ${buttonProps.className ?? ''}`}
    >
      {children}
    </button>
  );
}
```

`variant` được dùng để tạo class, không bị truyền thành thuộc tính DOM ngoài ý muốn. Wrapper cần có quy tắc rõ về className, disabled và event handler.

## 22. TypeScript: mô tả hợp đồng của props

```tsx
type Customer = {
  id: string;
  fullName: string;
};

type CustomerCardProps = {
  customer: Customer;
  compact?: boolean;
  onSelect: (customerId: string) => void;
};

function CustomerCard({
  customer,
  compact = false,
  onSelect,
}: CustomerCardProps) {
  return (
    <article>
      <h2>{customer.fullName}</h2>
      {!compact && <p>Mã khách hàng: {customer.id}</p>}
      <button onClick={() => onSelect(customer.id)}>Chọn</button>
    </article>
  );
}

export default function App() {
  return (
    <CustomerCard
      customer={{ id: 'C001', fullName: 'Tim Nguyen' }}
      onSelect={id => console.log(id)}
    />
  );
}
```

`?` đánh dấu prop tùy chọn. Union như `'primary' | 'danger'` giới hạn giá trị. Kiểu hàm cho biết con gửi payload gì khi gọi callback.

TypeScript kiểm tra lúc phát triển/build; nó không tự xác thực JSON lấy từ API. Dữ liệu bên ngoài cần validation ở ranh giới phù hợp.

Với children:

```tsx
import type { ReactNode } from 'react';

type CardProps = {
  title: string;
  children: ReactNode;
};
```

ReactNode phù hợp với nội dung có thể render; ReactElement hẹp hơn. Function không phải nội dung render trực tiếp: muốn truyền hàm làm children, component phải chủ động gọi hàm đó.

## 23. Kế thừa props HTML bằng TypeScript

Một Button tái sử dụng thường cần các props chuẩn như `type`, `disabled`, `aria-label`, `onClick`.

```tsx
import type { ComponentPropsWithoutRef } from 'react';

type ButtonProps = ComponentPropsWithoutRef<'button'> & {
  variant?: 'primary' | 'danger';
  loading?: boolean;
};

function Button({
  variant = 'primary',
  loading = false,
  disabled = false,
  type = 'button',
  className = '',
  children,
  ...rest
}: ButtonProps) {
  return (
    <button
      {...rest}
      type={type}
      className={`btn btn-${variant} ${className}`}
      disabled={disabled || loading}
      aria-busy={loading}
    >
      {loading ? 'Đang xử lý...' : children}
    </button>
  );
}

export default function App() {
  return (
    <Button variant="danger" onClick={() => alert('Xóa')}>
      Xóa khách hàng
    </Button>
  );
}
```

Ví dụ chọn mặc định type=button để không vô tình submit form. Các class btn cần CSS của dự án để có hình thức tương ứng. Đây là wrapper không cung cấp ref; phần ref ở dưới có ví dụ riêng.

## 24. Discriminated union: ngăn tổ hợp props không hợp lệ

Ví dụ trạng thái tải dữ liệu nên mô tả các trạng thái hợp lệ thay vì nhiều boolean rời rạc:

```tsx
type Customer = { id: string; name: string };

type CustomerResultProps =
  | { status: 'loading' }
  | { status: 'error'; message: string; onRetry: () => void }
  | { status: 'success'; customers: Customer[] };

function CustomerResult(props: CustomerResultProps) {
  switch (props.status) {
    case 'loading':
      return <p>Đang tải...</p>;
    case 'error':
      return (
        <div role="alert">
          <p>{props.message}</p>
          <button onClick={props.onRetry}>Thử lại</button>
        </div>
      );
    case 'success':
      return (
        <ul>
          {props.customers.map(customer => (
            <li key={customer.id}>{customer.name}</li>
          ))}
        </ul>
      );
  }
}
```

```tsx
<CustomerResult status="loading" />
<CustomerResult status="error" message="Không tải được" onRetry={() => {}} />
<CustomerResult status="success" customers={[]} />

// TypeScript báo lỗi vì thiếu customers:
// <CustomerResult status="success" />
```

Đây là lựa chọn thiết kế API component: kiểu dữ liệu giúp loại bỏ một số trạng thái sai ngay lúc viết code.

## 25. Generic props: tái sử dụng nhưng giữ đúng kiểu

```tsx
import type { Key, ReactNode } from 'react';

type DataListProps<T> = {
  items: readonly T[];
  getKey: (item: T) => Key;
  renderItem: (item: T) => ReactNode;
  emptyText?: string;
};

function DataList<T>({
  items,
  getKey,
  renderItem,
  emptyText = 'Chưa có dữ liệu',
}: DataListProps<T>) {
  if (items.length === 0) return <p>{emptyText}</p>;

  return (
    <ul>
      {items.map(item => <li key={getKey(item)}>{renderItem(item)}</li>)}
    </ul>
  );
}

export default function App() {
  const customers = [{ id: 'C001', fullName: 'Tim Nguyen' }];

  return (
    <DataList
      items={customers}
      getKey={customer => customer.id}
      renderItem={customer => <strong>{customer.fullName}</strong>}
    />
  );
}
```

TypeScript suy ra T từ items. Khi renderItem nhận customer, IDE biết có id và fullName. Component quản lý danh sách; nơi dùng quản lý cách hiển thị từng item. Hàm getKey vẫn phải trả về key ổn định và duy nhất trong danh sách.

## 26. Render props: truyền cách render bằng hàm

Khác với truyền JSX đã tạo sẵn, render prop cho phép component cung cấp dữ liệu cho hàm render.

```tsx
import { useState } from 'react';
import type { ReactNode } from 'react';

type ToggleProps = {
  children: (args: { open: boolean; toggle: () => void }) => ReactNode;
};

function Toggle({ children }: ToggleProps) {
  const [open, setOpen] = useState(false);
  return children({ open, toggle: () => setOpen(current => !current) });
}

export default function App() {
  return (
    <Toggle>
      {({ open, toggle }) => (
        <section>
          <button onClick={toggle}>{open ? 'Ẩn' : 'Hiện'} chi tiết</button>
          {open && <p>Thông tin bổ sung</p>}
        </section>
      )}
    </Toggle>
  );
}
```

Toggle gọi children như một hàm; không đặt thẳng `{children}` vào JSX. Mẫu này phù hợp khi thư viện cung cấp logic nhưng cho phép người dùng tự tạo UI. Custom hook cũng có thể chia sẻ logic; chọn cách làm API dễ đọc nhất cho tình huống cụ thể.

## 27. Initial props, bản nháp và controlled/uncontrolled API

Tên prop nên nói rõ ý nghĩa. `value` thường biểu thị giá trị hiện tại do cha điều khiển; `defaultValue` hoặc `initialValue` biểu thị giá trị khởi tạo.

Ví dụ TypeScript cho một Counter hỗ trợ hai cách dùng nhưng không cho trộn cả hai:

```tsx
import { useState } from 'react';

type CounterProps =
  | {
      value: number;
      onValueChange: (value: number) => void;
      defaultValue?: never;
    }
  | {
      value?: never;
      onValueChange?: never;
      defaultValue?: number;
    };

function Counter(props: CounterProps) {
  // Hook luôn được gọi, không đặt trong if.
  const [internalValue, setInternalValue] = useState(props.defaultValue ?? 0);
  const value = props.value !== undefined ? props.value : internalValue;

  function increment() {
    const nextValue = value + 1;
    if (props.value !== undefined) {
      props.onValueChange(nextValue);
    } else {
      setInternalValue(nextValue);
    }
  }

  return <button onClick={increment}>{value}</button>;
}

export default function App() {
  const [count, setCount] = useState(10);
  return (
    <>
      <Counter value={count} onValueChange={setCount} />
      <Counter defaultValue={5} />
    </>
  );
}
```

Counter đầu dùng state của cha; Counter sau dùng state nội bộ. Ví dụ minh họa mỗi lần click tăng một đơn vị, không cung cấp API hàng đợi nhiều yêu cầu tăng cùng snapshot. Trong thư viện thực tế, có thể thiết kế callback hành động onIncrement nếu cần ngữ nghĩa tăng thay vì chỉ báo giá trị mới. Không đổi một instance từ controlled sang uncontrolled giữa chừng.

Bản nháp chỉnh sửa hồ sơ là một trường hợp hợp lệ để khởi tạo state từ props, nhưng cần nói rõ khi nào reset, khi nào giữ thay đổi chưa lưu, và xử lý cập nhật từ server ra sao.

## 28. Giữ và reset state: vị trí, type và key

React gắn state với danh tính component trong cây render, dựa vào vị trí, type và key. Render lại không có nghĩa state được tạo lại từ đầu.

```jsx
<CustomerEditor key={selectedCustomer.id} initialCustomer={selectedCustomer} />
```

Khi key đổi sang khách hàng khác, editor được tạo lại và bỏ state bản nháp cũ. Với cùng key, đổi initialCustomer không tự khởi tạo lại useState.

Nếu component bị unmount rồi mount lại, state cục bộ được tạo mới. Không khai báo một component có state bên trong thân component cha, vì mỗi lần render có thể tạo một function type mới và khiến React reset state ngoài ý muốn.

```jsx
// Đặt Editor ở phạm vi module, ngoài Page.
function Editor() { /* ... */ }
function Page() { return <Editor />; }
```

State cục bộ cũng không tự tồn tại sau reload trang. Muốn giữ lâu hơn cần chọn nơi lưu phù hợp như URL, storage hoặc server, tùy bản chất dữ liệu.

## 29. Lazy initialization và state so với ref

Nếu tạo giá trị khởi đầu tốn chi phí:

```jsx
function buildInitialRows() {
  return Array.from({ length: 1000 }, (_, index) => ({
    id: `R${index}`,
    selected: false,
  }));
}

// Truyền hàm initializer.
const [rows, setRows] = useState(buildInitialRows);

// Tránh gọi phép tính này trong mọi lần render:
// const [rows, setRows] = useState(buildInitialRows());
```

Initializer phải thuần; trong Strict Mode development, React có thể gọi thêm để kiểm tra. Không đọc localStorage/browser API trong initializer của component có thể render ở server mà không có chiến lược SSR/hydration phù hợp.

| Nhu cầu | Công cụ |
|---|---|
| Giá trị thay đổi cần phản ánh lên UI | State |
| Tham chiếu DOM để focus | Ref |
| Timer ID hoặc dữ liệu mutable không dùng để render | Ref có thể phù hợp |
| Giá trị tính được từ props/state | Biến tính lúc render, đôi khi useMemo |

Thay đổi ref.current không yêu cầu React render lại. Đừng dùng ref thay state chỉ để tránh render rồi mong giao diện tự cập nhật.

## 30. useReducer: quản lý nhiều chuyển đổi state có liên quan

Khi nhiều handler cùng cập nhật một cấu trúc và quy tắc bắt đầu phức tạp, reducer giúp gom logic chuyển đổi vào một hàm thuần. useReducer không tự làm state thành global và không tự nhanh hơn useState.

```tsx
import { useReducer, useState } from 'react';

type Task = { id: string; title: string; done: boolean };
type Action =
  | { type: 'added'; task: Task }
  | { type: 'toggled'; id: string }
  | { type: 'removed'; id: string };

function tasksReducer(state: Task[], action: Action): Task[] {
  switch (action.type) {
    case 'added':
      return [...state, action.task];
    case 'toggled':
      return state.map(task => task.id === action.id
        ? { ...task, done: !task.done }
        : task
      );
    case 'removed':
      return state.filter(task => task.id !== action.id);
  }
}

type TaskItemProps = {
  task: Task;
  onToggle: (id: string) => void;
  onRemove: (id: string) => void;
};

function TaskItem({ task, onToggle, onRemove }: TaskItemProps) {
  return (
    <li>
      <label>
        <input
          type="checkbox"
          checked={task.done}
          onChange={() => onToggle(task.id)}
        />
        {task.title}
      </label>
      <button onClick={() => onRemove(task.id)}>Xóa</button>
    </li>
  );
}

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, []);
  const [title, setTitle] = useState('');

  function addTask() {
    const trimmedTitle = title.trim();
    if (!trimmedTitle) return;
    dispatch({
      type: 'added',
      task: { id: crypto.randomUUID(), title: trimmedTitle, done: false },
    });
    setTitle('');
  }

  const remaining = tasks.filter(task => !task.done).length;

  return (
    <section>
      <input
        aria-label="Công việc mới"
        value={title}
        onChange={event => setTitle(event.target.value)}
      />
      <button onClick={addTask}>Thêm</button>
      <p>Còn {remaining} việc chưa hoàn thành.</p>
      <ul>
        {tasks.map(task => (
          <TaskItem
            key={task.id}
            task={task}
            onToggle={id => dispatch({ type: 'toggled', id })}
            onRemove={id => dispatch({ type: 'removed', id })}
          />
        ))}
      </ul>
    </section>
  );
}
```

TaskApp giữ state; TaskItem nhận props và gọi callback; dispatch đưa action vào reducer; reducer tạo state tiếp theo. ID được tạo trong event handler. Không gọi API, sửa localStorage hoặc tạo ID bên trong reducer.

Nếu cần nhiều nhánh dùng chung state/action, có thể kết hợp reducer với Context. Provider quản lý state; component sâu đọc state và dispatch qua custom hook. State thay đổi vẫn có thể khiến các consumer render; Context không phải miễn phí về hiệu năng.

## 31. Custom hook chia sẻ logic, không tự chia sẻ state

```jsx
import { useState } from 'react';

function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);
  function increment() { setCount(current => current + 1); }
  function reset() { setCount(initialValue); }
  return { count, increment, reset };
}

function Counter() {
  const { count, increment, reset } = useCounter(0);
  return (
    <div>
      <button onClick={increment}>{count}</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}

export default function App() {
  return <><Counter /><Counter /></>;
}
```

Hai Counter dùng cùng hook nhưng mỗi lần gọi hook có state riêng. Muốn chúng đồng bộ, đưa một lần gọi hook lên cha rồi truyền props xuống, hoặc dùng provider/store chung có chủ đích. Trong hook ví dụ, reset dùng initialValue của lần render hiện tại; nếu muốn luôn reset về giá trị lúc mount, cần thiết kế và lưu riêng giá trị gốc đó.

## 32. Prop drilling: composition trước, Context khi phù hợp

Prop drilling xảy ra khi dữ liệu phải đi qua nhiều component trung gian không dùng nó.

```jsx
function Page({ customer }) {
  return <Layout customer={customer} />;
}

function Layout({ customer }) {
  return <Sidebar customer={customer} />;
}

function Sidebar({ customer }) {
  return <p>{customer.name}</p>;
}
```

Nếu Layout chỉ cần bố cục, composition có thể giảm việc truyền tiếp:

```jsx
function Layout({ sidebar, children }) {
  return <div><aside>{sidebar}</aside><main>{children}</main></div>;
}

function Page({ customer }) {
  return (
    <Layout sidebar={<p>{customer.name}</p>}>
      <p>Nội dung trang</p>
    </Layout>
  );
}
```

Nếu nhiều nhánh cần cùng dữ liệu xuyên suốt cây, cân nhắc Context:

```jsx
import { createContext, useContext } from 'react';

const ThemeContext = createContext('light');

function Toolbar() {
  const theme = useContext(ThemeContext);
  return <div className={`toolbar-${theme}`}>Thanh công cụ</div>;
}

export default function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}
```

Props phù hợp với đầu vào cụ thể; Context phù hợp với thông tin dùng xuyên nhiều tầng như theme hoặc locale. Context không tự là giải pháp tối ưu render: consumer có thể render lại khi provider value thay đổi. Không đưa tất cả state vào một Context lớn chỉ để tránh truyền props.

## 33. State với dữ liệu bất đồng bộ và race condition

Race condition thường gặp: người dùng chọn A rồi B; request B trả trước; request A trả sau và ghi đè giao diện bằng dữ liệu cũ.

Ví dụ minh họa dưới đây giả định có API `GET /api/customers/:id` trả JSON `{ id, name }`. Endpoint chưa được triển khai trong tài liệu.

```jsx
import { useEffect, useState } from 'react';

export default function CustomerDetail({ customerId }) {
  const [result, setResult] = useState({
    customerId: null,
    status: 'idle',
    data: null,
    error: '',
  });

  useEffect(() => {
    if (!customerId) return;

    const controller = new AbortController();
    let ignore = false;

    setResult({ customerId, status: 'loading', data: null, error: '' });

    async function load() {
      try {
        const response = await fetch(
          `/api/customers/${encodeURIComponent(customerId)}`,
          { signal: controller.signal }
        );
        if (!response.ok) throw new Error(`HTTP ${response.status}`);
        const data = await response.json();
        if (!ignore) {
          setResult({ customerId, status: 'success', data, error: '' });
        }
      } catch (error) {
        if (!ignore) {
          setResult({
            customerId,
            status: 'error',
            data: null,
            error: error instanceof Error ? error.message : 'Không tải được',
          });
        }
      }
    }

    load();
    return () => {
      ignore = true;
      controller.abort();
    };
  }, [customerId]);

  if (!customerId) return <p>Hãy chọn khách hàng.</p>;
  if (result.customerId !== customerId || result.status === 'loading') {
    return <p>Đang tải...</p>;
  }
  if (result.status === 'error') return <p role="alert">{result.error}</p>;
  return <p>{result.data?.name}</p>;
}
```

Cleanup đánh dấu bỏ qua kết quả cũ và yêu cầu hủy fetch; việc gắn customerId vào kết quả tránh hiển thị dữ liệu của người trước trong lần render chuyển ID. JSON cần được xác thực nếu tích hợp production. Với hệ thống thật, ưu tiên cơ chế lấy dữ liệu của framework hoặc thư viện quản lý server data phù hợp khi cần cache, retry và chống request trùng. Ví dụ dùng Effect để minh họa sự đồng bộ giữa prop customerId với hệ thống API bên ngoài.

## 34. Props và re-render: memo, useMemo, useCallback

Khi cha render lại, các component con thông thường cũng có thể được render lại dù giá trị props không đổi. Render lại là React tính toán giao diện; không có nghĩa mọi DOM node đều được sửa.

`memo` có thể giúp bỏ qua render từ cha khi props không đổi. Mặc định React so sánh từng prop bằng Object.is, không deep-compare nội dung object. Đây là tối ưu hiệu năng, không phải bảo đảm component không bao giờ render lại; state hoặc context mà con dùng vẫn có thể làm con render.

```jsx
import { memo, useCallback, useMemo, useState } from 'react';

const CustomerList = memo(function CustomerList({ customers, onSelect }) {
  console.log('CustomerList render');

  return (
    <ul>
      {customers.map(customer => (
        <li key={customer.id}>
          <button onClick={() => onSelect(customer.id)}>
            {customer.name}
          </button>
        </li>
      ))}
    </ul>
  );
});

export default function App() {
  const [customers] = useState([
    { id: 'C001', name: 'Tim' },
    { id: 'C002', name: 'Lan' },
  ]);
  const [keyword, setKeyword] = useState('');
  const [count, setCount] = useState(0);

  const visibleCustomers = useMemo(() => {
    const query = keyword.toLowerCase();
    return customers.filter(customer =>
      customer.name.toLowerCase().includes(query)
    );
  }, [customers, keyword]);

  const handleSelect = useCallback(id => {
    console.log('Chọn:', id);
  }, []);

  return (
    <>
      <input
        aria-label="Tìm khách hàng"
        value={keyword}
        onChange={event => setKeyword(event.target.value)}
      />
      <button onClick={() => setCount(current => current + 1)}>
        Bộ đếm: {count}
      </button>
      <CustomerList customers={visibleCustomers} onSelect={handleSelect} />
    </>
  );
}
```

Khi chỉ count đổi, useMemo thường giữ cùng array và useCallback giữ cùng function; memo có thể bỏ qua render danh sách. Khi keyword đổi, danh sách tính lại và con nhận prop mới. Trong development Strict Mode có thể xuất hiện thêm log do các kiểm tra của React.

Các lỗi cần tránh:

- Tạo `options={{ pageSize: 20 }}` hoặc `onSelect={() => ...}` ngay tại JSX rồi mong memo luôn bỏ qua render: mỗi lần tạo là tham chiếu mới.
- Bỏ dependency để ép callback ổn định: hàm có thể giữ dữ liệu cũ, gọi là stale closure.
- Viết custom comparator chỉ kiểm tra array mà bỏ qua callback: con có thể giữ callback cũ.
- Dùng memo/useMemo/useCallback khắp nơi mà không đo: code khó đọc và bản thân việc tối ưu cũng có chi phí.

Nếu dự án đã bật React Compiler, nhiều tối ưu có thể được thực hiện tự động. Đừng giả định mọi dự án đều đã bật compiler. Dùng React DevTools Profiler để xác định phần chậm trước khi tối ưu thủ công.

## 35. State nâng cao: stale closure, render ưu tiên và ranh giới lưu trữ

### Stale closure trong timer

```jsx
import { useEffect, useState } from 'react';

export default function Clock() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const timerId = setInterval(() => {
      setSeconds(current => current + 1);
    }, 1000);
    return () => clearInterval(timerId);
  }, []);

  return <p>{seconds} giây</p>;
}
```

Nếu thay updater bằng `setSeconds(seconds + 1)` trong Effect có dependencies rỗng, closure giữ seconds của lần thiết lập đầu. Functional update xử lý trường hợp tính từ state trước. Nó không tự sửa mọi stale closure của các biến khác; dependencies vẫn phải đúng khi Effect dùng các giá trị reactive khác.

### Giữ ô nhập phản hồi nhanh khi phần kết quả render nặng

```jsx
import { memo, useDeferredValue, useState } from 'react';

const SearchResults = memo(function SearchResults({ items, query }) {
  const rows = items.filter(item =>
    item.name.toLowerCase().includes(query.toLowerCase())
  );
  return <ul>{rows.map(item => <li key={item.id}>{item.name}</li>)}</ul>;
});

export default function SearchPage({ items }) {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);
  const isStale = query !== deferredQuery;

  return (
    <section>
      <input
        aria-label="Tìm kiếm"
        value={query}
        onChange={event => setQuery(event.target.value)}
      />
      <div style={{ opacity: isStale ? 0.5 : 1 }}>
        <SearchResults items={items} query={deferredQuery} />
      </div>
    </section>
  );
}
```

Query cập nhật ngay cho input; deferredQuery cho phép phần kết quả cập nhật với ưu tiên thấp hơn. Ví dụ cần items ổn định nếu muốn tận dụng memo khi gõ. Cách này không tự giảm số request mạng, không có thời gian chờ cố định như debounce, không làm vòng lặp CPU đồng bộ trở thành tác vụ có thể ngắt giữa chừng. Với dữ liệu lớn, vẫn cần cân nhắc pagination, virtualization hoặc đưa xử lý nặng ra khỏi main thread.

### Chọn đúng nơi lưu từng loại state

| Loại dữ liệu | Nơi thường phù hợp | Ví dụ |
|---|---|---|
| UI tạm thời, dùng cục bộ | useState/useReducer | Modal mở, bản nháp form |
| UI dùng chung nhiều nhánh | Cha chung, Context hoặc store theo nhu cầu | Lựa chọn đang áp dụng toàn màn hình |
| Cần chia sẻ qua link/back/refresh | URL/search params | Bộ lọc, trang hiện tại |
| Dữ liệu do server sở hữu | Framework/data cache và server | Khách hàng, trạng thái lệnh |
| Cần giữ qua lần mở trình duyệt | Storage hoặc server theo yêu cầu | Tùy chọn hiển thị không nhạy cảm |

Đây là hướng dẫn thiết kế, không phải luật cứng. Client cache không thay thế nguồn dữ liệu chính trên server. Trong Next.js, component có state/tương tác thuộc cây client; initial data có thể đến từ server qua props. 'use client' không có nghĩa mọi mã trong file chỉ có thể được thực thi sau khi trình duyệt mở trang; cần tính đến bước render ban đầu và hydration.

## 36. ref: khác biệt React 18 và React 19+

Trong React 19+, function component có thể nhận ref như prop để chuyển tiếp tới DOM:

```tsx
import { useRef } from 'react';
import type { ComponentPropsWithRef } from 'react';

function TextInput({ ref, ...props }: ComponentPropsWithRef<'input'>) {
  return <input {...props} ref={ref} />;
}

export default function App() {
  const inputRef = useRef<HTMLInputElement>(null);

  return (
    <>
      <TextInput ref={inputRef} aria-label="Tên khách hàng" />
      <button onClick={() => inputRef.current?.focus()}>Focus</button>
    </>
  );
}
```

React 18 và trước đó dùng forwardRef cho function component:

```tsx
import { forwardRef } from 'react';
import type { ComponentPropsWithoutRef } from 'react';

const TextInput = forwardRef<
  HTMLInputElement,
  ComponentPropsWithoutRef<'input'>
>(function TextInput(props, ref) {
  return <input {...props} ref={ref} />;
});
```

Kiểm tra phiên bản React runtime và type definitions. Không áp dụng quy tắc cũ “function component không thể nhận ref trong props” cho React 19+. `key` vẫn không trở thành prop nghiệp vụ mà component có thể đọc.

## 37. Props trong Next.js App Router

Trong cùng cây client, truyền callback giữa các component vẫn là cách dùng React thông thường. Ranh giới Server Component → Client Component có thêm quy tắc: props phải thuộc nhóm giá trị React có thể serialize. Nhóm này không chỉ giới hạn ở JSON; React hỗ trợ thêm một số kiểu và React elements. Với người mới, plain object, array, string, number, boolean là lựa chọn dễ kiểm soát.

### Ví dụ chạy bằng dữ liệu mẫu

File `app/customers/page.tsx`:

```tsx
import CustomerPicker from './CustomerPicker';

export default function CustomersPage() {
  // Dữ liệu mẫu. Khi tích hợp thật, truy vấn API/DB tại server ở đây.
  const customers = [
    { id: 'C001', fullName: 'Tim Nguyen' },
    { id: 'C002', fullName: 'Lan Tran' },
  ];

  return <CustomerPicker customers={customers} />;
}
```

File `app/customers/CustomerPicker.tsx`:

```tsx
'use client';

import { useState } from 'react';

type Customer = { id: string; fullName: string };

export default function CustomerPicker({
  customers,
}: {
  customers: Customer[];
}) {
  const [selectedId, setSelectedId] = useState<string | null>(null);

  return (
    <section>
      <p>Đã chọn: {selectedId ?? 'Chưa chọn'}</p>
      {customers.map(customer => (
        <button
          key={customer.id}
          onClick={() => setSelectedId(customer.id)}
        >
          {customer.fullName}
        </button>
      ))}
    </section>
  );
}
```

Server chuẩn bị dữ liệu; client xử lý tương tác. Không truyền hàm JavaScript thông thường từ Server Component sang Client Component để làm event handler:

```tsx
// Không hợp lệ nếu handleSelect chỉ là hàm thường được tạo tại server:
// <CustomerPicker onSelect={handleSelect} />
```

Hãy định nghĩa handler tại client. Server Function được đánh dấu và sử dụng đúng cơ chế framework là ngoại lệ cho việc truyền tham chiếu hàm; nó không biến mọi closure server thành callback chạy trong browser.

Không truyền database connection, request object, secret hoặc toàn bộ entity chứa trường nhạy cảm xuống client. Chọn rõ dữ liệu cho giao diện:

```tsx
const customerView = {
  id: customer.id,
  fullName: customer.fullName,
};
```

Nội dung được truyền tới client có thể được người dùng quan sát. Lọc dữ liệu ở server trước khi tạo props.

### Truyền Server Component qua children

Server Component có thể tạo nội dung và truyền qua children/slot cho Client Component. Client không cần import Server Component trực tiếp.

```tsx
// app/Disclosure.tsx
'use client';
import { useState } from 'react';
import type { ReactNode } from 'react';

export default function Disclosure({ children }: { children: ReactNode }) {
  const [open, setOpen] = useState(false);
  return (
    <section>
      <button onClick={() => setOpen(current => !current)}>Bật/tắt</button>
      {open && children}
    </section>
  );
}
```

```tsx
// app/page.tsx — Server Component
import Disclosure from './Disclosure';

function ServerSummary() {
  return <p>Nội dung được tạo ở server.</p>;
}

export default function Page() {
  return <Disclosure><ServerSummary /></Disclosure>;
}
```

Đóng Disclosure không có nghĩa React trì hoãn toàn bộ công việc server đến khi người dùng mở. Cách truyền slot này nói về composition và ranh giới render, không tự là lazy loading.

## 38. Ví dụ tổng hợp: danh sách sản phẩm và giỏ hàng

Ví dụ độc lập dưới đây kết hợp object props, array props, callback, state, immutable update, giá trị mặc định, children, controlled input, key và dữ liệu suy ra. Có thể dùng trong App.jsx của ứng dụng React. Trong Next.js, đặt nó trong một module Client Component với dòng 'use client' ở đầu.

```jsx
import { useState } from 'react';

const PRODUCTS = [
  { id: 'P01', name: 'Bàn phím', price: 500000 },
  { id: 'P02', name: 'Chuột', price: 300000 },
  { id: 'P03', name: 'Màn hình', price: 3500000 },
];

const money = new Intl.NumberFormat('vi-VN', {
  style: 'currency',
  currency: 'VND',
});

function Card({ title, children }) {
  return <section><h2>{title}</h2>{children}</section>;
}

function SearchBox({ value, onValueChange }) {
  return (
    <label>
      Tìm sản phẩm
      <input
        value={value}
        onChange={event => onValueChange(event.target.value)}
      />
    </label>
  );
}

function ProductCard({ product, quantity = 0, onAdd }) {
  return (
    <article>
      <h3>{product.name}</h3>
      <p>{money.format(product.price)}</p>
      <p>Trong giỏ: {quantity}</p>
      <button onClick={() => onAdd(product.id)}>Thêm vào giỏ</button>
    </article>
  );
}

function ProductList({ products, cart, onAdd }) {
  if (products.length === 0) return <p>Không tìm thấy sản phẩm.</p>;

  return (
    <div>
      {products.map(product => (
        <ProductCard
          key={product.id}
          product={product}
          quantity={cart[product.id] ?? 0}
          onAdd={onAdd}
        />
      ))}
    </div>
  );
}

function CartSummary({ itemCount, total }) {
  return (
    <aside>
      <p>Tổng số lượng: {itemCount}</p>
      <p>Tổng tiền: {money.format(total)}</p>
    </aside>
  );
}

export default function App() {
  const [keyword, setKeyword] = useState('');
  const [cart, setCart] = useState({});

  function handleAdd(productId) {
    setCart(current => ({
      ...current,
      [productId]: (current[productId] ?? 0) + 1,
    }));
  }

  const visibleProducts = PRODUCTS.filter(product =>
    product.name.toLowerCase().includes(keyword.trim().toLowerCase())
  );

  const itemCount = Object.values(cart).reduce((sum, quantity) => sum + quantity, 0);
  const total = PRODUCTS.reduce(
    (sum, product) => sum + product.price * (cart[product.id] ?? 0),
    0
  );

  return (
    <main>
      <Card title="Cửa hàng">
        <SearchBox value={keyword} onValueChange={setKeyword} />
        <ProductList products={visibleProducts} cart={cart} onAdd={handleAdd} />
      </Card>
      <CartSummary itemCount={itemCount} total={total} />
    </main>
  );
}
```

App sở hữu giỏ hàng vì danh sách sản phẩm và tổng tiền đều cần nó. ProductCard chỉ nhận dữ liệu cần hiển thị và báo hành động thêm sản phẩm. Tổng tiền được tính từ PRODUCTS và cart, không giữ thêm state tổng tiền để tránh lệch dữ liệu. Tìm kiếm chỉ lọc phần hiển thị, không xóa sản phẩm trong giỏ. Với dữ liệu nhỏ, chưa cần memo hóa.

Kịch bản tự kiểm tra: ban đầu tổng tiền bằng 0; thêm bàn phím hai lần và chuột một lần thì tổng số lượng là 3, tổng tiền là 1.300.000 VND; tìm từ không tồn tại thì thấy thông báo rỗng và giỏ vẫn giữ nguyên.

Trong hệ thống bán hàng thật, server phải kiểm tra lại giá, tồn kho và quyền thao tác khi đặt hàng; tổng tiền phía client chỉ phục vụ hiển thị.

## 39. Thiết kế props và state dưới góc nhìn kiến trúc

### Đặt API theo vai trò của component

```tsx
type CustomerCardProps = {
  customer: CustomerSummary;
  onSelect: (id: string) => void;
};
```

Đoạn này minh họa hợp đồng, giả định CustomerSummary là kiểu dữ liệu của ứng dụng. Component hiển thị thường không cần biết endpoint, database, tenant resolver hoặc toàn bộ service nghiệp vụ. Container/page chịu trách nhiệm lấy dữ liệu, chuyển đổi và điều phối hành động.

### Payload nghiệp vụ giúp giảm phụ thuộc

```tsx
onSelect: (customerId: string) => void;
```

API này giúp bên sử dụng quan tâm tới khách hàng được chọn, không phải cấu trúc MouseEvent. Nhưng một Button wrapper thấp tầng nên giữ API onClick chuẩn để hỗ trợ đầy đủ hành vi DOM. Chọn mức trừu tượng theo vai trò.

### Tối giản đầu vào nhưng không máy móc

Nếu component chỉ hiển thị tên, `name` có thể đủ. Nếu hiển thị một hồ sơ thống nhất gồm nhiều thuộc tính, một object `customer` có kiểu rõ ràng có thể dễ đọc hơn. Không có luật rằng primitive props luôn tốt hơn object props.

### Tránh quá nhiều boolean

Một API như `isSmall`, `isLarge`, `isDanger`, `isPrimary` cho phép tổ hợp mâu thuẫn. Chọn `size: 'small' | 'large'` và `variant: 'primary' | 'danger'` khi các lựa chọn loại trừ nhau. Nếu từng trạng thái đòi hỏi payload khác nhau, dùng discriminated union.

### Không dùng props làm nơi phát sinh side effect khi render

Callback nên được gọi trong event handler hoặc cơ chế đồng bộ phù hợp. Đừng gọi onSave ngay trong thân render chỉ vì nhận được một prop mới. Render cần có thể chạy lại mà không gửi lệnh mua/bán hay API ghi dữ liệu ngoài ý muốn.

## 40. Các lỗi thường gặp

| Lỗi | Hệ quả | Cách xử lý |
|---|---|---|
| `active="false"` | Chuỗi vẫn truthy | `active={false}` |
| Sửa `customer.name` trong con | Mutation dữ liệu dùng chung | Callback và immutable update tại chủ sở hữu |
| `onClick={save()}` | Gọi lúc render | Truyền hàm hoặc arrow function |
| Đọc `props.key` | Không có key nghiệp vụ | Truyền thêm id |
| Dùng `null` và mong default chạy | Default không áp dụng | Dùng `??` nếu đúng nghiệp vụ |
| `useState(prop)` rồi mong tự đồng bộ | State giữ giá trị cũ | Chọn controlled/draft/reset rõ ràng |
| Text input value lúc undefined lúc string | Đổi uncontrolled ↔ controlled | Khởi tạo `''` hoặc chuẩn hóa value |
| `{count && <Badge />}` với count=0 | Hiện số 0 | `count > 0 && ...` |
| Truyền object/hàm mới vào memo | Khó bỏ qua render | Đo rồi ổn định tham chiếu khi cần |
| Bỏ dependency của callback | Dùng dữ liệu cũ | Khai báo đủ dependencies |
| Spread toàn bộ entity vào DOM | API khó kiểm soát | Tách prop nghiệp vụ và DOM props |
| Truyền callback thường từ server xuống client | Vi phạm ranh giới serialize | Đặt handler ở client hoặc dùng Server Function đúng cơ chế |
| Gọi setCount rồi đọc count và mong giá trị mới | Handler vẫn đọc snapshot cũ | Hiểu snapshot, tính nextValue hoặc dùng updater |
| Ba lần setCount(count + 1) | Chỉ đặt cùng một giá trị | Functional updater nếu cần cộng dồn |
| setForm chỉ truyền một field | Mất các field còn lại | Sao chép object hiện tại rồi cập nhật |
| Lưu total và selectedCustomer dư thừa | Có nhiều bản dữ liệu dễ lệch | Suy ra từ dữ liệu nguồn |
| Dùng cùng custom hook và mong state tự chung | Mỗi lần gọi có state riêng | Chia sẻ một nguồn state có chủ đích |
| Không cleanup timer/request | Tác vụ cũ tiếp tục chạy | Cleanup và bỏ qua kết quả không còn hợp lệ |
| Đổi key ngẫu nhiên | Mất state, remount liên tục | Dùng key ổn định theo thực thể |

## 41. Bài tập theo cấp độ

1. **Cơ bản:** Viết Avatar nhận name, imageUrl, size mặc định 80. Render ba người với ba kích thước; đặt alt có ý nghĩa.
2. **Cơ bản:** Viết StatusBadge nhận status bằng union active/inactive/pending; mỗi trạng thái có nhãn và class riêng.
3. **Trung cấp:** Viết QuantityInput controlled nhận value, min, max, onValueChange. Nút giảm không vượt min, nút tăng không vượt max.
4. **Trung cấp:** Viết CustomerList nhận customers, selectedId, onSelect. Hai component cùng phản ánh người đang được chọn từ state ở cha.
5. **Nâng cao:** Viết DataTable<T> nhận rows, getRowKey và columns có renderCell. Không dùng any để né kiểu.
6. **Nâng cao:** Trong Next.js, lấy dữ liệu tại Server Component, truyền DTO tối thiểu vào Client Component để tìm kiếm/chọn. Không truyền field nội bộ.
7. **Hiệu năng:** Tạo danh sách đủ lớn để quan sát, đo bằng Profiler rồi so sánh trước/sau memo. Không xem số log trong development là số lần cập nhật DOM.
8. **State cơ bản:** Viết bộ đếm có tăng, giảm, reset và nút cộng ba bằng functional updater. Giải thích vì sao gọi setter với snapshot khác updater.
9. **State object/array:** Viết form thêm/sửa/xóa công việc, không mutation, không dùng index làm key.
10. **Props + State:** Viết giao diện master/detail; cha giữ selectedId, hai con nhận props và callback. Không giữ bản sao selectedCustomer độc lập.
11. **Reducer:** Chuyển task app từ useState sang useReducer, mô tả action bằng discriminated union, giữ reducer thuần.
12. **Bất đồng bộ:** Giả lập A trả chậm hơn B; chọn A rồi B và chứng minh kết quả A không ghi đè lựa chọn B.

## 42. Lộ trình đọc và tài liệu chính thức

Bài được sắp theo tiến trình: phần 1–10 giải thích nền tảng props/state và cơ chế cập nhật; phần 11–21 đi vào object, array, form, chia sẻ dữ liệu và composition; phần 22–32 trình bày TypeScript, thiết kế API, reducer và Context; phần 33–39 tập trung bất đồng bộ, hiệu năng, Next.js và ví dụ tổng hợp; phần 40–41 là bảng lỗi và bài tập.

- [Passing Props to a Component](https://react.dev/learn/passing-props-to-a-component)
- [Responding to Events](https://react.dev/learn/responding-to-events)
- [Updating Objects in State](https://react.dev/learn/updating-objects-in-state)
- [Sharing State Between Components](https://react.dev/learn/sharing-state-between-components)
- [Input: controlled và uncontrolled](https://react.dev/reference/react-dom/components/input)
- [You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect)
- [Using TypeScript](https://react.dev/learn/typescript)
- [memo](https://react.dev/reference/react/memo)
- [useCallback](https://react.dev/reference/react/useCallback)
- [Passing Data Deeply with Context](https://react.dev/learn/passing-data-deeply-with-context)
- [React 19: ref as a prop](https://react.dev/blog/2024/12/05/react-19)
- [use client và các kiểu có thể serialize](https://react.dev/reference/rsc/use-client)
- [Next.js: Server and Client Components](https://nextjs.org/docs/app/getting-started/server-and-client-components)
- [State: A Component's Memory](https://react.dev/learn/state-a-components-memory)
- [State as a Snapshot](https://react.dev/learn/state-as-a-snapshot)
- [Queueing a Series of State Updates](https://react.dev/learn/queueing-a-series-of-state-updates)
- [Updating Arrays in State](https://react.dev/learn/updating-arrays-in-state)
- [Choosing the State Structure](https://react.dev/learn/choosing-the-state-structure)
- [Preserving and Resetting State](https://react.dev/learn/preserving-and-resetting-state)
- [Extracting State Logic into a Reducer](https://react.dev/learn/extracting-state-logic-into-a-reducer)
- [useEffect](https://react.dev/reference/react/useEffect)
- [useDeferredValue](https://react.dev/reference/react/useDeferredValue)

Tài liệu được đối chiếu ngày 01/11/2026. Các ví dụ là mã minh họa do tác giả xây dựng; chưa được chạy trong một dự án React/Next.js cụ thể.
