# Sổ quy tắc gán nhãn — Trạng thái và Tính liên quan của Đèn giao thông
*(Annotation Guideline — Traffic Light State and Ego Relevance)*

**Version:** v2

---

## 1. Mục tiêu và Phạm vi (Objective & Scope)

### Mục tiêu
Tài liệu này hướng dẫn người gán nhãn (annotator) cách xác định vị trí, trạng thái màu sắc và tính liên quan của đèn giao thông đối với xe chở camera hành trình. Mục tiêu là giúp hệ thống lái xe tự động nhận biết chính xác khi nào cần dừng hoặc được phép di chuyển.

### Trong phạm vi cần vẽ (In-scope — BẮT BUỘC VẼ)
- Mọi đầu đèn giao thông dùng để điều khiển luồng phương tiện cơ giới (ô tô, xe máy) xuất hiện phía trước xe.
- Đèn có thể gắn trên cột lề đường, treo trên giá long môn hoặc dây treo ngang đường.
- Kích thước chiều cao của hộp đèn phải **tối thiểu từ 10 pixel trở lên** (khoảng bằng đầu ngón tay út khi nhìn trên màn hình ở độ phóng đại bình thường).

### Ngoài phạm vi (Out-of-scope — BỎ QUA, KHÔNG VẼ)
- **Đèn người đi bộ:** Hộp đèn có biểu tượng hình người đi bộ (màu đỏ/xanh), thường gắn thấp trên cột sát vỉa hè $\rightarrow$ **KHÔNG VẼ**.
- **Đèn xe buýt / xe đạp riêng biệt:** Đèn có hình xe đạp hoặc ký hiệu chữ riêng $\rightarrow$ **KHÔNG VẼ**.
- **Đèn quá xa hoặc quá mờ:** Chiều cao nhỏ hơn 10 pixel, chỉ là một chấm sáng mờ mịt không thấy hình dạng vỏ hộp $\rightarrow$ **KHÔNG VẼ**.
- **Nguồn sáng gây nhầm lẫn:** Đèn chiếu sáng đường, đèn đuôi xe phía trước, biển quảng cáo LED, bóng đèn phản chiếu trên mặt kính/mặt đường $\rightarrow$ **KHÔNG VẼ**.
- **Đèn quay lưng:** Đầu đèn của chiều đường ngược lại chỉ nhìn thấy mặt lưng màu đen hoặc cạnh bên $\rightarrow$ **KHÔNG VẼ**.

---

## 2. Đơn vị gán nhãn (Annotation Unit)

- **Quy tắc 1 hộp đèn = 1 khung hình chữ nhật (1 Bounding Box):** Mỗi cụm vỏ đèn vật lý riêng biệt (thường gồm 3 bóng đỏ - vàng - xanh nằm dọc hoặc ngang) là **1 đối tượng duy nhất**.
- **TUYỆT ĐỐI KHÔNG VẼ GỘP:** Nếu trên cùng một giá treo hoặc cùng một cột có 2 hoặc 3 hộp đèn nằm cạnh nhau (ví dụ: 1 hộp đèn đi thẳng, 1 hộp đèn rẽ trái), bạn phải vẽ **2 hoặc 3 khung chữ nhật riêng biệt**. Tuyệt đối không vẽ một khung to ôm trùm tất cả các hộp đèn.

---

## 3. Quy tắc vẽ khung hình chữ nhật (Geometry Rule)

- **Công cụ:** Chọn công cụ vẽ hình chữ nhật (**Draw new rectangle**, chế độ **Shape**) trên CVAT.
- **Ôm sát mép nhìn thấy (Tight to visible):** Bốn cạnh của khung chữ nhật phải tiếp xúc vừa khít với mép ngoài cùng của vỏ hộp đèn mà mắt bạn nhìn thấy trên ảnh.
  - Không vẽ thiếu (cắt lẹm vào thân đèn).
  - Không vẽ thừa (thừa khoảng trời, cành cây hay cột thép).
- **Dung sai (Tolerance):** Sai lệch cạnh cho phép không quá **2 pixel** (sai số rất nhỏ, cần phóng to ảnh khi vẽ).
- **Đèn bị che khuất một phần:** Chỉ vẽ khung ôm lấy phần vỏ đèn thực tế đang nhìn thấy, không tự tưởng tượng để vẽ bù phần bị che.

---

## 4. Danh mục nhãn và Thuộc tính (Taxonomy)

Trong hệ thống chỉ có duy nhất **1 loại nhãn (Class):** `traffic_light`.

Sau khi vẽ xong khung chữ nhật, bạn **bắt buộc phải kiểm tra và chọn 2 thuộc tính (Attributes)** ở bảng bên phải màn hình:

### Thuộc tính 1: `color` — Trạng thái màu của đèn
| Giá trị | Khi nào chọn? | Giải thích dễ hiểu |
|---|---|---|
| `red` | Đèn đỏ đang bật sáng | Có bóng màu đỏ hoặc mũi tên đỏ đang phát sáng rõ ràng. |
| `yellow` | Đèn vàng đang bật sáng | Có bóng màu vàng/hổ phách đang phát sáng. |
| `green` | Đèn xanh đang bật sáng | Có bóng màu xanh lá cây hoặc mũi tên xanh đang phát sáng. |
| `off` | Đèn đang tắt toàn bộ | Cả 3 bóng đều tối đen (đèn hỏng, mất điện hoặc ngã tư chưa bật đèn). |
| `unknown` | Không thể khẳng định màu | Đèn bị lóa sáng ban đêm, quá mờ, bị cành cây che bóng đèn hoặc bạn không chắc chắn. |

> ⚠️ **LƯU Ý:** Giá trị mặc định là `unknown`. Bạn phải chủ động nhìn và chọn màu đúng (`red`, `yellow`, `green`). Nếu mắt nhìn rõ màu mà bạn lười không chọn, để nguyên `unknown` thì sẽ bị tính là lỗi nặng!

### Thuộc tính 2: `relevance` — Đèn có điều khiển làn xe mình không?
| Giá trị | Khi nào chọn? | Giải thích dễ hiểu |
|---|---|---|
| `ego_lane` | **Điều khiển làn xe mình** | Đèn nằm thẳng hướng di chuyển của xe mình, hoặc treo ngay trên làn đường xe mình đang đi. Xe mình phải tuân theo đèn này. |
| `other_lane` | **Điều khiển làn đường khác** | Đèn dành cho làn rẽ trái/phải riêng biệt, đèn của hướng đường giao cắt vuông góc, hoặc đèn của chiều đường ngược lại. |
| `ambiguous` | **Không chắc chắn** | Ngã tư quá phức tạp, có nhiều đầu đèn nhưng không có vạch phân làn rõ ràng, góc nhìn bị xiên khiến bạn không thể khẳng định đèn này dành cho xe mình hay làn bên cạnh. |

> ⚠️ **QUY TẮC AN TOÀN:** Nếu bạn thấy đèn mũi tên rẽ trái/rẽ phải mà xe mình đang đi thẳng ở làn giữa, bắt buộc phải chọn `other_lane`. Gán nhầm đèn rẽ thành `ego_lane` là lỗi đặc biệt nghiêm trọng (Critical error)!

---

## 5. Bảng tổng hợp: Bắt buộc vẽ vs Bỏ qua (Inclusion / Exclusion)

| Trường hợp | Hành động | Thuộc tính cần gán |
|---|---|---|
| Đèn tín hiệu giao thông chính diện, cao $\ge 10$ px | **Vẽ khung** | Chọn đúng màu (`red`/`green`/`yellow`) và `relevance = ego_lane` |
| Đèn làn rẽ phụ bên cạnh | **Vẽ khung** | Chọn đúng màu và `relevance = other_lane` |
| Đèn bị cành cây/biển báo che khuất $< 50\%$ | **Vẽ khung** | Ôm phần nhìn thấy; nếu thấy rõ bóng sáng thì chọn màu đó, không thì chọn `unknown` |
| Đèn bị che khuất nặng $\ge 50\%$ | **BỎ QUA** | Không vẽ |
| Đèn người đi bộ (hình người) | **BỎ QUA** | Không vẽ |
| Đèn quá nhỏ / quá xa (chiều cao $< 10$ px) | **BỎ QUA** | Không vẽ |
| Đèn giao lộ phức tạp không rõ làn | **Vẽ khung** | Chọn đúng màu và `relevance = ambiguous` |

---

## 6. Quy tắc khi gặp tình huống khó (Visibility & Edge cases)

1. **Đèn bị che khuất (Occlusion):**
   - Nếu cành cây hoặc cột biển báo che một góc đèn (dưới 50% diện tích): Hãy vẽ khung ôm sát phần vỏ đèn còn lộ ra.
   - Nếu bị che quá nửa thân đèn hoặc không thể nhận diện chắc chắn đây là đèn giao thông: Bỏ qua không vẽ.
2. **Cảnh ban đêm (Nighttime) và Đèn bị lóa (Glare):**
   - Ban đêm bóng đèn phát sáng thường tạo ra quầng sáng chói (glare/bloom) xung quanh.
   - **Cách vẽ đúng:** Chỉ vẽ ôm lấy nguồn sáng hình tròn của bóng đèn (và phần vỏ đèn mờ mờ nếu thấy). **Tuyệt đối không vẽ bao trùm quầng sáng loang lổ ra ngoài khoảng tối**.
   - Nếu đèn bị chói lóa đến mức trắng xóa không thể phân biệt là đỏ hay vàng/xanh: Chọn `color = unknown`.
