# Báo cáo Day 5 — điền trực tiếp trong fork của bạn

**Cách dùng:** Thay mọi dấu `…` bằng bài làm thật của bạn trước khi nộp link fork trên VLearn. Giữ nguyên bốn mục và bảng để coach đọc nhanh. Viết ngắn, cụ thể theo ảnh/vùng; không cần thuật ngữ chuyên sâu. Ví dụ trong [hướng dẫn mẫu](reports/REPORT_TEMPLATE.md) chỉ giúp hiểu cách điền, không phải câu trả lời để chép lại.

- Mã học viên theo lớp: 2A202602101
- Ngày / CVAT local: 2026-09-17 / CVAT v2.24+ (Local Docker & Google Colab A100 GPU)
- Công cụ đã dùng: CVAT 2.24+, PyTorch, Mask2Former (Swin-Large Cityscapes Semantic & COCO Panoptic), YOLO11x-seg, OpenCV, PyCOCOtools

Mã học viên là mã lớp cấp; không cần ghi họ tên trong report nếu kênh VLearn đã nhận diện bạn. Chỉ ghi công cụ thật sự đã dùng; không có SAM vẫn làm bài bình thường.

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

Nếu export lỗi, ghi task, dữ liệu đã Save đến đâu và lỗi đã báo coach.

## 2. Một quyết định trước khi dùng gợi ý

Chọn object đầu tiên bạn tự vẽ ở `medium_instance`, trước khi xem bất kỳ đề xuất tự động nào cho object đó. Ghi ảnh/vị trí đủ để tìm lại; “quy tắc biên” là lý do bạn chọn hoặc dừng mask ở ranh đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh `000000037777.jpg`, chiếc xe hơi màu trắng (`car`) ở tiền cảnh phía bên trái lòng đường.
- Class và quy tắc tôi dùng để chọn biên: Gán nhãn `car`. Ranh giới bao kín toàn bộ thân vỏ xe, bánh xe tiếp giáp với mặt đường và gương chiếu hậu; không để hở viền ngoài và không ăn lấn sang mặt đường.
- Nếu dùng gợi ý sau đó: vùng gợi ý sai/đúng, hành động sửa/giữ và lý do: Model phân đoạn thân xe rất chuẩn xác, tuy nhiên phần mép gương chiếu hậu hơi mỏng bị khuyết một góc nhỏ; tôi đã dùng công cụ Brush (cọ vẽ) tô dặm bù thêm phần gương để mask thể hiện trọn vẹn hình dáng thực của xe.
- Nếu không dùng gợi ý: không áp dụng.

## 3. Một lỗi tôi tìm thấy và sửa

Chọn một lỗi **có thật** trong bài. Nếu công cụ lỗi khiến bạn chưa sửa được, ghi rõ đã thử gì và cần coach hỗ trợ gì; không ghi “đã sửa” khi chưa sửa.

- Task/ảnh/vùng: Task `cp1_holes`, ảnh `000000144300.jpg`, ô tô có phần kính trong suốt nhìn xuyên qua phía sau.
- Lỗi thuộc loại: sai lớp / thiếu-thừa vật / gộp-tách / biên / phủ vùng / khác: khác (rỗng ruột / lỗ thủng kính xe trong mask).
- Bằng chứng tôi nhìn thấy: Khi model phân tích đối tượng xe, do kính xe trong suốt nhìn thấu cảnh vật bên kia nên thuật toán segmentation ban đầu tạo ra lỗ thủng (hole) bên trong mask thân xe.
- Quy tắc và hành động sửa: Theo guideline Day 5 và yêu cầu cụ thể của checkpoint `cp1_holes`, kính xe và khe hở bên trong thân xe bắt buộc phải thuộc về mask xe (fill kín lỗ). Đã dùng thuật toán trích xuất đường viền ngoài (external contour) và vẽ lấp đầy toàn bộ vùng rỗng bên trong thân xe thành một khối liền khối.
- Sau sửa đã Save và export lại chưa? Đã Save và export ra file `cp1_holes.zip`.

Nếu bạn **đã xem Summary tự đánh giá trên GitHub Actions hoặc tự chạy script**, ghi ngắn một kết quả liên quan lỗi vừa sửa (ví dụ task, metric trước/sau nếu có): Chạy `scripts/inspect_submissions.py` đạt `[OK] cp1_holes: cp1_holes.zip` với 6 mask RLE hợp lệ.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1. `cp4_curb` (ảnh `7d83710e-4697c3b2.jpg`) | Phân định ranh giới giữa `road` và `sidewalk` tại khu vực gờ bó vỉa chìm và vũng nước mép đường. | Task chỉ có đúng 2 class `road` và `sidewalk`. Mặt đường bao gồm cả lòng đường và các phương tiện di chuyển trên đường. | Quyết định: Cắt ranh giới chính xác theo mép gờ bó vỉa; toàn bộ lòng đường và các vật thể trên mặt đường được gộp trọn vẹn vào nhãn `road`. |
| 2. `cp5_occlusion` (ảnh `000000336232.jpg`) | Một chiếc xe bị cột biển báo che cắt ngang thân xe, tạo thành hai mảng mask tách rời. | Theo quy tắc Instance Segmentation, vật thể bị che khuất (Occlusion) dù đứt đoạn vẫn là một thực thể duy nhất. | Quyết định: Giữ cả hai mảng mặt nạ xe dưới cùng 1 ID annotation (1 instance duy nhất với multipolygon/RLE mask), không tách thành 2 xe độc lập. |
| 3. `cp3_thin` (ảnh `839f7736-abe28069.jpg`) | Cột đèn mảnh và biển báo trên nền cây cối và công trình xây dựng. | Bộ nhãn task này chỉ có đúng 4 nhãn (`pole`, `traffic sign`, `sky`, `road`), không có nhãn cây hay nhà hay background. | Quyết định: Giữ độ sắc nét cho cột và biển báo; toàn bộ cây cối hay công trình nền phía sau được hợp nhất vào nền `road` theo quy ước triệt tiêu background của bài. |
