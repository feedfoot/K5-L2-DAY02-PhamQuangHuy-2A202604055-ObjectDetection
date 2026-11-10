# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên:Phạm Quang Huy
- MSSV:2A202604055
- Email:huyde1357@gmail.com
- Lớp:E403
- Link repo bài làm:https://github.com/feedfoot/K5-L2-DAY02-PhamQuangHuy-2A202604055-ObjectDetection.git
- Link Colab đã chạy:https://colab.research.google.com/github/feedfoot/K5-L2-DAY02-PhamQuangHuy-2A202604055-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb

## 2. Schema và mini guideline

Chi tiết ở GUIDELINE_MINI.md. Schema 4 lớp: car = 0, truck = 1, bus = 2, van = 3. Mỗi xe một box thẳng, box bám sát phần thân xe nhìn thấy; xe đỗ vẫn gán; xe bị che thì gán phần nhìn thấy; xe bị cắt khung thì box chạm rìa ảnh; không gán xe máy, người đi bộ, biển báo.

Các ca mơ hồ đã quyết định:

drive_031: xe trắng phía sau sedan, ảnh mờ, không phân biệt được car hay van. Quyết định: không tự chọn lớp, ghi lại để hỏi giảng viên.
drive_004: ảnh ban đêm, phía xa chỉ thấy đốm đèn pha, có xe máy lẫn trong dòng xe. Quyết định: không gán đốm đèn và xe máy, chỉ gán xe thấy rõ thân xe.
drive_002: cụm xe nhỏ ở xa, rất mờ. Quyết định: chỉ gán xe nhận ra hình khối, không gộp hai xe vào một box.
drive_020: xe bị cầu thang và lan can che một phần. Quyết định: gán phần nhìn thấy nếu nhận ra là xe.
drive_009: xe tải thùng kín chạy sát nhiều xe buýt. Quyết định: truck cho xe tải thùng kín, bus cho xe buýt dài nhiều cửa, van cho xe van nhỏ thân vuông.

Cần giảng viên làm rõ: (1) ranh giới car/van ở ca drive_031; (2) quy tắc kích thước tối thiểu của xe ở xa, vì kết quả chấm gold (mục 4) gợi ý gold không tính nhiều xe nhỏ mà mình đã gán.

## 3. Annotation và self-QC

-Số file nhãn train: 50 (đủ drive_001 đến drive_050, không file rỗng). Tổng 1221 box: car 1027, bus 112, van 62, truck 20.
-Kết quả checker và lỗi đã xử lý: python tools/check_yolo_labels.py báo "ĐẠT cấu trúc: 50/50 file nhãn hợp lệ". Mình cũng tự kiểm tra thêm: mỗi dòng đủ 5 cột, class ID chỉ từ 0 đến 3, không box nào vượt biên ảnh. Không có lỗi cấu trúc cần xử lý.
-Danh sách ảnh đã rà bằng mắt: drive_002, drive_004, drive_009, drive_020, drive_031, drive_035, drive_037, drive_038, drive_40, drive_050.
-Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: khi rà các frame trong CVAT, mình phát hiện các loại lỗi sau: một số xe có box còn lỏng (dư nền), một số xe chưa có box hoặc box chồng nhau, một box bị xoay nhầm (do kéo nhầm tay cầm xoay), và vài chỗ xe nhỏ ở xa khó xác định. Cách sửa: kéo lại cạnh box, vẽ lại bằng hộp thẳng, bỏ box thừa.
-Commit khóa nhãn ban đầu: 941baaebd5a2d69c5864db3d1a9491b0aef77960

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP |487 | |
| FP |734 | |
| FN |166 | |
| Precision 0.3989 | |
| Recall |0.7458 | |
| F1 |0.5197 | |
| mean_iou_of_matches |0.8283 | |
| passed |pass | |