3. **Ảnh trời mưa / tuyết (Rain / Snow):**
   - Giọt nước trên kính chắn gió làm nhòe đèn: Nếu vẫn định hình được hộp đèn thì vẽ khung và chọn `color = unknown` nếu màu bị nhòe mờ.

---

## 7. Quy tắc xử lý khi phân vân (Ambiguity & Escalation)

- **Nguyên tắc "Thà nhận không biết, hơn là đoán mò":** Hệ thống tự hành thà biết rằng thông tin bị mơ hồ (`ambiguous` / `unknown`) để giảm tốc độ thận trọng, còn hơn là nhận một nhãn sai để vượt đèn đỏ.
- **Cách thể hiện trên CVAT:**
  - Không phân biệt được đèn xanh hay đỏ $\rightarrow$ Chọn `color = unknown`.
  - Không chắc chắn đèn điều khiển làn nào $\rightarrow$ Chọn `relevance = ambiguous`.
  - Nghi ngờ vật thể lạ không biết có phải đèn hay không $\rightarrow$ Bỏ qua không vẽ.

---

## 8. Quy tắc theo thời gian (Temporal Rule)

- **Không áp dụng — Task ảnh tĩnh:** Bài tập này sử dụng các bức ảnh chụp độc lập (Static images). Bạn xử lý từng ảnh một cách độc lập, không cần liên kết hay theo dõi (track) qua các frame khác nhau.

---

## 9. Bảng ví dụ minh họa (Examples)

Dưới đây là 3 trường hợp điển hình trong tập ảnh mẫu để người gán nhãn đối chiếu:

| Sample ID | Tình huống trong ảnh | Kết quả cần vẽ (Expected output) | Quy tắc áp dụng |
|---|---|---|---|
| `BDD02` | Đường phố ban ngày (`city street`, mây nhẹ). Có cụm đèn treo trên giá ngang và cột bên phải. | Vẽ 2 khung riêng biệt cho 2 hộp đèn nhìn rõ: cả hai đều gán `color = green` và `relevance = ego_lane`. | Mỗi hộp đèn vẽ 1 khung riêng; đèn thẳng hướng xe mình là `ego_lane`. |
| `BDD13` | Ngã tư ban ngày nắng rõ (`clear daytime`), có hộp đèn đi thẳng và hộp đèn rẽ trái riêng. | Vẽ 2 khung riêng: Box 1 (đi thẳng) chọn `relevance = ego_lane`; Box 2 (rẽ trái) chọn `relevance = other_lane`. | Tách biệt rõ ràng làn xe chủ và làn rẽ khác; không vẽ gộp. |
| `BDD18` | Đường phố ban đêm (`night`). Đèn đỏ trên cao phát sáng giữa trời tối. | Vẽ 1 khung ôm lấy nguồn sáng đèn đỏ và mép vỏ đèn nhìn thấy; gán `color = red`, `relevance = ego_lane`. | Ban đêm vẽ sát bóng đèn, không vẽ lan ra quầng sáng chói; nhận diện đúng đèn đỏ. |

---

## 10. 5 Lỗi sai phổ biến nhất cần tuyệt đối tránh (Common Mistakes)

1. **Lỗi vẽ gộp hộp đèn:** Thấy 2 hộp đèn treo cạnh nhau liền tiện tay kéo 1 khung to bao cả hai $\rightarrow$ **SAI**. Mỗi hộp đèn là 1 khung độc lập.
2. **Lỗi vẽ đèn người đi bộ:** Tiện tay khoanh luôn cả hộp đèn hình người đi bộ ở cột vỉa hè $\rightarrow$ **SAI**. Phải bỏ qua hoàn toàn đèn người đi bộ.
3. **Lỗi lười đổi thuộc tính:** Mắt nhìn thấy rất rõ đèn màu đỏ/xanh nhưng quên không chọn, để nguyên giá trị mặc định là `unknown` $\rightarrow$ **SAI**. Phải chủ động click chọn màu.
4. **Lỗi gán nhầm làn rẽ thành làn xe mình:** Đèn có mũi tên rẽ hoặc nằm lệch hẳn sang làn rẽ nhưng lại chọn `ego_lane` $\rightarrow$ **NGUY HIỂM CHẾT NGƯỜI**. Phải chọn `other_lane`.
5. **Lỗi vẽ khung quá rộng:** Khung chữ nhật bao thừa cả khoảng trời, ngọn cây hoặc cột đèn $\rightarrow$ **SAI**. Bốn mép khung phải ôm sát vào cạnh ngoài của vỏ hộp đèn (dung sai $\le 2$ pixel).
