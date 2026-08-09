# Kiến Thức — HP Framework Reference Router

File này **không mô tả lại kiến trúc hoặc API của HP Framework**. Nó chỉ chỉ agent tới đúng source of truth khi task dùng framework.

HP Framework mặc định nằm tại:

```text
Assets/Plugins/HPFramework/
```

## 1. Task → Documentation

| Khi task liên quan | Đọc trước |
|---|---|
| Tổng quan framework, requirements, quick start, Bootstrap mặc định | `Assets/Plugins/HPFramework/README.md` |
| Cài đặt, cách consume source, package/dependency | `Assets/Plugins/HPFramework/Documentation~/installation.md` |
| Setup project mới, startup cơ bản, UI Camera | `Assets/Plugins/HPFramework/Documentation~/getting-started.md` |
| Ownership, composition root, module boundary, event architecture | `Assets/Plugins/HPFramework/Documentation~/architecture.md` |
| VContainer lifetime, scene scope, feature scope, registration | `Assets/Plugins/HPFramework/Documentation~/scopes.md` |
| UI, BaseScreen/BasePopup, scoped UI, pool, EventSystem | `Assets/Plugins/HPFramework/Documentation~/ui-and-pooling.md` |
| Runtime optimization, pooling cache, non-alloc API, graphics clocks | `Assets/Plugins/HPFramework/Documentation~/runtime-performance.md` |
| Setup/dependency/camera/package/troubleshooting | `Assets/Plugins/HPFramework/Documentation~/troubleshooting.md` |
| Migration từ framework version/layout cũ | `Assets/Plugins/HPFramework/Documentation~/migration-3.0.md` |

## 2. Quy Tắc Đọc

- Chỉ đọc docs liên quan task, không load toàn bộ framework docs mặc định.
- Nếu task dùng nhiều subsystem, đọc các file tương ứng.
- Nếu docs không mô tả đủ behavior, đọc implementation + test của module đó.
- Nếu docs và source/test có dấu hiệu không khớp, source/test hiện tại là bằng chứng runtime mạnh hơn; báo rõ inconsistency thay vì đoán.
- Không copy API/pattern từ project cũ chỉ vì class name giống nhau.
- Không tạo abstraction thay thế framework trước khi kiểm tra framework đã có API phù hợp hay chưa.

## 3. Khi Làm Với VContainer

Nếu task liên quan:

```text
Lifetime
Root/Scene/Feature scope
registration
injection
entry points
scoped service
```

đọc ít nhất:

```text
Documentation~/architecture.md
Documentation~/scopes.md
```

Sau đó đọc source của registration/scope liên quan nếu implementation detail ảnh hưởng task.

## 4. Khi Làm UI/Pool/Event

UI hoặc pooling:

```text
Documentation~/ui-and-pooling.md
```

Nếu cần ownership/lifetime:

```text
Documentation~/scopes.md
```

Event architecture hoặc cross-scope behavior:

```text
Documentation~/architecture.md
Documentation~/scopes.md
```

## 5. Khi Tối Ưu Framework Usage

Đọc:

```text
Documentation~/runtime-performance.md
```

Không tự viết duplicate optimization/helper nếu framework đã có non-alloc/cached/scoped API phù hợp.

## 6. Khi Sửa Chính HP Framework

Trước khi sửa:

- đọc README + docs của subsystem;
- đọc source + tests hiện tại;
- giữ package/module boundary hiện tại trừ khi task yêu cầu thay đổi kiến trúc;
- không để game-specific code lọt vào framework.

Sau khi sửa framework, chạy validation phù hợp, bao gồm package validator nếu project có:

```text
Assets/Plugins/HPFramework/.github/scripts/Validate-Package.ps1
```

và compile/tests liên quan nếu khả dụng.

## 7. Khi Documentation Thiếu

Nếu implementation có contract quan trọng nhưng docs chưa có:

1. Không đoán.
2. Đọc source/test để xác định behavior.
3. Triển khai theo behavior đã xác minh.
4. Nếu task bao gồm framework maintenance/documentation, cập nhật docs tương ứng; nếu không, báo lại gap để người dùng quyết định.
