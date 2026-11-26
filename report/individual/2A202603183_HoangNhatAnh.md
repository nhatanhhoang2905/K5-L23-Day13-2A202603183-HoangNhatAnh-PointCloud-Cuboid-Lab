# Báo cáo Day 13 — Hoàng Nhật Anh, 2A202603183

## 1. Kết luận trước
MSSV: **2A202603183**. Task cá nhân: **D13-2A202603183**. Hệ điều hành: **Windows**. Trình duyệt: **Google Chrome**. CVAT: **Server 2.74.1, UI 2.74.1**.

Mười frame được giao: `s01f08`, `s01f31`, `s02f17`, `s02f32`, `s03f18`, `s03f46`, `s04f15`, `s04f46`, `s05f07`, `s05f41`.

Bảng A hiện có **65 cuboid**, trong đó **61 cuboid có tâm trong 50 m** và 4 cuboid ngoài phạm vi chấm. Ba frame ít hộp nhất là `s01f31`, `s04f15`, `s04f46`, mỗi frame 5 hộp; chưa có ghi chép riêng giải thích số lượng ở các frame này. Phiếu tự kiểm còn **1/65 cuboid có cảnh báo**. Ba vật mẫu đã được kiểm hướng, đáy và các view; phần còn cần xem lại là kích thước Bus, loại xe của Cyclist và các vật rất xa. Đã dùng công cụ đối chiếu hai số trong bài của MSSV **2A202603192**, cả hai đều khớp với Bảng A bạn ấy gửi. Chưa có phản hồi bạn ấy kiểm bài của tôi và chưa xác nhận đổi kiểm trực tiếp trên màn hình.

Cách dùng CVAT:

- Trong khung 3D, kéo chuột trái để xoay, kéo chuột phải để dời và dùng con lăn để phóng to/thu nhỏ; xoay khung lớn để nhìn toàn cảnh từ trên xuống.
- Chọn label và Shape, nháy đúp để đặt cuboid. Chỉnh tâm, dài, rộng và yaw ở Top; chỉnh chiều cao và đáy ở Side/Front, không xoay hộp ở hai view này.
- Bật Appearance → Cuboid orientation để kiểm hướng; sau khi Save và xuất Bảng A, dùng `python scripts/lab.py preview` để xem hộp trên các ảnh camera.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ: `s01f08`, `s01f31`. Ảnh: `t1-s01f08-3d.png`, `t1-s01f08-top.png`, `t1-s01f31-3d.png`, `t1-s01f31-top.png`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f08 | Phía trước (−y) sang phải; +x lên trên; +y sang trái. | Đường thẳng, mặt đường tương đối phẳng; chưa đo trực tiếp độ cao so với LiDAR. | Cuboid xa nhất đã gán: 75,47 m, ngoài phạm vi chấm. | Car | Có Car phía sau và ở cả hai bên xe thu dữ liệu. | Hiếm gặp cụm điểm không nhận dạng được. |
| s01f31 | Phía trước (−y) sang phải; +x lên trên; +y sang trái. | Đường thẳng, mặt đường tương đối phẳng; chưa đo trực tiếp độ cao so với LiDAR. | Cuboid xa nhất đã gán: 75,37 m, ngoài phạm vi chấm. | Car | Có Car phía sau và ở cả hai bên xe thu dữ liệu. | Có cụm điểm chưa nhận dạng được vật, ghi lại trong ảnh `t4-4-diem-la.png`. |

Khoảng cách trong bảng lấy từ tâm cuboid đã gán trong Bảng A; chưa có phép đo độc lập xác nhận vật xa nhất còn đọc được của toàn cảnh.

