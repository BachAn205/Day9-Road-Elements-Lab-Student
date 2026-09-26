# QA plan + quality gates

Không được viết "reviewer kiểm tra lại". Phải có sampling, metric, threshold và action khi fail. Thay mọi placeholder
mới là xong (gate G6).

## Flow

Guideline → Calibration → Production → Self-QC → Review → Rework → Quality Gate. Ghi cụ thể cho project của nhóm:

- **Ai review, review bao nhiêu:** Một thành viên chưa tự vẽ ảnh đó (khác người label gốc) review lại 100% ảnh có
  tag `critical`, `edge`, `ambiguity`, cộng thêm random 20% ảnh tag `normal`.
- **Chọn sample theo rule nào** (random, theo tag rủi ro, theo annotator mới…): Theo rủi ro (risk-based) — ưu tiên
  toàn bộ ảnh có khả năng ảnh hưởng an toàn (đèn `other_lane` dễ bị nhầm thành `ego_lane`), phần còn lại lấy random để
  kiểm tra tổng thể.
- **Issue được ghi ở đâu, đóng thế nào:** Ghi vào `06_calibration_report.csv` (giai đoạn calibration nội bộ) hoặc cột
  `note` trong `transfer_score.csv` (giai đoạn blind test). Issue đóng khi guideline đã được cập nhật và version hoá,
  hoặc annotator đã được coaching và label đúng ở lần kiểm tra sau.
- **Khi phát hiện guideline gap thì update và version ra sao:** Tăng số `Version` trong `02_guideline.md` (v1 → v2 →
  v3), ghi lại lý do đổi vào `08_revision_log.md`, rồi dán lại bản mới vào **Guide** của task CVAT đang dùng (CVAT
  không tự lưu lịch sử Guide).

## Defect severity

Nhóm được đổi mapping nếu downstream contract khác, nhưng phải giải thích và chốt trước khi QA.

| Severity | Định nghĩa cho project này | Ví dụ | Action mặc định |
|---|---|---|---|
| Critical | Lỗi khiến hệ thống lái xe tự hành ra quyết định nguy hiểm ngay lập tức | Gán `relevance=ego_lane` cho đèn thực ra điều khiển làn khác (`other_lane`), hoặc gán sai màu đỏ ↔ xanh | REJECT ảnh đó, coaching lại annotator ngay trước khi giao thêm việc |
| Major | Ảnh hưởng chất lượng dữ liệu huấn luyện nhưng không gây nguy hiểm tức thời | Vẽ gộp 2 hộp đèn cạnh nhau thành 1 box, bỏ sót một đèn hợp lệ, chọn `color=unknown` dù nhìn rõ được màu | REWORK — annotator vẽ lại ảnh đó |
| Minor | Sai lệch hình học nhỏ, vẫn nằm trong dung sai cho phép nhưng chưa tối ưu | Box lệch nhẹ so với mép đèn nhưng vẫn ≤ 2px theo rule ở mục 3 guideline | Ghi nhận, không bắt sửa ngay; gom lại để cải thiện guideline nếu lặp lại nhiều |
| Question | Annotator không chắc và đã escalate đúng quy trình | Đã chọn `relevance=ambiguous` hoặc `color=unknown` theo đúng rule ambiguity | QA trả lời trực tiếp, ghi log; không tính là lỗi của annotator |

## Metrics

| Metric | Cách tính | Vì sao phù hợp với bài toán |
|---|---|---|
| Color accuracy | % object có `color` khớp gold decision / tổng object được review | Đo trực tiếp việc nhận diện trạng thái đèn — input chính cho quyết định dừng/đi |
| Relevance accuracy | % object có `relevance` khớp gold decision / tổng object được review | Đây là attribute có hậu quả an toàn nặng nhất (đèn của làn khác) |
| Geometry tightness | % box có IoU ≥ ngưỡng (ví dụ 0.7) so với box gold | Box quá rộng/hẹp làm sai lệch input cho model downstream |

Metric high-risk tách riêng (ví dụ critical defect escape rate): **Critical defect escape rate** = số quyết định
`critical` bị sai / tổng số quyết định `critical` trong gold — đo riêng vì đây là chỉ số quyết định PASS/REJECT, không
được gộp trung bình với các lỗi nhẹ.

## Quality gate

Threshold là đề xuất của nhóm, không phải chuẩn ngành. Giải thích trade-off cost/risk.

```text
PASS if:
  color accuracy >= 90% and relevance accuracy >= 90% and critical defect escape rate = 0%
REWORK if: color accuracy hoặc relevance accuracy nằm trong khoảng 75-90%, hoặc có lỗi critical nhưng đã bắt được lúc review nội bộ (chưa lọt xuống downstream)
REJECT / ESCALATE if: color accuracy hoặc relevance accuracy dưới 75%, hoặc có lỗi critical lọt xuống downstream mà QA không phát hiện trước khi bàn giao
```

Trade-off: Ngưỡng PASS chọn cao (90%) và critical defect escape rate bắt buộc bằng 0% vì hậu quả của lỗi
`relevance`/`color` sai ảnh hưởng trực tiếp tới an toàn (xe có thể vượt đèn đỏ hoặc đi theo đèn của làn khác). Đổi
lại, nhóm chấp nhận tốn thêm thời gian review 100% ảnh rủi ro cao thay vì chỉ sample ngẫu nhiên toàn bộ.