Dẫn tới `outputs/annotation_evaluation.json` trong repo. Nếu rework, ghi ảnh, lỗi đã kiểm, quyết định sửa/giữ và lý do. Không suy nguyên nhân chỉ từ FP/FN. Không có rework thì ghi rõ.
Nhận xét: mean IoU của các box đã ghép là 0.8283, vượt ngưỡng 0.70, nên box mình vẽ khá sát. F1 = 0.5197 thấp hơn ngưỡng 0.80 của GUIDE, chủ yếu do precision thấp (0.3989). Mình vẽ 1221 box (TP + FP), trong khi gold có khoảng 653 box (TP + FN). Dữ kiện đi kèm: trong nhãn của mình có 733 box có diện tích nhỏ hơn 0.5% diện tích ảnh, gần bằng số FP (734). Đây chỉ là giả thuyết (gold không tính các xe nhỏ ở xa), mình chưa kiểm chứng bằng cách sửa nhãn rồi chạy lại, nên không kết luận nguyên nhân chắc chắn chỉ từ FP/FN. Recall 0.7458 cho thấy còn 166 xe trong gold mà mình bỏ sót hoặc gán sai lớp, mình chưa rà chi tiết phần này.

Rework: mình không thực hiện rework. Theo trao đổi với mentor trong nhóm, ảnh gold có một số ảnh bị lỗi nên mức F1 trên 0.5 được coi là chấp nhận, vì vậy mình đã chuyển sang bước train
## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch:Tesla T4 (14913 MiB), Ultralytics 8.4.208, Torch 2.14.1+cu130, Python 3.13.16
- Các tham số thay đổi so với notebook nếu có và lý do:không thay đổi (yolo26n.pt, epochs=50, imgsz=640, batch=-1, seed=20260909).
- Đường dẫn best checkpoint trong phiên chạy:/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision |0.409 |
| Recall |0.234 |
| mAP50 |0.271 |
| mAP50–95 |0.199 |

Đặt cả 2 folder `assets/eval_predictions/` và `assets/yolo26_runs/` (giải nén từ `day2_colab_outputs.zip`) cạnh báo cáo và dẫn link tương đối. Không chỉ ghi số; chỉ ra ít nhất một ảnh và vật thể cụ thể.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
Lớp bus có recall thấp nhất (recall = 0, precision = 0, mAP50 = 0.014), tiếp theo là van (recall 0.083) và truck (recall 0.2). Lớp car có recall cao nhất (0.653). Tập eval chỉ có 2 xe bus, 5 xe truck và 12 xe van, nên các con số này rất nhiễu và không đủ để kết luận chắc. Ở drive_053, model đoán xe buýt lớn là bus 0.86 nhưng box rộng hơn thân xe, nên IoU có thể thấp hơn 0.5 và bị tính là chưa khớp.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
## Câu hỏi phân tích để nộp

1. Lớp nào có recall thấp nhất? Chỉ ra một ảnh eval minh họa.
Lớp bus có recall thấp nhất (recall = 0, precision = 0, mAP50 = 0.014), tiếp theo là van (recall 0.083) và truck (recall 0.2). Lớp car có recall cao nhất (0.653). Tập eval chỉ có 2 xe bus, 5 xe truck và 12 xe van, nên các con số này rất nhiễu và không đủ để kết luận chắc. Ở drive_053, model đoán xe buýt lớn là bus 0.86 nhưng box rộng hơn thân xe, nên IoU có thể thấp hơn 0.5 và bị tính là chưa khớp

