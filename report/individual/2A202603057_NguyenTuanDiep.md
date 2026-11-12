# Báo cáo thực hành Day 3 — điền trực tiếp trong fork của bạn

**Cách dùng:** Thay mọi dấu `…` bằng bài làm thật của bạn trước khi nộp link fork trên [LMS](https://aithucchien.vinuni.edu.vn). Giữ nguyên bốn mục và bảng để coach đọc nhanh. Viết ngắn, cụ thể theo ảnh/vùng; không cần thuật ngữ chuyên sâu. Ví dụ trong [hướng dẫn mẫu](../../reports/REPORT_TEMPLATE.md) chỉ giúp hiểu cách điền, không phải câu trả lời để chép lại.

- Mã học viên theo lớp: 2A202603057 (Họ và tên: Nguyen Tuan Diep)
- Ngày / CVAT local: 11/11/2026 (hoàn thiện 12/11/2026) / CVAT v2.74.1 local chạy qua Docker
- Công cụ đã dùng: CVAT local (công cụ Brush, Polygon), SAM Assistant

Mã học viên là mã lớp cấp; không cần ghi họ tên trong report nếu kênh [LMS](https://aithucchien.vinuni.edu.vn) đã nhận diện bạn. Chỉ ghi công cụ thật sự đã dùng; không có SAM vẫn làm bài bình thường.

## 1. Bài đã nộp

Ghi tên ZIP đúng như file trong `submissions/` và số ảnh đã vẽ, Save. Chưa làm hoặc export lỗi thì ghi `chưa có`, không tạo ZIP rỗng. Cột điểm là điểm tối đa của task, **không phải điểm tự chấm**.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

*Ghi chú về bài nộp:* Tôi đã hoàn thành đầy đủ cả 3 tier chính (`easy_semantic`, `medium_instance`, `hard_panoptic`) và toàn bộ 6 trạm checkpoint (`cp1_holes`, `cp2_slice`, `cp5_occlusion`, `cp3_thin`, `cp4_curb`, `cp6_coverage`). Tất cả các file ZIP đều được xuất đúng định dạng quy định (COCO 1.0 cho instance/panoptic, Segmentation mask 1.1 cho semantic), đã được kiểm tra cấu trúc qua `scripts/inspect_submissions.py` và đều đạt trạng thái `[OK]`.

## 2. Một quyết định trước khi dùng gợi ý

Chọn object đầu tiên bạn tự vẽ ở `medium_instance`, trước khi xem bất kỳ đề xuất tự động nào cho object đó. Ghi ảnh/vị trí đủ để tìm lại; “quy tắc biên” là lý do bạn chọn hoặc dừng mask ở ranh đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh `000000181542.jpg`, object là người lái xe máy (`person`) ở góc dưới bên phải (tọa độ bbox khoảng x: 546 đến 639, y: 149 đến 458).
- Class và quy tắc tôi dùng để chọn biên: Class `person`. Quy tắc biên tôi áp dụng là chỉ vẽ theo phần nhìn thấy thực tế của cơ thể người (đầu đội mũ bảo hiểm, thân trên, cánh tay và chân). Ranh giới khó nhất là điểm tiếp xúc giữa chân/mông người với chiếc xe máy: tôi quyết định dừng mask `person` ngay tại mép quần/đùi tiếp giáp yên xe và bình xăng xe máy, dứt khoát không vẽ lan sang các bộ phận của xe máy (`motorcycle`), đồng thời không cố vẽ tưởng tượng các phần cơ thể bị che khuất sau yếm xe.
- Nếu dùng gợi ý sau đó: vùng gợi ý sai/đúng, hành động sửa/giữ và lý do: Khi thử dùng gợi ý tự động (SAM) cho các đối tượng người khác, mô hình thường bị lem mask: phần người thường bị dính gộp luôn một phần yên xe máy hoặc tay lái vào nhãn `person`, và vùng kẽ giữa cánh tay với thân người thường bị ăn dính cả mảng nền phía sau. Tôi đã dùng công cụ Brush chế độ tẩy (Eraser) để xóa bỏ phần mask lem sang xe máy và gọt sạch phần nền ở kẽ tay, chỉ giữ lại phần thân người chuẩn xác.
- Nếu không dùng gợi ý: không áp dụng (tôi có dùng kết hợp gợi ý sau khi đã tự vẽ hoàn chỉnh object đầu tiên).

## 3. Một lỗi tôi tìm thấy và sửa

Chọn một lỗi **có thật** trong bài. Nếu công cụ lỗi khiến bạn chưa sửa được, ghi rõ đã thử gì và cần coach hỗ trợ gì; không ghi “đã sửa” khi chưa sửa.

- Task/ảnh/vùng: Task `medium_instance`, ảnh `000000373353.jpg`, khu vực gần giữa ảnh nơi có nhóm người đi bộ đi sát nhau phía sau hàng xe ô tô.
- Lỗi thuộc loại: sai lớp / thiếu-thừa vật / gộp-tách / biên / phủ vùng / khác: Lỗi gộp-tách (gộp nhầm hai đối tượng riêng biệt thành một mask).
- Bằng chứng tôi nhìn thấy: Khi bật hiển thị viền nhãn trên CVAT và kiểm tra danh sách Objects, tôi phát hiện hai người đi bộ (`person`) đi cạnh nhau gần tâm ảnh bị phủ chung bởi một polygon duy nhất (do lúc dùng nét brush quét nhanh đã làm dính mép áo của hai người lại với nhau).
- Quy tắc và hành động sửa: Theo quy tắc instance segmentation trong `guideline-mini-sheet.md`, "Hai vật cùng lớp sát nhau vẫn là hai instance riêng biệt; cùng class không đồng nghĩa cùng instance". Tôi đã dùng công cụ tẩy (Eraser) để tách đôi ranh giới tiếp xúc giữa hai người, sau đó tạo một shape `person` mới cho người thứ hai, vẽ lại khít theo từng đường biên cơ thể của từng người.
- Sau sửa đã Save và export lại chưa? Tôi đã bấm Save trên giao diện CVAT, sau đó thực hiện Export job dataset ra file `medium_instance.zip` (định dạng COCO 1.0) và lưu vào thư mục `submissions/`.

Nếu bạn **đã xem Summary tự đánh giá trên GitHub Actions hoặc tự chạy script**, ghi ngắn một kết quả liên quan lỗi vừa sửa (ví dụ task, metric trước/sau nếu có): Sau khi tách đúng instance và export lại, tôi chạy script tự đánh giá `scoring/scorecard.py --group tiers`: task `medium_instance` đạt metric matched IoU × recall = 0.689, quy đổi đạt 20.5 / 32 điểm. Scorecard ba tier tối đa **82**, không phải điểm cuối trên 100. Không tự ghi PASS/top 3/bonus; người phụ trách xác nhận theo tiêu chí lớp. Không đưa file ground truth vào fork.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1. `easy_semantic`: ảnh `7ee6d192-89e2408b.jpg`, dải lề đường mép phải | Có thể hiểu là `road` (vì bề mặt rải nhựa đồng màu xám với lòng đường) hoặc hiểu là `sidewalk` (lối đi bộ bên lề đường). | Quy tắc trong `guideline-mini-sheet.md`: "Ranh road–sidewalk xác định theo chức năng và bó vỉa, không chỉ theo màu". Tôi thấy có gờ bó vỉa (curb) rõ nét ngăn cách phần xe chạy và phần hè phía trên. | Tôi quyết định kẻ ranh giới bám sát theo mép gờ bó vỉa: phần lòng đường xe chạy gán `road`, còn phần trên gờ bó vỉa gán `sidewalk`, không để màu nhựa đường đánh lừa. |
| 2. `medium_instance`: ảnh `000000458325.jpg`, ô tô đỗ gối đầu lề trái | Có thể coi chiếc ô tô bị che khuất quá nhiều không thể gán nhãn chính xác (bỏ qua), hoặc phải đánh dấu một instance `car` riêng. | Quy tắc gán nhãn: mọi vật thuộc schema nhìn thấy được đều phải vẽ, và chỉ vẽ sát phần nhìn thấy thực tế, không vẽ phần bị che. Chiếc xe dù bị che khuất hơn một nửa thân nhưng nắp capo và góc kính trước vẫn nhận diện rõ ràng. | Tôi quyết định vẽ một instance `car` riêng cho chiếc xe này, dừng mask chính xác tại mép xe phía trước đang che khuất nó. |
| 3. `hard_panoptic`: ảnh `000000460147.jpg`, phương tiện khối hộp lớn ở làn giữa xa | Có thể hiểu là `bus` (xe khách đường dài) hoặc hiểu là `truck` (xe tải thùng kín chở hàng). | Quy tắc phân loại trong schema Hard: `bus` và `truck` là hai thing class khác nhau. Khi zoom lớn quan sát, xe có phần đầu cabin tách rời với khối thùng hàng kín phía sau, không có hàng kính sổ chở khách. | Tôi quyết định gán nhãn là `truck` (thing instance riêng), đồng thời xử lý kỹ phần đáy xe tiếp giáp với stuff `road` để tránh chồng lấn nhãn. |
