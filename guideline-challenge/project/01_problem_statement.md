# Problem statement + downstream contract

Tài liệu xác lập phạm vi bài toán gán nhãn đèn giao thông tại giao lộ phức tạp phục vụ hệ thống tự hành.

## Bài toán

Xác định khung bao (2D Bounding Box), trạng thái tín hiệu màu (Color State) và tính liên quan tới làn xe chủ (Ego Relevance) của đèn tín hiệu giao thông tại các giao lộ có nhiều đầu đèn từ ảnh camera hành trình phía trước.

## Downstream contract

1. **Downstream task / model / user là ai?**
   Mô-đun Perception & Motion Planning của hệ thống tự hành cấp độ L2+/L3. Bộ lập quỹ đạo (Planner) cần biết đèn nào đang trực tiếp điều khiển làn xe đang đi để đưa ra quyết định tiếp tục di chuyển, giảm tốc hay dừng khẩn cấp trước vạch dừng.
2. **Output annotation nào thực sự cần?**
   - Geometry: 2D Bounding Box bao quanh vỏ hộp đèn nhìn thấy (visible housing).
   - Class: `traffic_light`.
   - Attributes:
     - `color`: `red`, `yellow`, `green`, `off`, `unknown`.
     - `relevance`: `ego_lane` (điều khiển làn xe mình), `other_lane` (làn rẽ trái/phải, làn đường nhánh/ngược chiều), `ambiguous` (không đủ bằng chứng xác định).
3. **Failure nào gây hậu quả lớn nhất?**
   - Bỏ sót đèn đỏ của làn mình (False Negative trên `ego_lane` + `red`).
   - Phân loại nhầm đèn xanh của làn rẽ/làn khác thành đèn của làn mình (`relevance` bị gán sai từ `other_lane` thành `ego_lane`). Cả hai lỗi này đều dẫn tới nguy cơ xe tự hành vượt đèn đỏ gây tai nạn thảm khốc tại giao lộ.
4. **Khi ambiguity không resolve được, ai / ở đâu là escalation path?**
   Gán thuộc tính `relevance = ambiguous`. Nếu đèn bị che khuất nghiêm trọng hoặc không rõ có phải đèn xe cơ giới hay không, annotator chọn `color = unknown` và báo cáo trường hợp cần review.

## Scope

- **Trong scope (bắt buộc label):** Mọi đầu đèn giao thông điều khiển phương tiện cơ giới nhìn thấy được trên cột, giá long môn hoặc dây treo phía trước xe, có chiều cao bounding box ≥ 10 pixel và nhìn rõ tối thiểu một phần vỏ hộp đèn hoặc nguồn sáng.
- **Ngoài scope (ignore):** Đèn tín hiệu dành riêng cho người đi bộ (hình người), đèn cho xe đạp/xe bus chuyên dụng, đèn đuôi xe ô tô phía trước, biển quảng cáo led, đèn đường, và các đầu đèn quá xa/mờ (chiều cao < 10 pixel).
- **Geometry tolerance:** Bounding box hình chữ nhật 2D ôm sát vỏ hộp đèn nhìn thấy (visible housing), sai lệch mỗi cạnh không quá 2 pixel (tolerance ≤ 2 px).

## Output chấm được

Mọi quyết định phải thể hiện rõ trong file XML export từ CVAT:
- Quyết định gắn nhãn: Tồn tại box với class `traffic_light`.
- Quyết định thuộc tính: Thẻ `<attribute name="color">` và `<attribute name="relevance">` có giá trị khớp với quy chuẩn.
- Bỏ qua (Ignore): Không có box trên đối tượng ngoài scope.

## Dữ liệu và giới hạn

Sử dụng tập ảnh tĩnh trích xuất từ camera hành trình phía trước thuộc dataset BDD100K (`data/bdd100k/`). Độ phân giải ảnh 1280x720, đa dạng điều kiện thời tiết (nắng, nhiều mây, mưa, đêm, chạng vạng). Tổng số ảnh dự kiến dùng là 15 ảnh (3 example, 8 calibration, 4 blind).
