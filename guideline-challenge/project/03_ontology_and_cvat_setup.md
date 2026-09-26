# Ontology + CVAT setup

Bảng ontology là **source of truth** cho schema CVAT: `03_cvat_labels.json` phải khớp từng dòng ở đây.

## Ontology table

| Name | Geometry | Type | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| `traffic_light` | Rectangle | class | — | — | — | Đối tượng chính cần label |
| `color` | — | attribute của `traffic_light` | `red`, `yellow`, `green`, `off`, `unknown` | `unknown` | No | Trạng thái đèn tại thời điểm chụp; không đổi trong cùng một frame ảnh tĩnh |
| `relevance` | — | attribute của `traffic_light` | `ego_lane`, `other_lane`, `ambiguous` | `ego_lane` | No | Đèn nào điều khiển làn xe ego-vehicle; cần thiết để model không học nhầm tín hiệu của làn bên cạnh |

## Class hay attribute

- **`traffic_light` là class** (không phải attribute) vì nó là một **instance vật lý** trong ảnh — mỗi bộ đèn vẽ một bounding box riêng. Detector cần học vị trí + hình dạng của nó.
- **`color` là attribute**, không phải class riêng, vì nó mô tả **trạng thái** của cùng một bộ đèn — tách class thành `traffic_light_red`, `traffic_light_green`... làm phình taxonomy và gây nhầm khi đèn chuyển màu.
- **`relevance` là attribute** vì cùng một bộ đèn vẫn là một instance — chỉ cần đánh dấu nó phục vụ làn nào, không cần vẽ box mới.
- **Default `color = unknown`** có thể gây bias nếu annotator quên đổi → cần kiểm tra calibration: tỷ lệ `unknown` cao bất thường là dấu hiệu bỏ sót attribute.
- **Default `relevance = ego_lane`** hợp lý cho city street (đèn chính diện thường là ego), nhưng annotator phải chủ động đổi sang `other_lane` khi đèn rõ ràng là làn vuông góc.

## CVAT

- **Phiên bản CVAT** (`make cvat-status`): Xem kết quả lệnh trên máy — thường là CVAT 2.x (cài từ Day 2/Day 8)
- **Tên task calibration**: `traffic-light-calib-v1` (đặt theo format `<topic>-calib-v<guideline_version>`)
- **Guide của task đã dán `02_guideline.md`?**: Dán nội dung `02_guideline.md` vào ô **Guide** khi tạo task CVAT → **có**
- **Nhóm dùng Shape (không dùng Track), vì sao**: Task này dùng **ảnh tĩnh** (image, không phải video) — Track chỉ cần thiết khi label video liên tiếp và muốn theo dõi object qua nhiều frame. Shape đủ dùng và đơn giản hơn cho calibration.

## Setup test

Một thành viên **chưa tham gia setup** (ví dụ TV3 hoặc TV4) mở task calibration và trả lời:

| Câu hỏi kiểm tra | Expected answer |
|---|---|
| Label object nào? | Mỗi bộ đèn giao thông → 1 rectangle |
| Dùng tool nào trong CVAT? | Rectangle (Shape mode) |
| Gán attribute gì? | `color` (chọn đúng màu đang hiển thị) + `relevance` (ego/other/ambiguous) |
| Khi đèn bị che >50%? | Vẫn label nếu nhận ra là đèn; gán `color=unknown` nếu không đọc được màu |
| Khi nào escalate? | Khi không xác định được `relevance` (đèn ở ngã tư phức tạp) → gán `ambiguous` |
| Đèn nhỏ ở xa có label không? | Có, nếu nhìn thấy và phân biệt được với background |

**Kết quả setup test**: Điền tên thành viên test và ghi lại chỗ vấp sau khi chạy task calibration thực tế trong buổi lab. Ví dụ: "TV3 test — vấp ở bước gán relevance khi đèn ở ngã tư 4 chiều, xử lý bằng cách escalate sang ambiguous."