Khi đối chiếu hai frame trên, ảnh camera giúp nhận biết màu xe, các chi tiết để phân biệt loại xe và đầu/đuôi xe. Point cloud LiDAR cung cấp vị trí của xe trong không gian 3D, khoảng cách theo mét và hình dạng/kích thước theo mét.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f17 | Bên phải ảnh (−y). | Chưa có ghi chép riêng. |
| s02f32 | Bên phải ảnh (−y). | Chưa có ghi chép riêng. |
| s03f18 | Bên phải ảnh (−y). | Chưa có ghi chép riêng. |
| s03f46 | Bên phải ảnh (−y). | Chưa có ghi chép riêng. |
| s04f15 | Bên phải ảnh (−y). | Chưa có ghi chép riêng. |
| s04f46 | Bên phải ảnh (−y). | Chưa có ghi chép riêng. |
| s05f07 | Bên phải ảnh (−y). | Chưa có ghi chép riêng. |
| s05f41 | Bên phải ảnh (−y). | Chưa có ghi chép riêng. |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car.png`, `t2-b-bus-truck.png`, `t2-c-ped-cyclist.png`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s03f46, #1 | Car | Đúng hướng | Có | Đã kiểm | Trong khoảng |
| (b) xe buýt hoặc xe tải | s05f41, #3 | Bus | Đúng hướng | Có | Đã kiểm | Chiều rộng ngoài khoảng; xem mục 5. |
| (c) người đi bộ hoặc xe hai bánh | s03f46, #5 | Cyclist | Đúng hướng | Có | Đã kiểm | Cần đối chiếu loại xe; xem mục 5. |

Tôi chưa tham gia vòng hiệu chuẩn với lớp nên chưa có kết quả so sánh cuboid hoặc kết luận hiệu chuẩn chung. Không có vật bỏ qua vì không đủ cơ sở gán nhãn.

## 4. Bốn hiện tượng chất lượng (Bước 4)
Các ảnh dưới đây được chọn từ task cá nhân. Nguyên nhân là **giả thuyết**, chưa được xác minh; những chi tiết chưa có trong ghi chép được nêu rõ.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s01f08; `t4-1-bong-che.png`; task cá nhân D13-2A202603183 | Giả thuyết: vật ở gần chặn tia LiDAR tới vùng phía sau. | Ảnh được chọn để minh họa bóng che; chưa có mô tả riêng về vật che và vị trí vùng mất điểm. | Khi vật bị che một phần, bật occluded và bao phần bị che có đủ cơ sở suy luận; không coi vùng chưa quan sát được là đường trống. |
| 2. Thưa ở xa | s01f08; `t4-2-thua-o-xa.png`; task cá nhân D13-2A202603183 | Giả thuyết: khoảng cách tăng khiến ít tia quét trúng vật hơn. | Ảnh được chọn để minh họa điểm thưa ở xa; chưa ghi riêng vị trí và khoảng cách của vật trong ảnh. | Dùng mốc thấy khoảng 15–20% thể tích để xem xét vẽ; ít hơn chỉ vẽ khi đủ cơ sở suy luận, nếu không thì bỏ qua và ghi LOW_VISIBILITY. |
| 3. Phản xạ thấp / ít điểm bất thường | s01f31; `t4-3-phan-xa-thap.png`; task cá nhân D13-2A202603183 | Giả thuyết: đặc tính bề mặt hoặc góc tới làm giảm tín hiệu phản hồi; chưa loại trừ che khuất hay khoảng cách. | Ảnh được chọn cho hiện tượng ít điểm bất thường; chưa có số đo intensity hoặc vật đối chiếu cùng tầm để xác nhận phản xạ thấp. | Đối chiếu camera và hình dạng điểm; không khẳng định nguyên nhân chỉ từ việc ít điểm và không dùng kích thước tham chiếu thay cho bằng chứng. |
| 4. Điểm lạ | s01f31; `t4-4-diem-la.png`; task cá nhân D13-2A202603183 | Giả thuyết: nhiễu đo hoặc phản xạ đa đường; cũng có thể là vật thật ở xa chưa nhận ra. | Có cụm điểm chưa nhận dạng được là vật gì; chưa đủ cơ sở kết luận là nhiễu. | Không gán cụm điểm bất thường thành vật khi chưa có cơ sở; cần kiểm tra thêm góc nhìn để phân biệt với vật thật ở xa, thưa điểm. |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-2A202603183-bangA.csv`; số điểm lấy từ cột `points_in` của `submission/D13-2A202603183-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z (m) | h (m) | z đáy = z − h/2 (m) | dist_m (m) | r = √(x²+y²+z²) (m) | yaw_deg (°) | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s03f46, #1, Car | −1,040 | 1,452 | −1,766 | 4,37 | 4,495 | 268,6 | Đáy sát mặt đường theo kiểm tra Top/Side/Front; gần mốc tham chiếu −1,7 m. |
| s05f41, #3, Bus | −0,156 | 3,123 | −1,717 | 6,02 | 6,024 | 270,8 | Đáy sát mặt đường theo kiểm tra Top/Side/Front; gần mốc tham chiếu −1,7 m. |
| s03f46, #5, Cyclist | −0,959 | 1,591 | −1,755 | 7,13 | 7,190 | 271,5 | Đáy sát mặt đường theo kiểm tra Top/Side/Front; gần mốc tham chiếu −1,7 m. |

Các giá trị z đáy giữ theo Bảng A đã cung cấp. Tính lại từ z và h đã làm tròn cho Cyclist được −1,7545 m, Bus được −1,7175 m; chênh lệch nhỏ với cột xuất là do độ chính xác làm tròn. Khoảng cách r tính từ các tọa độ x, y, z đã cung cấp.

Đối chiếu kích thước với bảng 4.3:

- Car s03f46 #1 có dài × rộng × cao = 4,346 × 1,854 × 1,452 m, nằm trong các khoảng tham chiếu của sedan/hatchback: dài 3,8–5,0 m, rộng 1,70–1,90 m, cao 1,40–1,60 m.
- Cyclist s03f46 #5 có kích thước 1,663 × 0,851 × 1,591 m. Nếu là xe đạp có người, rộng 0,851 m vượt khoảng 0,50–0,70 m và cao 1,591 m thấp hơn cận 1,60 m. Nếu là xe máy có người, dài 1,663 m thấp hơn khoảng 1,8–2,3 m; rộng và cao nằm trong khoảng tham chiếu của loại này. Chưa xác nhận xe đạp hay xe máy, nên chưa có kết luận về nguyên nhân kích thước ngoài khoảng.
- Bus s05f41 #3 có kích thước 11,985 × 3,117 × 3,123 m. So với xe buýt thường, dài và cao trong khoảng tham chiếu, nhưng rộng 3,117 m vượt khoảng 2,50–2,60 m. Chưa xác nhận nguyên nhân hoặc có lần sửa tiếp theo; đây là điểm còn cần xem lại, dù selfcheck không gắn cờ hộp này.

Khoảng cách gần nhất và xa nhất trong toàn bộ Bảng A:

| Cuboid | dist_m (m) | r (m) | Phạm vi |
|---|---|---|---|
| s01f08, #2, Car — gần nhất | 3,79 | 3,925 | Trong 50 m |
| s01f08, #7, Car — xa nhất | 75,47 | 75,481 | Ngoài 50 m, không được chấm |

Hai ví dụ hướng được chọn theo tọa độ và yaw trong Bảng A: Car `s01f08 #3` có y = −21,109 m và yaw = 269,8°, phù hợp với hướng cùng chiều ở phía trước; Car `s02f17 #5` có y = −4,159 m và yaw = 90,5°, phù hợp hướng ngược chiều ở phía trước. Đây là đối chiếu số của nhãn đã vẽ; chưa có xác nhận riêng bằng camera cho hai ví dụ này, nên không coi đó là bằng chứng độc lập rằng hướng nhãn đúng.

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m (m) | Số điểm (`points_in`) |
|---|---|---|
| s02f17, #6, Car | 5,08 | 2664 |
| s05f07, #3, Car | 15,11 | 665 |
| s02f32, #3, Car | 24,72 | 244 |
| s03f18, #8, Car | 40,60 | 161 |

