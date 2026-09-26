# Feedback blind test — nhóm DeadlineDodgers gửi nhóm Traffic Light

File export: `deadlinedodgers_peer.zip` (CVAT for images 1.1, 4 ảnh BDD02, BDD15, BDD18, BDD26). Dùng trực tiếp file
zip này cho `make score FILE=deadlinedodgers_peer.zip`.

## 1. Rule nào rõ nhất / giúp quyết định nhanh nhất?

Quy tắc "1 hộp đèn = 1 khung, tuyệt đối không vẽ gộp" (mục 2) và danh sách không vẽ ở mục 1 (đèn người đi bộ, đèn
quay lưng, đèn đường, đèn hậu xe, phản chiếu): quyết định vẽ hay bỏ rất nhanh. Nguyên tắc "thà nhận không biết hơn là
đoán mò" (mục 7) giúp chọn `unknown` / `ambiguous` mà không phân vân.

## 2. Rule nào mơ hồ hoặc phải tự suy diễn?

- **Đèn ban đêm chỉ thấy bóng đang sáng, không thấy vỏ hộp:** mục 6.2 bảo vẽ ôm nguồn sáng ("và vỏ đèn nếu thấy"),
  nhưng mục 1 bảo "chấm sáng không thấy vỏ hộp → không vẽ". Không rõ bóng sáng to, rõ màu mà không thấy vỏ thì vẽ hay
  bỏ. Chúng tôi vẽ nếu bóng từ khoảng 10 px trở lên.
- **Mốc 10 px:** "bằng đầu ngón tay út" phụ thuộc màn hình và mức zoom; CVAT không cho đo trực tiếp. Đèn nằm ngang
  thì không rõ "chiều cao" tính theo cạnh nào.
- **`relevance`:** quy tắc an toàn dựa vào "xe mình đang đi thẳng ở làn giữa", nhưng ảnh tĩnh không cho biết xe sẽ
  đi thẳng hay rẽ. Mục 5 dòng 1 ghi "đèn chính diện → `ego_lane`", dễ hiểu là mọi đèn phía trước đều `ego_lane` khi
  có nhiều đầu đèn.
- **Đèn mũi tên:** `color` chỉ ghi màu, không ghi được mũi tên hay đèn tròn; mũi tên xanh rẽ trái và đèn tròn xanh
  ra cùng một nhãn.

## 3. Sample nào khiến guideline "vỡ"?

- **BDD18, BDD26 (ban đêm):** gặp đúng trường hợp chỉ thấy bóng sáng và quầng lóa, không thấy vỏ hộp; mục 1 và mục
  6.2 cho hai hướng khác nhau.
- **BDD02, BDD18:** hai ảnh này nằm trong gói blind nhưng đã được dùng làm ví dụ có đáp án ở mục 9 guideline, nên
  người vẽ thấy trước đáp án và kết quả trên hai ảnh này không đo được guideline. Theo luật lab, ví dụ chỉ lấy từ ảnh
  example/calibration. Ví dụ BDD13 không có trong gói nên peer không xem được.

## 4. Attribute / default nào trong CVAT dễ gây thao tác sai?

- **`relevance` mặc định `ego_lane`:** quên đổi thì đèn làn rẽ / đường giao cắt thành đèn điều khiển xe mình, đúng
  loại lỗi critical guideline cảnh báo, và export không phân biệt được "đã chọn" với "quên chọn".
- **`color` mặc định `unknown`:** tương tự, không phân biệt người vẽ chủ động chọn `unknown` hay quên chọn, trong khi
  guideline tính "quên" là lỗi nặng.
- **Không có cách escalate:** thiếu checkbox kiểu `needs_review`; mục 7 dạy "nghi ngờ thì bỏ qua không vẽ", dễ bỏ sót
  đèn thật.

## 5. Một thay đổi cụ thể giúp annotator mới ít hỏi hơn?

Đặt `__undefined__` làm default cho cả `color` và `relevance`, thêm checkbox `needs_review`; viết lại quy tắc ban đêm
thành một dòng: "bóng sáng rõ màu, từ 10 px trở lên → vẽ khung ôm nguồn sáng dù không thấy vỏ; chấm sáng dưới 10 px →
không vẽ"; và thay ví dụ mục 9 bằng ảnh không nằm trong blind, kèm ảnh chụp có khung vẽ sẵn.
