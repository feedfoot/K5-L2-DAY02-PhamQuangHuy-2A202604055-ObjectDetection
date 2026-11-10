# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` |Xe con chở người cỡ nhỏ: sedan, hatchback, SUV, taxi, xe bán tải/cabin nhỏ. Thân thấp và thon, có capo rõ|Taxi vàng, sedan trắng/bạc/đen, SUV đen. Thấp và dài hơn `van`|
| `truck` |Xe tải chở hàng có cabin tách khỏi thùng hàng, hoặc xe đầu kéo|Xe tải thùng kín màu tím chạy cạnh xe buýt. Có thùng hàng phía sau cabin|
| `bus` |Xe buýt và minibus chở khách: thân rất dài, nhiều cửa sổ, nhiều cửa lên xuống|Xe buýt đỏ-trắng, xe buýt vàng, xe buýt trắng chạy tuyến. Dài hơn hẳn các xe xung quanh|
| `van` |Xe van/minivan chở hàng hoặc chở người: thân vuông, cao, ngắn hơn bus, thường không có capo dài|Xe van trắng và kem. Cao và vuông hơn car, nhỏ hơn nhiều so với bus|

## Quy tắc chung

- Box bám sát vật thể nghĩa là:bốn cạnh box chạm bốn điểm ngoài cùng của phần thân xe nhìn thấy được (đầu xe, đuôi xe, nóc xe, đáy bánh xe); không lấy thêm mặt đường, xe bên cạnh. Mỗi xe một box riêng, hộp thẳng.
- Vật thể bị che khuất:vẫn gán box quanh phần nhìn thấy được nếu nhận ra được lớp; nếu chỉ còn mảnh nhỏ không xác định được lớp thì không gán. Hai xe chồng nhau thì vẽ hai box riêng trên phần nhìn thấy của từng xe.
- Vật thể bị cắt bởi rìa ảnh:box chạm đúng rìa ảnh, không đoán phần nằm ngoài ảnh.
- Vật thể rất nhỏ/mơ hồ:chỉ gán khi nhìn thấy hình khối xe (thân, kính, màu). Nếu chỉ là đốm sáng hoặc vệt màu thì không gán. Khi mơ hồ giữa `car` và `van` thì không tự chọn bừa, ghi lại ở bảng dưới và hỏi giảng viên.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_031` |Ngã tư đông xe: xe trắng nằm sau sedan bên trái, ảnh mờ, thân vuông, không phân biệt được `car` hay `van` |Không tự chọn lớp; hỏi giảng viên. Chỉ gán khi có câu trả lời hoặc khi nhìn rõ hơn|Schema yêu cầu ca van/minivan mơ hồ với car thì ghi lại và hỏi, không đoán|
| `drive_002` |	Dãy xe nhỏ ở xa chen sát nhau, rất mờ|Chỉ gán xe nhận ra được hình khối khi nhìn cả cụm; bỏ qua xe chỉ còn là vệt màu. Không gộp hai xe vào một box|Quy tắc "mỗi xe một box" và không đoán khi không xác định chắc lớp|
| `drive_004` |Ảnh ban đêm: phía xa chỉ thấy các đốm đèn pha chói, và có xe máy có người ngồi lẫn giữa các xe|Không gán các đốm đèn. Không gán xe máy. Chỉ gán xe nhìn rõ thân xe (xe van màu kem)|Đốm sáng không đủ để xác định lớp; xe máy nằm ngoài bốn lớp của schema|
| `drive_020` |Xe tối phía sau cầu thang và lan can, bị che một phần|Gán box quanh phần xe nhìn thấy nếu thấy rõ đó là xe; nếu chỉ thấy lan can thì không gán|Vật thể bị che một phần vẫn cần box nếu phần nhìn thấy đủ để nhận diện lớp|
| `drive_009` | Xe tải thùng kín màu tím chạy sát nhiều xe buýt, dễ nhầm giữa truck và bus/van| Gán `truck` cho xe tải thùng kín; gán `bus` cho các xe buýt dài nhiều cửa; gán van cho hai xe van nhỏ ở trên cùng | Truck có cabin tách khỏi thùng hàng; bus rất dài, nhiều cửa; van thân vuông, ngắn hơn bus |
| `drive_047` | Chiếc xe bạc ở mép trái bị cắt mất phần đầu, đi gần chiếc SUV trắng và một xe tải thùng kín màu trắng bên phải. | Gán `van` cho xe bạc (cạnh trái chạm mép ảnh), `car` cho chiếc SUV trắng và `truck` cho xe tải thùng kín. | Xe bạc vẫn lộ rõ thân hộp cao liền khối và cửa lùa đặc trưng của `van`; SUV có đầu ca-pô thuộc car, còn xe có cabin rời thùng hàng vuông phía sau thuộc `truck`. |