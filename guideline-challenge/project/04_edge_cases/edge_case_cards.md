# Edge-case library

Kho lưu trữ 8 ca khó (Edge cases) phục vụ đánh giá chất lượng và xây dựng ground truth cho bài toán gán nhãn đèn giao thông.

---

CASE ID: EC01
Sample: BDD07
Scene: City-highway, ngã tư nhiều nhánh rẽ ban ngày
Observation: Cụm đèn treo gồm 2 mặt đèn vuông góc nhau (1 mặt đèn xanh hướng về xe mình, 1 mặt đèn vàng quay sang đường giao cắt).
Decision: LABEL (vẽ 2 box riêng biệt)
Expected: Box 1 (hướng xe mình): label=traffic_light, color=green, relevance=ego_lane. Box 2 (hướng cắt ngang): label=traffic_light, color=unknown, relevance=other_lane.
Rationale: Ngăn ngừa model học gộp 2 đầu đèn thành một, đồng thời phân biệt rõ luồng điều khiển của làn xe chủ.
Common mistake: Vẽ 1 box to bao trùm cả hai đầu đèn hoặc gán cả hai là ego_lane.
Diversity: conflict / geometry / critical

---

CASE ID: EC02
Sample: BDD17
Scene: Đường phố ban ngày trời mưa lớn (rainy)
Observation: Kính chắn gió đọng nhiều giọt nước làm mờ nhòe và méo mó hình dạng nguồn sáng đèn giao thông.
Decision: ESCALATE
Expected: label=traffic_light, color=unknown, relevance=ambiguous
Rationale: Khi nguồn sáng bị khúc xạ qua giọt nước không thể khẳng định chắc chắn màu sắc, annotator phải escalate để hệ thống tự hành thận trọng giảm tốc độ thay vì đoán mò.
Common mistake: Nhìn vệt sáng mờ đoán bừa thành màu đỏ hoặc xanh.
Diversity: occlusion / low_visibility / escalation

---

CASE ID: EC03
Sample: BDD18
Scene: Đường phố ban đêm (nighttime)
Observation: Đèn đỏ trên cao phát sáng mạnh trong bóng tối, tạo quầng lóa (glare/bloom) xung quanh nhưng không nhìn thấy rõ vỏ hộp đèn màu đen.
Decision: LABEL
Expected: label=traffic_light, color=red, relevance=ego_lane, geometry: box ôm sát bóng đèn phát sáng.
Rationale: Xe cần nhận diện đèn đỏ từ xa ban đêm để phanh kịp thời. Bắt buộc vẽ ôm bóng sáng, không bao trùm quầng lóa.
Common mistake: Kéo box rộng bao trùm cả vệt sáng lóa hoặc bỏ qua vì không thấy vỏ hộp.
Diversity: critical / night / geometry

---

CASE ID: EC04
Sample: BDD26
Scene: Đường phố ban đêm có nhiều đèn ở các khoảng cách khác nhau
Observation: Cột đèn bên phải ở khoảng cách xa, nguồn sáng nhỏ và mờ chìm trong nền trời tối.
Decision: LABEL
Expected: label=traffic_light, color=red, relevance=other_lane (hoặc ambiguous)
Rationale: Đảm bảo độ nhạy nhận diện (recall) của hệ thống đối với các đèn ở xa ở rìa góc nhìn.
Common mistake: Bỏ sót đầu đèn bên phải vì chú ý quá nhiều vào đèn xanh lớn bên trái.
Diversity: small_far / night

---

CASE ID: EC05
Sample: BDD11
Scene: Đường phố ban ngày nhiều mây (overcast)
Observation: Khung cảnh có cột giao thông gắn đèn người đi bộ và biển báo, không có đầu đèn giao thông điều khiển xe cơ giới nào.
Decision: IGNORE
Expected: count=0 (không vẽ bất kỳ box nào)
Rationale: Đèn người đi bộ và biển báo thuộc diện ngoài scope. Vẽ nhầm sẽ khiến xe tự hành phản ứng sai lệch trước luồng người đi bộ.
Common mistake: Nhầm đèn tín hiệu người đi bộ thành đèn giao thông cơ giới.
Diversity: negative / out_of_scope

---

CASE ID: EC06
Sample: BDD15
Scene: Đường cao tốc ngoại ô ban ngày
Observation: Cột đèn chiếu sáng đường phố và các biển báo chữ nhật trên cao, không có đèn tín hiệu điều khiển giao thông.
Decision: IGNORE
Expected: count=0 (bỏ qua toàn bộ đèn đường và biển báo)
Rationale: Phân biệt rõ đèn giao thông cơ giới với đèn chiếu sáng công cộng để tránh False Positive.
Common mistake: Nhầm chóa đèn đường ở xa thành đèn giao thông và gán color=unknown.
Diversity: negative / conflict

---

CASE ID: EC07
Sample: BDD24
Scene: Đường đô thị trong điều kiện bão tuyết (snowy)
Observation: Tuyết phủ trắng xóa mặt đường và bám lên thân cột đèn, đèn đỏ trên cao nổi bật giữa nền trời sáng mờ.
Decision: LABEL
Expected: label=traffic_light, color=red, relevance=ego_lane, geometry: box ôm sát vỏ đèn nhìn thấy dung sai ≤ 2px.
Rationale: Lỗi an toàn tối thượng — nhận diện chính xác đèn đỏ trong thời tiết khắc nghiệt để xe dừng xe an toàn, tránh trơn trượt.
Common mistake: Bị viền tuyết đánh lừa làm vẽ box quá rộng hoặc gán sai làn do không thấy vạch sơn tuyết phủ.
Diversity: critical / edge_weather

---

CASE ID: EC08
Sample: BDD02
Scene: Đại lộ New York ban ngày có giá long môn treo nhiều đầu đèn
Observation: Nhiều đầu đèn xanh treo thẳng các làn đường khác nhau (làn đi thẳng và làn rẽ).
Decision: LABEL
Expected: Đèn thẳng hướng xe: color=green, relevance=ego_lane. Đèn làn rẽ lệch hướng: color=green, relevance=other_lane.
Rationale: Tránh lỗi an toàn nghiêm trọng khi xe đi thẳng lại tuân theo đèn rẽ của làn bên cạnh.
Common mistake: Để mặc định ego_lane cho tất cả các đầu đèn trên cùng một giá treo.
Diversity: critical / multi_lane_relevance