Chọn một Car trong mỗi nhóm 0–10, 10–20, 20–30 và 30–50 m, ưu tiên khoảng cách gần giữa nhóm; ghép hai CSV bằng frame và idx. Trong bốn mẫu này, số điểm giảm khi khoảng cách tăng: mẫu ở 40,60 m xa gần 8 lần mẫu ở 5,08 m, nhưng chỉ có 161 so với 2664 điểm, còn khoảng 6,0%. Đây là bốn vật khác nhau nên kích thước, góc quan sát và che khuất cũng có thể ảnh hưởng kết quả.

So với số tham chiếu xe con trong slide, ba mẫu đầu lần lượt có 2664 so với 1968 điểm ở nhóm 0–10 m, 665 so với 718 điểm ở nhóm 10–20 m, và 244 so với 214 điểm ở nhóm 20–30 m. Xu hướng giảm tương tự; số tham chiếu không phải số bắt buộc của từng vật. Mẫu 40,60 m không nằm trong nhóm 30–40 m nên không so trực tiếp với mốc 72 điểm của nhóm đó.

### Tự kiểm và sửa cuboid

Tôi đã sửa bốn cuboid sau khi xem các cảnh báo tự kiểm dưới đây. Các số trong bảng là **cảnh báo trước khi sửa**, không phải số đo cuối cùng.

| Cuboid trước khi sửa (frame, idx, class) | Cảnh báo trước khi sửa | Nội dung sửa |
|---|---|---|
| s01f08, #2, Car | Đáy cao hơn mặt đường 1,28 m; chiều cao 1,00 m nằm ngoài khoảng kiểm tra 1,2–2,3 m của Car. | Chỉnh lại đáy sát mặt đường để hộp không lơ lửng; tăng chiều cao cho phù hợp với vật. |
| s01f08, #4, Bus | Đáy cao hơn mặt đường 24,94 m. | Chỉnh lại vị trí theo phương đứng để đáy hộp sát mặt đường. |
| s02f17, #5, Car | Chiều rộng 2,51 m nằm ngoài khoảng kiểm tra 1,4–2,3 m của Car. | Thu lại chiều rộng để hộp sát vật hơn, tránh bao dư. |
| s05f07, #5, Car | Đáy cao hơn mặt đường 1,06 m; có 57 điểm sát ngay ngoài mép, công cụ yêu cầu xem lại hộp quá chật hay điểm thuộc vật bên cạnh. | Chỉnh đáy sát mặt đường; chỉnh lại mép hộp để bao phần điểm thuộc vật. |

