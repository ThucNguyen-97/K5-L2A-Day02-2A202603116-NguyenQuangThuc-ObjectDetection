# Mini guideline — bài làm cá nhân

## Định nghĩa lớp

| Lớp | Định nghĩa | Ví dụ/điểm phân biệt |
|---|---|---|
| car | Xe con, sedan, SUV, taxi hoặc xe bán tải | Không phải xe tải, xe buýt hoặc xe van thân hộp rõ |
| truck | Xe tải chở hàng, xe ben hoặc xe đầu kéo | Có thùng, ben hoặc khoang chở hàng tách biệt |
| bus | Xe buýt hoặc minibus chở khách | Thân xe dài, thường có nhiều cửa sổ |
| van | Xe van hoặc minivan chở người hoặc hàng | Thân hộp nhỏ, không phải xe buýt lớn |

## Quy tắc chung

Mỗi xe hợp lệ được gán một bounding box riêng. Không gộp nhiều xe vào cùng một box.

Box bám sát phần thân xe nhìn thấy, không lấy thêm mặt đường, bầu trời hoặc xe bên cạnh.

Xe đang đỗ vẫn được gán nhãn theo loại xe.

Không gán người đi bộ, xe đạp, mô tô/xe máy, biển báo, đèn giao thông hoặc hình xe trong quảng cáo.

## Vật thể bị che khuất

Nếu xe bị xe khác, cây, cột hoặc vật thể khác che một phần nhưng vẫn nhận diện được lớp, tôi vẫn gán một box quanh phần xe nhìn thấy.

Tôi không đoán phần xe bị che khuất.

Nếu phần xe còn lại quá nhỏ hoặc không đủ để xác định lớp, tôi không tự đoán mà ghi lại để hỏi giảng viên.

## Vật thể bị cắt bởi rìa ảnh

Nếu xe bị mép ảnh cắt nhưng còn đủ bằng chứng để xác định lớp, tôi vẫn gán nhãn.

Bounding box dừng đúng tại mép ảnh. Tôi không mở rộng box ra ngoài ảnh để đoán phần xe không nhìn thấy.

## Vật thể rất nhỏ hoặc mơ hồ

Nếu xe quá nhỏ, quá mờ hoặc không đủ đặc điểm để phân biệt car, truck, bus và van, tôi không tự gán nhãn.

Tôi ghi lại tên ảnh và lý do để hỏi giảng viên thay vì chọn lớp tùy ý.

## Các quyết định cho ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
|---|---|---|---|
| drive_001.jpg | Xe car 13 và car 6 bị một cột che | Gán một box riêng quanh phần nhìn thấy của từng xe và chọn lớp car | Phần xe còn lại vẫn đủ đặc điểm để nhận diện là car |


