# Kiến Thức — Unity Asset Safety

File này bảo vệ dữ liệu Unity và thay đổi đang làm dở của người dùng khi AI agent sửa project.

## 1. Serialized Unity Assets

Các file sau có thể đang được Unity Editor hoặc người dùng chỉnh trực tiếp:

```text
.prefab
.unity
.asset
.controller
.anim
.mat
và các serialized Unity asset tương tự
```

Trước khi ghi:

1. Đọc/fetch trạng thái mới nhất ngay trước khi sửa.
2. Kiểm tra scene/prefab stage/asset dirty state khi có khả năng ảnh hưởng.
3. Chỉ sửa đúng phần cần thiết.
4. Không normalize/rebuild YAML ngoài phạm vi task.
5. Không save, close, reload hoặc overwrite user work đang mở nếu chưa được yêu cầu hoặc chưa xác minh an toàn.

Không dùng cached state cũ để overwrite serialized asset hiện tại.

## 2. `.meta` Và GUID — HARD RULE

Unity reference phụ thuộc vào GUID trong `.meta`.

Khi move/rename asset hiện có:

```text
asset + .meta phải đi cùng nhau
→ giữ nguyên GUID
→ giữ serialized references
```

Không:

- xóa `.meta` của asset hiện có ngoài ý muốn;
- tạo lại GUID khi chỉ đang move/rename;
- copy/replace asset theo cách làm mất `.meta`;
- regenerate meta hàng loạt để "làm sạch" project.

Chỉ tạo GUID mới khi asset thực sự là asset mới hoặc user yêu cầu identity mới.

## 3. Working Tree Safety

Không revert, format, cleanup hoặc overwrite thay đổi ngoài phạm vi task chỉ vì file đang dirty.

Trước khi sửa file đã có thay đổi:

- đọc version hiện tại trên disk;
- phân biệt thay đổi của user với thay đổi của task;
- patch tối thiểu để không làm mất phần không liên quan.

Không chạy các lệnh phá hủy dữ liệu như:

```text
git reset --hard
git checkout -- .
git clean -fd
```

trừ khi người dùng yêu cầu rõ ràng và hiểu phạm vi mất dữ liệu.

Không commit/push nếu user chưa yêu cầu.

## 4. Generated Files Và Source Of Truth

Trước khi sửa file generated, xác định nguồn sinh ra nó.

Ví dụ:

```text
generated prefab
code-generated C#
input wrapper
build output
project file
serialized cache
```

Nếu file sẽ bị generator ghi đè ở lần chạy sau:

- sửa generator/template/source-of-truth thay vì output;
- chỉ sửa output trực tiếp khi đó thực sự là workflow chuẩn hoặc user yêu cầu.

Không commit build/cache/generated artifacts chỉ vì tool tạo ra chúng, trừ khi project cố ý track.

## 5. Editor State

Nếu Unity đang mở scene/prefab stage dirty:

- không tự save/close để chạy test;
- không reload domain/project chỉ để tiện cho agent nếu có cách verify an toàn hơn;
- ưu tiên test trên copy/temp project khi cần và hợp lý.

## 6. Move/Rename/Delete

Trước khi delete asset hoặc folder:

- kiểm tra reference/usage nếu có khả năng được dùng;
- không xóa vì "có vẻ không dùng" nếu chưa xác minh;
- với asset có `.meta`, delete/move theo cặp đúng semantics Unity.

## 7. Diff Discipline

Sau thay đổi asset quan trọng:

- kiểm tra diff/status;
- đảm bảo không có serialized churn ngoài ý muốn;
- nếu Unity rewrite file lớn, xác minh semantic change đúng phạm vi task.
