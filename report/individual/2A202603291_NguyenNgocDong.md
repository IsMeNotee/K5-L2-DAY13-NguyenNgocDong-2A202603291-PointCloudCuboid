# Báo cáo Day 13 — Nguyen Ngoc Dong, 2A202603291

## 1. Kết quả

Task `D13-2A202603291`, job #6 trên CVAT local 2.41.0, Windows. Mười frame là `s01f07`, `s01f51`, `s02f08`, `s02f31`, `s03f25`, `s03f53`, `s04f04`, `s04f34`, `s05f00` và `s05f26`.

Job có 56 cuboid Shape. Frame `s01f07` có 11 hộp. Chín frame còn lại cùng có ít nhất, mỗi frame 5 hộp. Ưu tiên các cụm đủ rõ để đạt số hộp tối thiểu trên từng frame. Phiếu tự kiểm đánh dấu 11/56 hộp, trong đó 7 hộp thuộc `s01f07`. Mức độ khớp với mặt đường có thể kiểm bằng khoảng hở đáy. Chiều tiến ở các xe xa hoặc chỉ thấy một đầu vẫn cần đối chiếu camera. Chưa có thông tin về người kiểm chéo hoặc kết quả vòng hiệu chuẩn.

Trong CVAT, dùng góc nhìn từ trên để căn chiều dài và yaw, rồi kiểm độ cao ở Side và Front. Chọn Shape cho từng vật. Bật Cuboid orientation để thấy vạch đỏ `+X` và dùng `preview` để đối chiếu với ảnh camera.

## 2. Đọc hai frame đầu

Trong góc nhìn Top quy ước của bộ preview, phía trước xe thu dữ liệu ở trên, `+x` về trái và `+y` về dưới. Cao độ dưới đây là trung vị của điểm thấp trong 15 m quanh LiDAR. Mốc xa nhất trong bảng là hộp đọc được trong các nhãn đã lưu.

| Frame | Số điểm LiDAR | Cao độ mặt đường ước lượng | Vật đã gán nhãn xa nhất | Vật phía sau hoặc hai bên |
|---|---:|---:|---:|---|
| `s01f07` | 65.702 | −1,696 m | Car #11, 37,89 m | Car #11 ở phía sau. Các hộp Car và Truck nằm hai bên đường. |
| `s01f51` | 66.289 | −1,767 m | Car #4, 15,64 m | Có xe ở trước và sau LiDAR, gồm Car #1 ở phía sau. |

Ở `s01f07`, camera cho thấy xe con màu xanh ở bên hông. Ở `s01f51`, cam03 cho thấy đèn hậu và thân sedan của các xe trước LiDAR. LiDAR dùng để đo khoảng cách, cao độ đáy hộp và số điểm trong vùng bao. Cụm chỉ còn một mặt hoặc vài lát điểm cần đối chiếu camera trước khi suy đầu xe. Hai cặp ảnh là `t1-s01f07-3d.png`, `t1-s01f07-top.png`, `t1-s01f51-3d.png` và `t1-s01f51-top.png`. Hai ảnh `top` dùng góc nhìn chếch xuống và chưa đánh dấu trực tiếp các trục.

Tám frame còn lại dùng cùng hệ trục. `s02f08` có các cụm xe buýt dài, `s03f25` có cụm thưa cần kết hợp camera, và `s03f53` có hai hộp gần cùng phương nhìn. `s02f31`, `s04f04`, `s04f34`, `s05f00` và `s05f26` đều được gán nhãn trên bộ frame đúng MSSV.

## 3. Ba cuboid mẫu

| Vật | Frame và idx | Dài × rộng × cao (m) | Yaw | Đáy (m) | Điểm cần rà |
|---|---|---|---:|---:|---|
| Car | `s01f51` #2 | 3,650 × 1,570 × 1,489 | 264,3° | −1,781 | Rộng 1,570 m, nên rà theo loại xe trong ảnh. |
| Bus | `s02f08` #1 | 10,926 × 2,860 × 3,126 | 82,9° | −1,866 | Đối chiếu gương và mặt bên khi quyết định bề rộng. |
| Pedestrian cạnh Bus | `s03f25` #5 | 0,940 × 0,465 × 1,320 | 268,7° | −1,749 | Nằm thấp và hẹp hơn khoảng tham chiếu của người lớn. Cần kiểm tư thế và phần cơ thể bắt được trong đám mây điểm. |

