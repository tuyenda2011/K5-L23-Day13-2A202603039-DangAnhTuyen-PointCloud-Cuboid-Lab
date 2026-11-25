# Báo cáo Day 13 — Đặng Anh Tuyền, 2A202603039


## 1. Kết luận trước
MSSV **2A202603039** · Task `D13-2A202603039` · Windows · Microsoft Edge · CVAT 2.82.1.

10 frame: `s01f16`, `s01f50`, `s02f11`, `s02f52`, `s03f04`, `s03f40`, `s04f35`, `s04f59`, `s05f07`, `s05f46`.

Kết quả lần xuất gần nhất có **90 cuboid**, trong đó **26** được `selfcheck` đánh dấu để xem lại. Frame có ít cuboid nhất là `s03f40`, với **5 cuboid**.

Số hộp ít nhất phản ánh phần đã gán, chưa đủ để kết luận frame này ít vật hơn. Phần có căn cứ đối chiếu rõ nhất là kích thước và số điểm theo khoảng cách. Phần còn cần xem lại là hướng đầu vật, đáy hộp và các cụm điểm thưa.

- Đổi góc nhìn trong khung 3D: kéo chuột trái để xoay; kéo chuột phải để dời cảnh. Xoay khung 3D đến khi nhìn thẳng từ trên xuống để quét toàn cảnh.
- Tạo cuboid: chọn nhãn → **Shape** → nháy đúp vào cụm điểm. Dùng **Top** để căn tâm, dài, rộng và yaw. Dùng **Side/Front** để căn chiều cao và đáy.
- Mở **Appearance → Cuboid orientation** để hiện trục hộp. Vạch đỏ chỉ về đầu xe. Lệnh `preview` tạo ảnh chiếu hộp lên camera trong `work/xem/`.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ: `s01f16` và `s01f50`. Ảnh: `t1-s01f16-3d.jpg`, `t1-s01f16-top.png`, `t1-s01f50-3d.jpg`, `t1-s01f50-top.png`. Đánh dấu phía trước, +x và +y trên ảnh nhìn từ trên xuống.

Theo hệ tọa độ VF, phía trước xe thu dữ liệu là −y; +x hướng sang trái và +y hướng ra sau. Cạnh ảnh tương ứng phụ thuộc góc nhìn khi chụp.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| `s01f16` | Trước là −y, +x sang trái xe, +y ra sau. Ảnh chưa ghi nhãn trục nên chưa xác nhận cạnh ảnh | Camera cho thấy đường khá phẳng, nền tham chiếu khoảng −1,7 m | Bảng A có Truck ở 46,40 m, ảnh toàn cảnh chưa đủ rõ để xác nhận class của vật này | Xe con, xe buýt trong camera, Bảng A còn có Truck | cam05 thấy xe con bên hông, các hộp xuất hiện ở cả hai phía quanh gốc LiDAR | Cụm xa và các điểm ven đường chưa đủ rõ để tách vật |
| `s01f50` | Trước là −y, +x sang trái xe, +y ra sau. Ảnh chưa ghi nhãn trục nên chưa xác nhận cạnh ảnh | Camera cho thấy đường khá phẳng, nền tham chiếu khoảng −1,7 m | Car xa nhất đã gán ở 20,14 m, ảnh còn có cảnh xa hơn chưa đo | Nhiều xe con phía trước, Bảng A có 6 Car | cam05 thấy xe con bên hông, ảnh nhìn xuống có hộp ở hai phía gốc | Cụm xa gần giao lộ và điểm sát lề chưa đọc đầy đủ |

