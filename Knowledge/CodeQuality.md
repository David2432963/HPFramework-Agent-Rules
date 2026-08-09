# Kiến Thức — Code Quality

File này định nghĩa các nguyên tắc chất lượng code dùng chung cho project Unity. Không dùng file này để mô tả API cụ thể của framework.

## 1. Nguyên Tắc Cốt Lõi

- Ưu tiên code đơn giản, rõ ràng và dễ debug hơn code quá thông minh.
- Mỗi class tập trung vào một trách nhiệm chính.
- Ưu tiên composition hơn kế thừa sâu.
- Áp dụng SOLID khi nó làm dependency và ownership rõ hơn; không áp dụng máy móc.
- Chỉ dùng design pattern khi nó giải quyết vấn đề thật.
- Bám theo kiến trúc hiện tại trước khi thêm abstraction mới.
- Làm thay đổi nhỏ nhất đủ giải quyết task.
- Không tạo API, hook, interface hoặc service chỉ để "có thể dùng sau".

## 2. Naming Và Ngôn Ngữ

- Code, comment, class, method, property, field, biến: tiếng Anh.
- `PascalCase`: class, struct, enum, property, public method, event.
- `camelCase`: parameter và local variable.
- `_camelCase`: private field cho code mới, trừ khi module hiện tại có convention khác rõ ràng.
- Interface bắt đầu bằng `I`.
- Class kế thừa `ScriptableObject` dùng hậu tố `SO`.
- Tên file khớp với class chính trong file.
- Đặt tên theo ý nghĩa domain, không theo chi tiết triển khai.
- Tránh tên quá chung như `Manager`, `Helper`, `Utils` khi có tên domain cụ thể rõ hơn.
- Tránh tên gây nhầm với type/API Unity nếu có thể.

## 3. Trách Nhiệm Của Class

Đánh giá tách class khi xuất hiện một hoặc nhiều dấu hiệu:

- class sở hữu nhiều domain không liên quan;
- có nhiều lifecycle độc lập;
- có nhiều nhóm dependency phục vụ các trách nhiệm khác nhau;
- thay đổi một feature thường xuyên làm ảnh hưởng logic không liên quan;
- class vừa làm domain logic, presentation, persistence và orchestration.

Không tách class chỉ vì số dòng. Số dòng chỉ là tín hiệu để review trách nhiệm.

## 4. MonoBehaviour Và C# Thuần

Dùng `MonoBehaviour` khi cần trực tiếp:

- serialized reference;
- Transform/GameObject;
- Animator/Renderer/Physics;
- Unity lifecycle callback;
- coroutine;
- presentation/view.

Ưu tiên class C# thuần cho:

- state;
- domain logic;
- calculation;
- service/use case;
- presenter/coordinator không cần Unity API trực tiếp.

Không biến mọi system thành `MonoBehaviour` chỉ để có `Update` hoặc để truy cập scene.

Không dùng `new` để tạo `MonoBehaviour` hoặc `ScriptableObject`; dùng API Unity phù hợp.

## 5. Method Và Control Flow

- Ưu tiên early return thay vì nesting sâu.
- Method nên có một mục đích rõ ràng.
- Không tạo wrapper method không thêm ý nghĩa chỉ để bọc vài dòng.
- Xóa dead code, field không dùng và nhánh trùng lặp trong phạm vi task.
- Comment giải thích intent, invariant, trade-off hoặc behavior khó thấy; không comment điều hiển nhiên.

## 6. Abstraction Và Dependency

- Chỉ tạo interface khi có boundary thật: nhiều implementation, test seam, module boundary hoặc giảm coupling đáng kể.
- Chỉ tách helper khi logic lặp lại là thật và đủ ổn định.
- Không thêm service layer chỉ để "đúng pattern".
- Dependency bắt buộc phải rõ ràng trong constructor, injection, serialized reference hoặc explicit initialization.
- Không dùng static mutable state chỉ để truy cập tiện hơn.

Chi tiết về runtime reference xem `Knowledge/RuntimeReferenceRules.md`.

## 7. Feature Ownership

Nếu một feature có config, state, UI, setup, event hoặc test riêng, ưu tiên để feature đó sở hữu chúng thay vì dồn vào một global manager.

Shared code chỉ nên được đưa ra Common/Core khi thực sự được nhiều domain sử dụng và semantics đã ổn định.

## 8. Quy Tắc Thay Đổi

- Giữ style hiện tại của file/module khi sửa code cũ.
- Không refactor unrelated code trong cùng task nếu không cần cho correctness.
- Khi behavior thay đổi, cập nhật test nếu có test layer phù hợp.
- Với bug fix, xác định root cause trước; không chữa symptom bằng fallback chung chung.

## 9. Clarity Vs Performance

- Hot path/per-frame/mass-object code: cân nhắc performance ngay từ thiết kế.
- Initialization/editor/rare path: ưu tiên readability trước, trừ khi có bằng chứng profiling cho thấy vấn đề.
- Không biến LINQ, allocation hoặc abstraction thành lệnh cấm tuyệt đối ngoài hot path.

Chi tiết performance xem `Knowledge/RuntimeSafetyAndPerformance.md`.
