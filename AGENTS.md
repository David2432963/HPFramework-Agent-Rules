# Quy Tắc Cho AI Agent — Unity Project

> Đây là file rule gốc cho AI agent khi triển khai hoặc chỉnh sửa dự án Unity.
> File này chỉ chứa quy tắc cấp cao, hard guardrails và cách tìm source of truth. Kiến thức chi tiết nằm trong `Knowledge/` hoặc documentation của framework/project.

## 1. Thứ Tự Ưu Tiên

Khi có xung đột hoặc chưa rõ cách triển khai, ưu tiên theo thứ tự:

1. Yêu cầu trực tiếp của người dùng trong task hiện tại.
2. Code, asset và behavior hiện tại của project.
3. Documentation/Knowledge hiện tại của project và framework.
4. File `AGENTS.md` này.
5. Convention đang được dùng trong module đang sửa.
6. Best practice chung của Unity/C#.

Không đoán API, lifecycle, ownership hoặc behavior nếu có thể kiểm tra source, test hoặc tài liệu hiện tại.

## 2. Knowledge Routing

Trước khi triển khai task không tầm thường, đọc đúng file Knowledge liên quan:

| Phạm vi | File cần đọc |
|---|---|
| Clean code, naming, class responsibility, abstraction | `Knowledge/CodeQuality.md` |
| Dependency/reference runtime, scene/hierarchy lookup | `Knowledge/RuntimeReferenceRules.md` |
| Prefab, scene, ScriptableObject asset, `.meta`, GUID, working tree | `Knowledge/UnityAssetSafety.md` |
| Hot path, allocation, physics, pooling, logging, profiling | `Knowledge/RuntimeSafetyAndPerformance.md` |
| Config, runtime state, save data, schema compatibility | `Knowledge/DataAndPersistence.md` |
| Async, cancellation, initialization, lifetime ownership | `Knowledge/AsyncAndLifecycle.md` |
| HP Framework, VContainer, UI, pooling, EventBus, setup | `Knowledge/HPFrameworkReference.md` |

Chỉ đọc file liên quan tới task. Không cần load toàn bộ `Knowledge/` nếu không cần thiết.

Nếu Knowledge chưa đủ để quyết định, đọc source/test/documentation của module đang dùng trước khi viết code.

## 3. Hard Guardrails

- Không tự mở rộng phạm vi task.
- Không thêm package, framework, SDK hoặc abstraction mới nếu chưa có nhu cầu thật.
- Không dùng runtime scene/hierarchy search làm dependency resolution.
- Không viết fallback tìm kiếm để che việc thiếu dependency bắt buộc.
- Không overwrite/revert thay đổi của người dùng ngoài phạm vi task.
- Không sửa prefab/scene/asset từ cached state cũ; phải đọc trạng thái mới nhất ngay trước khi ghi.
- Không làm mất `.meta` hoặc thay GUID của asset hiện có ngoài ý muốn.
- Không sửa generated output trực tiếp nếu có source-of-truth sinh ra file đó.
- Không chạy lệnh Git phá hủy working tree như `git reset --hard`, `git checkout -- .`, `git clean -fd` nếu người dùng không yêu cầu rõ ràng.
- Không commit hoặc push nếu người dùng chưa yêu cầu.

## 4. Quy Trình Làm Việc

### Trước khi sửa

- Đọc code/asset hiện tại của vùng bị ảnh hưởng.
- Xác định source of truth và owner của dependency/state liên quan.
- Đọc Knowledge hoặc framework docs tương ứng.
- Kiểm tra working tree/dirty state nếu task có thể chạm dữ liệu người dùng đang chỉnh.

### Khi thiết kế

- Chọn giải pháp nhỏ nhất giải quyết đủ yêu cầu.
- Ưu tiên cấu trúc hiện tại trước khi thêm abstraction.
- Nếu nhiều phương án hợp lệ, ưu tiên phương án ít coupling, dễ debug và có ownership/lifecycle rõ ràng.
- Với bug, xác định **root cause** trước khi sửa; không chỉ che symptom bằng null-check/retry/fallback.

### Khi triển khai

- Chỉ thay đổi file và behavior cần thiết cho task.
- Không cleanup/refactor unrelated code chỉ vì nhìn thấy nó.
- Giữ coding style của module hiện tại trừ khi task yêu cầu chuẩn hóa.
- Khi behavior thay đổi, cập nhật test nếu project có test phù hợp.

### Sau khi sửa

Chạy validation phù hợp với phạm vi thay đổi, ví dụ:

- C# compile;
- test liên quan;
- Unity import/compile cho shader/editor/asset changes;
- validator riêng của framework/package nếu có;
- diff/status để phát hiện thay đổi ngoài ý muốn.

Nếu không thể verify một phần, phải nói rõ phần nào chưa được kiểm chứng.

## 5. Ngôn Ngữ Và Báo Cáo

- Code, identifier và comment trong source: tiếng Anh.
- Giải thích cho người dùng: tiếng Việt, ngắn gọn, đi thẳng vấn đề.

Khi có thay đổi, báo cáo:

1. Đã thay đổi gì.
2. Vì sao thay đổi.
3. File/system bị ảnh hưởng.
4. Validation/test đã chạy.
5. Rủi ro hoặc edge case còn lại nếu có.
6. Phần cố ý không đụng tới.

Không tuyên bố hoàn tất tuyệt đối nếu vẫn còn bước validation chưa chạy hoặc chưa chắc chắn.

## 6. Checklist Cuối Task

Trước khi kết thúc, tự kiểm tra:

- Có scope creep không?
- Có tạo dependency/package/abstraction không cần thiết không?
- Có phá ownership/lifecycle hiện tại không?
- Có runtime search/fallback cho dependency bắt buộc không?
- Có đụng thay đổi ngoài task hoặc user work đang dirty không?
- Có làm mất `.meta`, GUID hoặc serialized reference không?
- Có sửa generated output sai source of truth không?
- Có tạo work/alloc/log mới trong hot path mà chưa cân nhắc không?
- Nếu có async/save/framework behavior, đã đọc Knowledge tương ứng chưa?
- Đã chạy validation phù hợp chưa?
- Có commit/push khi người dùng chưa yêu cầu không?
