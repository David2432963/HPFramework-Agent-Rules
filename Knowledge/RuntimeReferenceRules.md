# Kiến Thức — Runtime Reference Rules

Đây là hard rule cho dependency/reference resolution trong gameplay runtime.

## 1. Nguyên Tắc

Runtime startup và dependency resolution phải explicit, deterministic và có owner rõ ràng.

Dependency bắt buộc phải đến từ một trong các nguồn:

1. Dependency Injection.
2. Serialized reference được gán rõ ràng.
3. Owner truyền dependency cho child qua explicit initialization.
4. Reference trả về trực tiếp từ factory/spawn/create operation.

## 2. Không Dùng Hierarchy/Scene Search Để Resolve Dependency

Không dùng các API sau để tìm gameplay dependency khi runtime:

```text
FindObjectOfType
FindObjectsOfType
FindObjectsByType
GameObject.Find
GameObject.FindWithTag
transform.Find
transform.root.Find
GetComponentInChildren
GetComponentsInChildren
GetComponentInParent
GetComponentsInParent
các dạng scene/hierarchy traversal tương tự
```

Không viết fallback kiểu:

```text
reference null
→ search scene/hierarchy
→ tiếp tục chạy
```

cho dependency bắt buộc.

## 3. Allowed Exceptions

Cho phép khi mục đích không phải runtime dependency discovery:

- Editor tooling để authoring, validation, migration hoặc debugging.
- `GetComponent` / `TryGetComponent` trên **cùng GameObject** để cache component nội bộ.
- Demo/migration code được cô lập rõ ràng khỏi gameplay runtime.
- Search trong test/editor validation để kiểm tra invariant.

## 4. Mandatory Vs Optional

Dependency bắt buộc:

- phải được setup rõ ràng;
- khi thiếu phải fail fast hoặc báo lỗi rõ ràng;
- không silent return để che lỗi setup;
- không tự search fallback.

Dependency optional:

- được null-check/fallback khi core flow vẫn hợp lệ nếu thiếu nó;
- optionality phải rõ trong API/field/documentation.

## 5. Owner → Child

Parent/owner giữ reference tới child và truyền dependency xuống theo luồng rõ ràng.

Child không tự tìm parent/owner để bootstrap chính nó.

Khi A sở hữu B và B sở hữu C, A không nên bypass B để thao tác trực tiếp C nếu ownership boundary của B cần được giữ.

## 6. Service Location

Không dùng global singleton hoặc container resolution ngẫu nhiên trong gameplay code chỉ để lấy dependency cho tiện.

Nếu project dùng DI/framework, tham chiếu tài liệu của DI/framework trước khi chọn cách resolve.

Với HP Framework/VContainer, xem `Knowledge/HPFrameworkReference.md`.