Ở hai frame, camera thể hiện màu thân xe, vạch sơn phân làn và màu cây ven đường. LiDAR cung cấp vị trí theo mét quanh cảm biến, hình dạng 3D của bề mặt và mật độ điểm theo khoảng cách. Các thông tin này bổ sung cho nhau khi xác định class và căn hình học của hộp.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| `s02f11` | −y, ảnh hiện có chưa phải góc Top toàn cảnh để chốt cạnh | Có các hộp đứng nhỏ, hướng người chưa rõ |
| `s02f52` | −y, ảnh chưa ghi nhãn trục | Các vệt điểm rời chưa được khoanh và kiểm tra cao độ |
| `s03f04` | −y, ảnh chưa ghi nhãn trục | Hàng rào quây kéo dài dọc đường, vùng phía ngoài ít điểm |
| `s03f40` | −y, chưa có ảnh Top để xác nhận cạnh | Chưa có ghi chép quan sát riêng |
| `s04f35` | −y, ảnh chưa ghi nhãn trục | Một số hộp ít nét điểm, chưa có cặp cùng tầm để so phản xạ |
| `s04f59` | −y, chưa có ảnh Top để xác nhận cạnh | Chưa có ghi chép quan sát riêng |
| `s05f07` | −y, chưa có ảnh Top để xác nhận cạnh | Đáy của hộp #15 còn cần kiểm tra theo mặt đường thực tế |
| `s05f46` | −y, chưa có ảnh Top để xác nhận cạnh | Có hộp xa hơn phạm vi chấm 50 m |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car.jpg`, `t2-b-bus-truck.jpg`, `t2-c-ped-cyclist.png`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | `s01f16 #1` | Car | 271,3°, gần −y. Ảnh chưa nối rõ hộp #1 với xe cụ thể để chốt hướng đầu | Đáy −1,808 m, thấp hơn nền ước tính 0,05 m | Có Top/Side/Front của hộp trong ảnh, orientation chưa bật | 4,988 × 1,812 × 1,547 m, trong khoảng sedan/hatchback |
| (b) xe buýt hoặc xe tải | `s01f16 #5` | Bus | 275,0°, gần −y. Camera thấy đuôi xe buýt phía trước, phù hợp hướng này | Đáy −1,764 m, cao hơn nền ước tính 0,19 m | Có Top/Side/Front của hộp trong ảnh, orientation chưa bật | 13,204 × 2,967 × 3,440 m. Dài và rộng vượt khoảng xe buýt thường, cần xem lại |
| (c) người đi bộ hoặc xe hai bánh | `s02f11 #10`, ở frame thứ ba vì hai frame đầu chưa có nhãn phù hợp. Chưa có xác nhận của coach về lựa chọn này | Pedestrian (ứng viên) | 37,8°, ảnh chưa đủ rõ để xác nhận hướng đi | Đáy −1,342 m, cao hơn nền tự kiểm 0,37 m, cần kiểm tra lại | Có ảnh 3D bật orientation, chưa chọn riêng #10 để kiểm đủ ba mặt | 0,915 × 0,627 × 1,541 m. Dài nhỉnh hơn ngưỡng tham chiếu 0,90 m |


## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s03f04 / t4-1-bong-che.png / task cá nhân | Giả thuyết là hàng rào hoặc bề mặt quây công trình chặn tia LiDAR | Camera thấy hàng rào dọc đường, point cloud có dải bề mặt kéo dài và vùng phía ngoài ít điểm. Ảnh chưa khoanh rõ ranh giới bóng che | Dùng occluded khi vật bị che và chỉ bao phần có thể suy luận có căn cứ. Vùng thiếu điểm cần kiểm tra trước khi kết luận trống vật |
| 2. Thưa ở xa | s01f16 #7 / t4-2-thua-o-xa.png / task cá nhân | Giả thuyết là vật ở xa nên ít tia LiDAR giao với bề mặt | Bảng tự kiểm ghi Car #7 ở 33,12 m có 48 điểm. Ảnh toàn cảnh cho thấy cụm xa nhỏ và khó đọc, chưa chọn riêng #7 | Gán khi đủ căn cứ suy luận hình dạng theo guideline, thiếu căn cứ thì ghi LOW_VISIBILITY |
| 3. Phản xạ thấp / ít điểm bất thường | s04f35 / t4-3-phan-xa-thap.png / task cá nhân | Giả thuyết là vật liệu hoặc góc bề mặt làm giảm tín hiệu phản hồi | Một số hộp chỉ có ít nét điểm bên trong. Ảnh chưa đánh dấu cặp cùng tầm hoặc hiển thị intensity nên chưa đủ để xác nhận phản xạ thấp | Đối chiếu camera và hình dạng trước khi gán, quá ít thông tin để suy luận thì ghi LOW_VISIBILITY |
| 4. Điểm lạ | s02f52 / t4-4-diem-la.png / task cá nhân | Nhiễu hoặc phản xạ nhiều đường là giả thuyết cần kiểm tra | Có các vệt và cụm điểm rời quanh cảnh, nhưng ảnh chưa khoanh cụm cụ thể hoặc xác nhận cao độ bất thường | Chỉ tạo hộp khi có căn cứ xác định vật. Kiểm tra điểm lẻ bằng nhiều góc nhìn và camera |