Ảnh chụp minh chứng trong CVAT dùng tên `t2-a-car.png`, `t2-b-bus-truck.png` và `t2-c-ped-cyclist.png`. Ba đáy hộp đều trong khoảng 0,17 m quanh mốc mặt đường −1,7 m. Ảnh chụp có các ô Top, Side và Front. Các góc yaw của cuboid còn cần xác nhận bằng hình dạng đầu xe hoặc tư thế người.

## 4. Bốn hiện tượng chất lượng

| Hiện tượng | Dấu hiệu đo được trên các frame của bài cá nhân | Ảnh CVAT và giới hạn minh chứng | Cách xử lý khi gán nhãn |
|---|---|---|---|
| Bóng che | Ở `s03f53`, Car #2 cách 14,41 m và Car #4 cách 21,62 m. Hình chiếu phương vị của hai hộp chồng khoảng 98% vùng góc của hộp xa. Che khuất là giả thuyết từ vị trí hai hộp. | `t4-1-bong-che.png` là ảnh bối cảnh `s02f08`. Cặp xe đo ở `s03f53` chưa có ảnh cận. | Nếu xác nhận vật bị che một phần thì bật `occluded`; cuboid vẫn bao trọn phần bị che có đủ cơ sở suy luận. Dò mặt thấy được trước khi suy bề dài phía khuất, không coi vùng trống sau vật che là đường trống. |
| Thưa ở xa | `s01f07` Car #11 ở 37,89 m có 90 điểm, còn `s01f51` Car #2 ở 8,67 m có 1.284 điểm. Giả thuyết: ở xa, vật nhận ít tia quét hơn; góc tới và che khuất cũng có thể góp phần làm giảm số điểm. | `t4-2-thua-o-xa.png` chụp `s01f07`. Hộp #11 chưa được đánh dấu riêng. | Chỉ vẽ khi thấy khoảng 15–20% thể tích hoặc có đủ cơ sở suy luận từ camera và hình dạng. Không đủ cơ sở thì bỏ qua, ghi `LOW_VISIBILITY`; không kéo hộp theo vài điểm rời. |
| Phản xạ thấp | `s01f51` Car #4 ở 15,64 m có trung vị intensity 0,00, so với 0,08 của `s01f07` Car #5 ở 16,40 m. Giả thuyết: góc tới hoặc đặc tính bề mặt làm tín hiệu phản xạ thấp; số intensity chưa đủ để xác định nguyên nhân. | `t4-3-phan-xa-thap.png` chụp `s01f51`. Số intensity lấy từ dữ liệu LiDAR. | Đối chiếu hình dạng và ảnh camera, chỉ bao các mặt có cơ sở xác định. Không khẳng định phản xạ thấp đồng nghĩa vật bị che hoặc thiếu mặt xe. |
| Điểm lạ | `s03f25` có một điểm gần (−2,94 m, 32,85 m, −1,02 m). Trong nhóm điểm ngoài mặt đất đã lọc, điểm lân cận gần nhất cách khoảng 0,95 m. | `t4-4-diem-la.png` chụp `s03f25`. Ảnh toàn cảnh chưa chỉ vị trí điểm này. | Giả thuyết: điểm rời có thể là nhiễu đo hoặc phản xạ đa đường, chưa xác định được từ một frame. Kiểm lại ở Top và Side; không gán điểm lạ thành một vật riêng khi chưa có đủ bằng chứng. |

## 5. Đo và tự kiểm

| Cuboid | z (m) | h (m) | z đáy (m) | dist_m (m) | r 3D (m) | yaw | Số điểm |
|---|---:|---:|---:|---:|---:|---:|---:|
| `s01f07` Car #1 | −1,007 | 1,509 | −1,761 | 3,94 | 4,07 | 270,1° | 3.229 |
| `s01f51` Car #2 | −1,037 | 1,489 | −1,781 | 8,67 | 8,73 | 264,3° | 1.284 |
| `s01f51` Car #4 | −1,080 | 1,454 | −1,807 | 15,64 | 15,68 | 265,9° | 403 |
| `s01f07` Car #11 | −0,745 | 1,803 | −1,646 | 37,89 | 37,90 | 91,1° | 90 |

