# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Nguyễn Quang Thức
- MSSV: 2A202603116
- Email: nguyenquangthuc.info@gmail.com
- Lớp: L2A
- Link repo bài làm: https://github.com/ThucNguyen-97/K5-L2A-Day02-2A202603116-NguyenQuangThuc-ObjectDetection
- Link Colab đã chạy: Không áp dụng — phần xử lý hiện tại được chạy local trên máy cá nhân.

## 2. Schema và mini guideline

Schema YOLO được sử dụng:

```text
0 = car
1 = truck
2 = bus
3 = van
```

Guideline chi tiết nằm tại [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Ba quyết định mơ hồ chính:

1. `drive_001.jpg`: hai xe car bị cột che. Gán box riêng quanh phần nhìn thấy vì vẫn đủ đặc điểm nhận diện; không đoán phần bị che.
2. `drive_003.jpg`: các xe ở trung tâm bị chồng lấn. Gán một box cho từng xe có thể nhận diện, không gộp thành box nhóm.
3. `drive_004.jpg`: xe bị cắt ở mép ảnh trong điều kiện thiếu sáng. Gán box quanh phần nhìn thấy và để box dừng đúng tại mép ảnh nếu còn đủ bằng chứng nhận diện.

## 3. Annotation và self-QC

- Số file nhãn train: 50 file, từ `drive_001.txt` đến `drive_050.txt`.
- Ảnh train: 50 ảnh; ảnh eval: 10 ảnh.
- Kết quả checker: `ĐẠT cấu trúc: 50/50 file nhãn hợp lệ.`
- Các ảnh đã rà/đối chiếu trong quá trình self-QC và rework: `drive_001`, `drive_003`, `drive_004`, `drive_007`, `drive_009`, `drive_018`, `drive_024`, `drive_027`, `drive_041`, `drive_049`.
- Lỗi đã xử lý: xóa box thừa, bổ sung box bị bỏ sót, chỉnh class và remap class ID từ thứ tự export cũ sang schema chuẩn.
- Commit khóa nhãn ban đầu: `f60cf1b Add 50 remapped YOLO training labels`.
- Công cụ annotation: CVAT; các nhãn sau khi export được kiểm tra lại trước khi đưa vào repo.

## 4. Gold QC và rework

Gold labels được dùng riêng để đánh giá sau khi khóa nhãn; không đưa gold labels hoặc file ZIP gold vào repo.

| Chỉ số | Trước sửa | Sau sửa |
|---|---:|---:|
| TP | 453 | 469 |
| FP | 520 | 183 |
| FN | 200 | 184 |
| Precision | 0.4656 | 0.7193 |
| Recall | 0.6937 | 0.7182 |
| F1 | 0.5572 | 0.7188 |
| mean_iou_of_matches | 0.8544 | 0.8523 |
| passed | false | false |

Kết quả sau rework được lưu tại `outputs/annotation_evaluation.json`. Mean IoU đạt ngưỡng yêu cầu, còn F1 chưa đạt ngưỡng `0.80`. Các lỗi chính sau đối chiếu là box thừa, bỏ sót xe nhỏ/xe bị che và một số box chưa khớp tốt với gold. Không sửa gold labels.

## 5. Kết quả YOLO

- Runtime: Windows local, Python 3.14.4, CPU Intel Core i5-1135G7.
- PyTorch: `2.11.0+cpu`.
- Ultralytics: `8.4.208`.
- Mô hình: `yolo26n.pt`, train 50 epochs, `imgsz=640`, batch tự động, seed `20260909`.
- Link Colab: không áp dụng — đã chạy notebook tương đương local trên máy cá nhân.
- Best checkpoint: tạo trong `outputs/yolo26_runs/vehicle_annotation/weights/best.pt` và không đưa vào Git.

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.4892 |
| Recall | 0.2530 |
| mAP50 | 0.2779 |
| mAP50–95 | 0.2060 |

Model đã chạy đủ trên 50 ảnh train và đánh giá trên 10 ảnh eval. Ảnh dự đoán được đặt tại `assets/eval_predictions/visuals/`; log train và biểu đồ được đặt tại `assets/yolo26_runs/`. Đây là kết quả minh họa trên bộ dữ liệu nhỏ, không dùng để kết luận chất lượng sản phẩm.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp có recall thấp nhất là `bus` trong lần đánh giá này; cần xem thêm các ảnh eval có xe bus để phân biệt lỗi bỏ sót và mất cân bằng lớp.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm tra cạnh box và độ khít của box; box có thể đúng vật thể nhưng lệch vị trí hoặc kích thước.
3. Lỗi class có thể liên quan guideline ở các trường hợp truck/bus/van bị che hoặc nhìn từ xa. Dữ kiện hỗ trợ là các quyết định mơ hồ trong `GUIDELINE_MINI.md` và kết quả per-class của model.
4. Không dùng 10 ảnh eval để train vì eval cần giữ độc lập để đo khả năng tổng quát hóa; đưa chúng vào train sẽ làm kết quả đánh giá bị rò rỉ.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Export CVAT | File YOLO dùng thứ tự class cũ `truck, bus, van, car` | Remap `0→1`, `1→2`, `2→3`, `3→0` | Repo dùng đúng schema `car, truck, bus, van` |
| Import gold vào CVAT | Gói gold thiếu `data.yaml` và thư mục ảnh train | Tạo gói import tạm có `data.yaml`, ảnh và nhãn; giữ nguyên nội dung 50 file gold | Xem được gold trong task `gold-review`; không đưa gold vào repo |
| Gold QC | F1 sau rework thấp hơn ngưỡng yêu cầu | Rà các ảnh có nhiều FP/FN, xóa box thừa và bổ sung box bỏ sót | F1 sau rework `0.7188`; phần train/eval model đã được thực hiện local và ghi ở mục 5 |
| Kiểm tra repo | Windows không in được tiếng Việt khi chạy Python mặc định | Dùng `$env:PYTHONIOENCODING="utf-8"; py -3 ...` | Checker chạy đạt 50/50 |

## 7. Tự đánh giá theo rubric

- Đã có 50 file nhãn YOLO trong `dataset/labels/train/`.
- Đã có mini guideline và các quyết định mơ hồ.
- Đã có commit khóa nhãn ban đầu và kết quả gold QC trước/sau rework.
- Đã đạt mean IoU `0.8523`, nhưng F1 `0.7188` chưa đạt ngưỡng `0.80` theo GUIDE.
- Đã train local và đánh giá đủ 10 ảnh eval; các chỉ số model đã ghi ở mục 5.
- Không commit gold train, file ZIP gold, mật khẩu, token hoặc checkpoint `.pt`.

## 8. Công cụ AI và tài liệu tham khảo

- ChatGPT/Codex: hỗ trợ đọc hướng dẫn, giải thích CVAT, kiểm tra cấu trúc, chuẩn bị lệnh và rà soát kết quả; các quyết định annotation được kiểm tra lại trong CVAT.
- Tài liệu tham khảo chính: `GUIDE.md`, `CHECKPOINTS.md`, `SUBMISSION.md`, `RULES.md` trong repo và tài liệu CVAT.