## 5. Số từ cuboid của tôi (Bước 5)

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| `s01f16 #1` Car | −1,035 m | 1,547 m | −1,808 m | 5,88 m | 5,97 m | 271,3° | Thấp hơn nền tự kiểm 0,05 m, gần mặt đường |
| `s01f16 #2` Car | −1,041 m | 1,532 m | −1,807 m | 10,10 m | 10,16 m | 271,5° | Cao hơn nền tự kiểm 0,01 m, gần mặt đường |
| `s01f16 #5` Bus | −0,044 m | 3,440 m | −1,764 m | 30,93 m | 30,93 m | 275,0° | Cao hơn nền tự kiểm 0,19 m, cần xem đáy trong Side/Front |

- (a) Gần nhất là `s03f04 #3` với `dist_m` **3,75 m** và r **3,88 m**. Xa nhất trong Bảng A là `s05f46 #12` với `dist_m` **62,47 m** và r **62,47 m**; hộp này ngoài phạm vi 50 m của bài.
- (b) Car `s03f04 #3` có kích thước **4,132 × 1,712 × 1,413 m**, trong khoảng của sedan/hatchback. Pedestrian `s02f11 #10` có kích thước **0,915 × 0,627 × 1,541 m**, chiều dài vượt khoảng người lớn **0,015 m**. Ảnh chưa chọn riêng người này nên chưa xác nhận được phần nhô ra có thuộc cơ thể hay vật bên cạnh. Cảnh báo điểm sát mép hộp cho thấy cần kiểm tra lại.
- (c) `s01f16 #2` có yaw **271,5°**, gần **270°**, tương ứng hướng trước xe (−y). `s01f16 #8` có yaw **91,8°**, gần **90°**, tương ứng hướng ra sau (+y). Hai hướng này đối nhau theo số đo, ảnh chưa chọn riêng hai hộp để xác nhận đầu xe thực tế.

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| `s01f16 #1` Car | 5,88 m | 2.449 |
| `s01f16 #2` Car | 10,10 m | 1.020 |
| `s01f16 #8` Car | 23,84 m | 197 |
| `s01f16 #7` Car | 33,12 m | 48 |

Trong bốn mẫu Car này, hộp ở **33,12 m** cách xa gấp khoảng **5,6 lần** hộp ở **5,88 m** và còn khoảng **2%** số điểm. Xu hướng giảm theo khoảng cách phù hợp với slide bài giảng, dù số điểm cụ thể còn tùy vật, góc quan sát và các phần bị che.

Giữ nguyên `s05f07 #15` trong lần tự kiểm này: thử nâng theo mặt đường ước tính làm số điểm trong hộp giảm từ **159** xuống **57**. Cần kiểm tra lại mặt đường thực tế trong Side/Front.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
**(a) Ý nghĩa của nhãn.** Nhãn 3D dùng mét để mô hình học vị trí và kích thước vật trong không gian, vì số pixel của cùng một vật thay đổi theo khoảng cách và góc nhìn. Vật thưa điểm cần quy tắc riêng vì nếu gán hộp khi hình dạng không đủ rõ, mô hình có thể học từ vị trí hoặc kích thước do người gán suy đoán.

**(b) Cách dùng AI**

- Công cụ: ChatGPT (Codex).
- Câu hỏi chính: cách dùng CVAT, chỉnh hướng cuboid, xuất kết quả và đọc cảnh báo tự kiểm.
- Cách dùng kết quả: làm theo hướng dẫn thao tác và xuất tệp. AI hỗ trợ kiểm tra cảnh báo. Hỗ trợ viết chau chuốt phần báo cáo. 

**(c) Điều còn chưa chắc.** Hướng của người đi bộ và một số xe chưa được đối chiếu rõ với camera. Hộp người còn có dấu hiệu cao hơn nền tự kiểm. Hai ảnh Top thiếu nhãn trục. Ảnh phản xạ thấp thiếu vật so sánh cùng tầm, ảnh điểm lạ chưa chỉ rõ cụm bất thường. Những phần này cần kiểm tra trong CVAT để tăng độ chắc chắn của báo cáo.