2. Nếu mAP50 cao nhưng mAP50-95 thấp, box đang gặp vấn đề gì?
Điều đó cho thấy box đúng vị trí tương đối (IoU ≥ 0.5) nhưng chưa khít với vật thể khi yêu cầu IoU cao hơn (đến 0.95). Trong kết quả của mình, lớp car có mAP50 = 0.741 nhưng mAP50-95 chỉ 0.524, nên box xe con đúng chỗ nhưng chưa sát. Với toàn bộ lớp, mAP50 chỉ 0.271 và mAP50-95 là 0.199, tức mAP50 chưa cao, nên mình chỉ áp dụng nhận xét này cho lớp car. Trên ảnh dự đoán drive_053, box của xe buýt lớn cũng rộng hơn thân xe. Mình thấy box của mình ở bước gán nhãn khá sát (mean IoU với gold là 0.828), nên phần lỏng chủ yếu đến từ phía model, với tập huấn luyện chỉ 50 ảnh và 50 epoch.
3. Có lỗi `truck`/`bus`/`van`/`car` nào liên quan tới guideline không?
Có. Ở drive_051 và drive_054, chiếc sedan đỏ lớn gần camera bị model đoán là van với độ tin cậy 0.99 và 0.97. Theo schema, car gồm sedan, SUV, taxi, còn van là xe thân vuông cao hơn, nên ranh giới car/van là ca mơ hồ mà mình đã ghi vào GUIDELINE_MINI.md (ví dụ ca xe trắng ở drive_031, mình không tự chọn lớp mà ghi lại để hỏi). Nguyên nhân có thể còn đến từ dữ liệu train mất cân bằng: 1027 car so với 112 bus, 62 van, 20 truck, nên model học các lớp ít mẫu kém và có xu hướng đoán lệch. Mình chưa thể kết luận nhãn sai, vì tập eval nhỏ và mAP thấp không tự chứng minh nhãn có lỗi.
4. Vì sao không dùng 10 ảnh eval để train dù chúng có nhãn?
Tập eval cần là dữ liệu model chưa từng thấy để đo khả năng tổng quát hóa. Nếu đưa vào train, model có thể nhớ chính các ảnh đó, mAP sẽ cao giả tạo (rò rỉ dữ liệu) và không so sánh được với các lần chạy khác.Trong notebook này, 10 ảnh eval cũng được dùng làm validation để chọn best.pt, nên chúng không phải test set độc lập hoàn toàn

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
|Đẩy nhãn lên GitHub |fatal: not a git repository vì repo đã tải bằng Download ZIP |Clone lại fork bằng GitHub Desktop, chép nhãn và GUIDELINE_MINI.md sang bản clone |	Đã commit và push thành công |
|Vẽ box trong CVAT|Box bị xoay nghiêng do kéo nhầm tay cầm xoay|Xóa và vẽ lại bằng hộp thẳng (phím N)|Đã sửa|
|Chấm nhãn với gold|Script báo "Không có gold labels"|Thư mục gold bị lồng một cấp (gold_train_labels\gold_train_labels), trỏ --gold-labels vào thư mục con|Chạy được, có kết quả ở mục 4|
|phân biệt vật thể nhỏ ở xa|vật thể nhỏ ở xa|phỏng đoán cá nhân|có đúng nhưng cũng có sai nhưng mô hình nhận diện k tốt|
## 7. Tự đánh giá theo rubric
Hoàn thành và định dạng YOLO: đã có, checker 50/50 đạt.
Đầy đủ vật thể: chưa kiểm chắc; recall với gold là 0.7458, còn 166 xe bỏ sót hoặc sai lớp.
Độ bám sát box: tốt, mean IoU với gold là 0.8283. Nhưng mình vẽ quá nhiều box nhỏ (precision 0.3989).
Phân lớp nhất quán: có ghi guideline, nhưng chưa kiểm được lỗi car/van trong nhãn train; dự đoán model còn nhầm car thành van ở một số ảnh eval.
Mini guideline: đã có định nghĩa 4 lớp, quy tắc box và 5 ca mơ hồ.
Gold-set annotation QC: chưa đạt ngưỡng chính thức (F1 0.5197 so với 0.80; mean IoU đạt). Mình không rework theo trao đổi với mentor nhóm, và nêu rõ ở mục 4.
Pipeline YOLO: Colab chạy được, không dùng eval để train, đã có metrics và ảnh dự đoán; phân tích còn phụ thuộc eval chỉ có 10 ảnh.

## 8. Công cụ AI và tài liệu tham khảo

-claude.
-Tài liệu ngoài: tài liệu của chính repo bài lab (README, GUIDE, RULES, RUBRIC, SUBMISSION, CHECKPOINTS). Ảnh dữ liệu theo ghi chú của repo có nguồn gốc UA-DETRAC (CC-BY-4.0), đã biên tập cho giảng dạy.
