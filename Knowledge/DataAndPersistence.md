# Kiến Thức — Data And Persistence

File này quy định cách phân biệt config, runtime state và persistent data trong project game.

## 1. Phân Loại Data Theo Mục Đích Và Lifetime

```text
Config / Definition      → dữ liệu authoring, thường immutable runtime
Application Profile      → dữ liệu persistent xuyên session
Scene / Session State    → dữ liệu sống cùng scene/run
Feature State            → dữ liệu sống cùng feature
UI State                 → state presentation cục bộ khi cần
```

Mỗi loại data phải có owner rõ ràng.

## 2. ScriptableObject

ScriptableObject phù hợp cho:

- config;
- catalog;
- reusable definition;
- authoring data.

Không dùng shared ScriptableObject asset làm mutable gameplay/session state nếu asset đó là config.

Không ghi runtime progression trực tiếp vào config asset.

Class `ScriptableObject` trong project dùng hậu tố `SO`, trừ khi module/framework hiện tại có convention khác được xác định rõ.

## 3. Runtime State

Runtime mutable state nên nằm trong object/service/state model có lifetime phù hợp.

Ví dụ:

```text
NpcConfigSO       → config
NpcRuntimeState   → runtime state
```

Không dùng static mutable facade để giữ gameplay/profile/session data chỉ vì tiện truy cập.

## 4. Persistence Boundary

Không rải trực tiếp persistence implementation khắp gameplay code:

```text
PlayerPrefs.Set...
File.WriteAllText...
JSON serialize trực tiếp từ nhiều feature
```

Ưu tiên một boundary rõ ràng như store/repository/save service phù hợp với kiến trúc project.

Gameplay code nên làm việc với contract/data model thay vì biết chi tiết file path/storage format nếu không cần.

## 5. Save Schema Compatibility — HARD RULE

Trước khi thay đổi model được lưu persistent, đánh giá save cũ.

Phải kiểm tra:

- field mới có default hợp lệ không;
- field rename có làm mất dữ liệu không;
- đổi type có deserialize được save cũ không;
- xóa field có cần migration không;
- enum value thay đổi có phá dữ liệu không;
- nested structure thay đổi có backward compatible không;
- có cần schema version/migration step không.

Không silently tạo breaking save format trong feature task thông thường.

Nếu thay đổi có thể làm save cũ không đọc được, phải báo rõ và đề xuất migration/versioning.

## 6. Save Versioning Và Migration

Khi project cần versioning:

- version phải thuộc persistent schema, không phụ thuộc scene;
- migration nên deterministic;
- migration không sửa data ngoài phạm vi version cần nâng;
- failure phải được xử lý/log rõ ràng;
- test với representative old-save data khi có thể.

## 7. Transaction Và File Safety

Với file save quan trọng:

- tránh concurrent write không kiểm soát;
- cân nhắc temp/backup/atomic replace nếu implementation yêu cầu;
- không để partial write phá save chính;
- path phải được validate, không cho traversal ngoài vùng lưu hợp lệ.

Nếu dùng persistence của HP Framework, đọc `Knowledge/HPFrameworkReference.md` và source/docs hiện tại trước khi tạo storage layer mới.

## 8. Sensitive Data

Không log token, credential, personal data hoặc nội dung save nhạy cảm.

Không hard-code secret/API key vào source hoặc ScriptableObject asset được commit.