Trong phiếu tự kiểm hiện tại, bốn cặp frame/idx ở bảng sửa trên đều không còn cờ. Cảnh báo còn lại là Car `s05f41 #6`: rộng 2,418 m và cao 2,310 m vượt khoảng kiểm của công cụ. Hộp có 628 điểm bên trong, khoảng hở đáy với mặt đường ước lượng là 0,07 m. Chưa có lý do quan sát được xác nhận để giữ nguyên kích thước này; cần xem lại, không coi hết các cờ khác là bằng chứng mọi hộp đã đúng.

### Đối chiếu với bài của bạn

Ngày **26/11/2026**, tôi nhờ công cụ đối chiếu hai số trong mục 5 báo cáo của MSSV **2A202603192** với Bảng A trong bộ ZIP bạn ấy gửi. Kết quả dưới đây thuộc bài của bạn ấy, không phải số đo của task cá nhân của tôi.

| Nội dung kiểm | Frame, idx, class trong bài bạn | Cột | Báo cáo bạn (m) | Bảng A bạn (m) | Kết quả |
|---|---|---|---|---|---|
| Khoảng cách gần nhất | s04f00, #1, Car | dist_m | 4,45 | 4,45 | Khớp; nhỏ nhất trong 99 dòng. |
| Khoảng cách xa nhất toàn bảng | s03f24, #6, Car | dist_m | 75,37 | 75,37 | Khớp; lớn nhất trong 99 dòng, ngoài 50 m. |

Không phát hiện chênh lệch cần sửa ở hai số này. Việc đối chiếu chỉ kiểm sự khớp nhau giữa báo cáo và CSV, không xác nhận hình học cuboid hay chất lượng nhãn. Đây chưa phải đổi kiểm trực tiếp trên màn hình theo GUIDE.md.

Chiều bạn ấy kiểm bài của tôi **chưa có kết quả**. Hai số tôi đề xuất để bạn ấy kiểm là `dist_m` của Car `s01f08 #2` (**3,79 m**) và Car `s01f08 #7` (**75,47 m**). Chưa ghi nhận yêu cầu đổi kiểm là đã hoàn thành.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
Nhãn 3D dùng mét để model học vị trí, khoảng cách và kích thước vật trong không gian thực; kích thước theo pixel thay đổi theo góc nhìn và khoảng cách nên không biểu diễn trực tiếp các đại lượng này. Với vật thưa điểm, cần quy tắc về mức bằng chứng và phần được phép suy luận để hạn chế tạo nhãn thiếu căn cứ, khiến model học sai vị trí hoặc kích thước.

Cách dùng AI:

- Công cụ: ChatGPT/Codex, có sử dụng công cụ tạo/chỉnh ảnh AI.
- Câu hỏi chính: hướng dẫn lệnh và thao tác CVAT, quy tắc Shape/class, yêu cầu ảnh và bài nộp; thêm trục hướng, khoanh cụm điểm trên ảnh; trích số từ hai CSV, đối chiếu hai số trong bài MSSV 2A202603192 và hoàn thiện báo cáo.
- Cách dùng kết quả: nhờ AI biên tập ghi chép, giải thích khái niệm, ghép frame/idx, tính khoảng cách/tỉ lệ và bổ sung các giả thuyết được ghi rõ là chưa xác minh; đóng gói và kiểm bộ nộp. Kết quả đối chiếu bài bạn được ghi riêng ở mục 5, không coi là xác nhận bạn ấy đã kiểm bài tôi. Hai bản ảnh top đánh dấu bằng AI cần thay bằng ảnh gốc đánh dấu trực tiếp để giữ nguyên bằng chứng; việc thay ảnh chưa được xác nhận.

Điều còn chưa chắc: loại xe cụ thể và kích thước của mẫu Cyclist; chiều rộng Bus; cảnh báo kích thước Car còn lại; bản chất cụm điểm rời trong ảnh điểm lạ. Độ cao mặt đường chưa được đo trực tiếp, các ví dụ hướng lấy từ Bảng A còn cần đối chiếu camera, và bốn hộp ngoài 50 m không thuộc phạm vi chấm. Đã có kết quả đối chiếu hai số trong bài bạn bằng công cụ, nhưng chưa có phản hồi bạn ấy kiểm bài tôi hoặc xác nhận đổi kiểm trực tiếp trên màn hình. Không sử dụng số từ phiếu hồ sơ lỗi của coach trong báo cáo.
