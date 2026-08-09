# Kiến Thức — Async And Lifecycle

File này áp dụng cho async/UniTask/task, scene loading, asset loading, backend, remote config, ads/IAP và các workflow kéo dài qua nhiều frame.

## 1. Mỗi Async Operation Phải Có Owner

Trước khi bắt đầu async work, xác định:

```text
Ai sở hữu operation?
Operation được phép sống bao lâu?
Nếu owner bị destroy/dispose thì điều gì xảy ra?
Result còn hợp lệ không khi operation hoàn tất?
```

Không bắt đầu async work mà không có câu trả lời rõ cho ownership/lifetime khi operation có thể kéo dài.

## 2. Cancellation

Async work gắn với GameObject/scene/feature/lifetime phải có strategy khi owner kết thúc:

- cancel operation nếu API hỗ trợ và semantics phù hợp;
- hoặc bỏ qua result một cách an toàn nếu không thể cancel;
- không tiếp tục chạm Unity object đã destroy;
- không apply result vào scene/feature đã unload.

Cancellation không phải error nếu nó là kết quả bình thường của lifecycle.

## 3. Fire-And-Forget

Không fire-and-forget vô trách nhiệm.

Nếu operation không được await:

- phải có lý do rõ ràng;
- exception phải được observe/log/route phù hợp;
- lifetime/cancellation vẫn phải được quản lý;
- caller không được giả định operation đã hoàn tất.

## 4. Exception Handling

Không nuốt exception bằng `catch { }`.

Khi catch:

- xử lý được lỗi thật sự; hoặc
- thêm context và rethrow/route; hoặc
- chuyển thành result/state lỗi có semantics rõ ràng.

Không dùng retry vô hạn hoặc retry để che setup bug.

## 5. Initialization Order

Không dựa vào hidden `Start()` ordering để đảm bảo async dependency sẵn sàng.

Nếu subsystem A cần B initialized trước:

- thể hiện dependency/lifecycle rõ ràng;
- có readiness contract/barrier nếu cần;
- caller không được đoán timing bằng delay tùy ý.

Không dùng `await Delay(...)` chỉ để "chờ hệ thống kia chắc đã init" nếu có thể có signal/lifecycle rõ ràng.

## 6. Scene Và Feature Transition

Khi async operation chạy qua scene transition:

- xác định result còn thuộc scene cũ hay application-level;
- không giữ reference scene object vô thời hạn;
- unsubscribe/dispose callback đúng lifetime;
- tránh double-complete/double-navigation khi user thao tác nhanh.

## 7. Unity Main Thread

Unity API chỉ được gọi ở context/thread hợp lệ.

Không đưa Unity Object access sang worker thread trừ khi API cụ thể cho phép.

Background work phù hợp cho pure data/CPU operation, sau đó marshal result về context phù hợp trước khi chạm Unity objects.

## 8. Timeouts Và External Services

Backend/remote/ads/IAP/network operations nên có timeout/error state phù hợp với UX thay vì chờ vô hạn.

Không biến external service failure thành crash nếu feature có thể degrade hợp lệ; nhưng dependency bắt buộc cho flow phải fail/report rõ ràng.

## 9. Framework Lifecycle

Nếu project dùng HP Framework/VContainer lifecycle hoặc UniTask integration, không tự suy luận registration/startup semantics.

Đọc `Knowledge/HPFrameworkReference.md` để mở đúng docs/source trước khi triển khai.
