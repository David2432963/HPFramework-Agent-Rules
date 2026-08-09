# Kiến Thức — Runtime Safety And Performance

Mục tiêu là giữ runtime ổn định, predictable và hiệu quả mà không tối ưu mù quáng.

## 1. Phân Biệt Hot Path Và Cold Path

### Hot path

Ví dụ:

```text
Update / LateUpdate / FixedUpdate
per-NPC / per-projectile / per-frame loop
frequent UI refresh
physics query lặp nhiều
render/material/property update lặp nhiều
```

Hot path phải cân nhắc CPU, allocation, cache locality, Unity API cost và số lượng object.

### Cold path

Ví dụ:

```text
Awake / Initialize
scene loading
one-time setup
editor tooling
migration
rare admin/debug action
```

Cold path ưu tiên readability và correctness trước. Không micro-optimize nếu không có bằng chứng cần thiết.

## 2. Allocation Và CPU Trong Hot Path

Tránh allocation/work không cần thiết:

- LINQ trong loop nóng;
- string concat/format mỗi frame khi value không đổi;
- closure/anonymous allocation trong loop nóng;
- tạo list/array/dictionary tạm liên tục;
- repeated component/hierarchy scan;
- repeated material/MaterialPropertyBlock write khi state không đổi;
- repeated parsing/reflection nếu có thể cache;
- Instantiate/Destroy liên tục khi pooling phù hợp.

Cache reusable reference/buffer khi owner rõ ràng.

Không cache toàn cục chỉ để tránh allocation nếu nó làm ownership/race/lifetime khó hiểu hơn.

## 3. Physics

- Rigidbody simulation/movement chạy trong fixed-step phù hợp.
- Object do Rigidbody sở hữu phải được điều khiển qua Rigidbody API thay vì set Transform trực tiếp trong gameplay simulation.
- Input/frame sampling có thể ở Update, nhưng physics application phải vào fixed-step khi cần.
- Query lặp nhiều nên cân nhắc NonAlloc API hoặc buffer reuse.
- LayerMask và explicit filtering tốt hơn scene search hoặc post-filter nặng.

## 4. Pooling

Dùng pooling khi:

- object spawn/despawn thường xuyên;
- Instantiate/Destroy tạo cost đáng kể;
- lifecycle của pooled object rõ ràng.

Không pool mọi object một cách máy móc. Object hiếm khi tạo hoặc có lifecycle phức tạp có thể phù hợp với Instantiate/Destroy hơn.

Khi dùng pool của HP Framework, đọc `Knowledge/HPFrameworkReference.md` và docs pooling trước khi triển khai.

## 5. UI Performance

- Không refresh UI mỗi frame nếu data không đổi.
- Cache displayed value nếu formatting/text update đắt hoặc lặp nhiều.
- Không tách thêm Canvas chỉ để "đẹp hierarchy"; split Canvas dựa trên rebuild/profile evidence.
- Tránh layout rebuild liên tục do thay đổi hierarchy/text không cần thiết.

Nếu dùng HP Framework UI, tham chiếu docs qua `Knowledge/HPFrameworkReference.md`.

## 6. Logging

Không:

- `Debug.Log` mỗi frame;
- log trong loop nóng cho từng object;
- để debug spam tạm trong production path;
- format string nặng chỉ để log bị disable/không cần thiết.

Log phải có giá trị chẩn đoán và mức độ phù hợp.

Trước khi hoàn tất task, xóa hoặc guard debug log tạm đã dùng để điều tra.

## 7. Time Và Animation

- Chọn scaled/unscaled time theo semantics của feature.
- Không update shader/property mỗi frame nếu GPU clock hoặc state-driven update giải quyết được.
- Pause/resume/timeScale phải giữ behavior deterministic.

## 8. Optimization Process

Áp dụng ngay các hygiene rõ ràng:

- tránh allocation per-frame hiển nhiên;
- tránh component search lặp;
- tránh duplicate work dễ thấy;
- dùng pooling/non-alloc khi workload thực sự lặp nhiều.

Với optimization không hiển nhiên:

1. Xác định bottleneck.
2. Dùng Unity Profiler/Memory Profiler hoặc measurement phù hợp.
3. Tối ưu đúng hot path.
4. Verify behavior và regression.

Không đổi architecture lớn chỉ vì micro-benchmark giả định.

## 9. Framework-Specific Performance

Nếu task dùng API performance của HP Framework như pooling, frustum buffer, graphics controller hoặc resource provider, đọc:

```text
Knowledge/HPFrameworkReference.md
```

rồi mở đúng framework documentation/source tương ứng.
