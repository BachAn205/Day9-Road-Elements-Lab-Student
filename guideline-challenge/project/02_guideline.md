# Annotation guideline — TODO tên bài toán

**Version:** v1

<!--
v0 = chưa có bản nháp. Đổi dòng Version ở trên thành v1 khi xong bản nháp đầu, v2 sau calibration, v3 sau blind
handoff; mỗi lần tăng version ghi một dòng vào 08_revision_log.md. `make freeze` đòi v2 trở lên.

File này là thứ nhóm peer nhận nguyên văn trong blind pack và là Guide dán vào CVAT. Peer KHÔNG nhận
edge_case_cards.md, gold_decisions.csv hay sample_pack.csv. Rule nào peer cần biết phải nằm ở đây.
No hidden rules: rule chỉ giải thích bằng miệng thì coi như không tồn tại.
Ví dụ trong guideline chỉ dùng ảnh split example hoặc calibration, không dùng ảnh blind.
-->

## 1. Objective + scope

TODO — label để làm gì; object/region nào trong scope, cái nào ngoài scope.

## 2. Annotation unit

TODO — image, frame hay track? Instance hay region? Khi nào một object được tính là instance mới?

## 3. Geometry rule

TODO — rectangle / polyline / polygon; tight, visible hay amodal; đặt điểm thế nào; endpoint ở đâu; tolerance.

## 4. Taxonomy

Có **1 class duy nhất**: `traffic_light`. Mỗi bộ đèn vật lý = 1 bounding box. Bảng đầy đủ ở `03_ontology_and_cvat_setup.md` — hai nơi phải khớp nhau.

### Attribute: `color` — trạng thái màu đèn đang hiển thị

| Giá trị | Dùng khi |
|---|---|
| `red` | Đèn đỏ đang sáng rõ |
| `yellow` | Đèn vàng đang sáng rõ |
| `green` | Đèn xanh đang sáng rõ |
| `off` | Tất cả đèn tắt (mất điện, ngoài giờ hoạt động) |
| `unknown` | Bị che, loá, quá nhỏ để đọc màu, hoặc không chắc chắn |

**Default**: `unknown`. Annotator phải chủ động chọn màu — để nguyên default là lỗi.

### Attribute: `relevance` — đèn điều khiển làn nào

| Giá trị | Dùng khi |
|---|---|
| `ego_lane` | Đèn trực tiếp điều khiển làn xe đang đi (lane chính giữa hướng camera) |
| `other_lane` | Đèn của làn vuông góc, làn ngược chiều, hoặc làn bên cạnh rõ ràng |
| `ambiguous` | Không xác định được đèn này điều khiển làn nào |

**Default**: `ego_lane`. Đổi sang `other_lane` khi đèn rõ ràng hướng về luồng giao thông khác.

## 5. Inclusion / exclusion

TODO — trường hợp bắt buộc label; trường hợp ignore.

## 6. Visibility / occlusion

TODO — bị che một phần, bị cắt mép ảnh, nhỏ/xa, phản chiếu, loá, độ tin cậy thấp.

## 7. Ambiguity / escalation

TODO — khi nào LABEL / IGNORE / UNKNOWN / ESCALATE khi bằng chứng không đủ. Ghi rõ **thể hiện mỗi quyết định trong
CVAT bằng cách nào** (attribute, giá trị, tag…), để quyết định đó nhìn thấy được trong file export.

## 8. Temporal rule

TODO — nếu là video/track: track bắt đầu/kết thúc khi nào, attribute nào mutable, xử lý chuyển trạng thái và bị che
ngắn. Task ảnh tĩnh ghi "Không áp dụng — task ảnh tĩnh".

## 9. Examples

TODO — positive, negative và edge case, mỗi ví dụ có sample_id (split example/calibration) và expected output.

| sample_id | Thấy gì | Expected output | Rule áp dụng |
|---|---|---|---|
| TODO | TODO | TODO | TODO |

## 10. Common mistakes

TODO — những lỗi reviewer có khả năng gặp nhiều nhất và cách tránh.