Car gần nhất và xa nhất là `s01f07` #1 và #11. Từ 8,67 m lên 37,89 m, khoảng cách tăng 4,4 lần và số điểm giảm từ 1.284 xuống 90, còn khoảng 7%. Xu hướng giảm cũng có trong số liệu tham chiếu của bài giảng, từ 1.968 điểm ở 0–10 m xuống 72 ở 30–40 m. Góc tới, che khuất và kích thước cũng ảnh hưởng mật độ. Đáy của bốn ví dụ nằm gần mốc −1,7 m. Khoảng cách ngang dùng `dist_m = √(x² + y²)`, còn khoảng cách 3D tới tâm hộp dùng `r = √(x² + y² + z²)`. Đáy hộp tính bằng `z_đáy = z − h/2`; bốn ví dụ trong bảng chưa cho thấy lơ lửng hoặc chìm quá 0,3 m so với mốc mặt đường tham chiếu −1,7 m.

**So kích thước với bảng 4.3**

| Cuboid | Dài × rộng × cao (m) | Khoảng tham chiếu dài / rộng / cao (m) | Đối chiếu và giới hạn kết luận |
|---|---|---|---|
| Car `s01f51` #2 | 3,650 × 1,570 × 1,489 | Sedan, hatchback: 3,8–5,0 / 1,70–1,90 / 1,40–1,60 | Dài thấp hơn cận dưới 0,150 m, rộng thấp hơn 0,130 m; cao nằm trong khoảng. Giả thuyết là hộp chưa bao đủ mép thân xe hoặc loại xe không khớp nhóm tham chiếu; cần đối chiếu camera và các mặt hộp. |
| Pedestrian `s03f25` #5 | 0,940 × 0,465 × 1,320 | Người lớn: 0,4–0,9 / 0,5–0,9 / 1,50–1,95 | Dài vượt cận trên 0,040 m, rộng thấp hơn cận dưới 0,035 m, cao thấp hơn 0,180 m. Giả thuyết là hộp chưa bám đủ các mặt hoặc tư thế khác người đứng thẳng; cần kiểm toàn bộ cơ thể trên Top, Side, Front và camera. Chiều cao nằm trong dải trẻ em 0,90–1,40 m, nhưng riêng số đo này không đủ để kết luận đây là trẻ em. |

Các khoảng kích thước là tham chiếu; không đổi kích thước chỉ để nằm trong khoảng khi chưa kiểm lại vật trên CVAT.

**Đọc hướng từ yaw đã gán**

Theo hệ VF, phía trước là `−y`, xe cùng chiều có yaw gần 270°, xe ngược chiều gần 90°. Car `s01f51` #2 có yaw 264,3°, lệch 5,7° so với 270°, tức đang được gán hướng gần cùng chiều xe thu dữ liệu. Car `s01f07` #11 có yaw 91,1°, lệch 1,1° so với 90°, tức đang được gán hướng gần ngược chiều. Cách đọc này dựa trên tham số hộp; cần đối chiếu đầu/đuôi xe trong camera để xác nhận mũi tên đã gán đúng hướng thực tế.

Ưu tiên rà `s03f53` Car #1 vì có 338 điểm sát mép ngoài, `s03f25` Pedestrian #4 vì có 87 điểm sát mép ngoài, và `s04f04` Car #3 vì rộng khoảng 2,60 m. `s01f07` Car #5 cần rà lớp và kích thước vì vừa rộng vừa cao hơn khoảng tham chiếu của Car. Phiếu tự kiểm là căn cứ chọn chỗ cần xem lại. Chưa xác nhận các cảnh báo này là lỗi.

## 6. Vai trò của nhãn và việc dùng AI

Mô hình 3D học kích thước vật theo mét và vị trí trong không gian. Với vật thưa điểm, ảnh camera và quan hệ che khuất giúp tránh suy kích thước từ một cụm điểm thiếu.

Công cụ: Codex.  
Câu hỏi chính: Kiểm tra các cuboid tôi đã tự vẽ trên CVAT có đúng chưa.  
Cách dùng kết quả: Tham khảo góp ý để rà soát lại các cuboid.

Cần rà hướng đầu xe, lớp của các cụm thưa và các cảnh báo trên ảnh. Mười ảnh preview được tạo trên máy để hỗ trợ rà soát. ZIP gồm 11 ảnh chụp CVAT, với các giới hạn minh chứng ghi ở mục 2 và 4. Chưa có kết quả kiểm chéo.
