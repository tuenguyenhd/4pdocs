# 4P Update - Code
## Các trường trên parse
- Report: Đây là bảng quan trọng nhất lưu các vấn đề được user đẩy lên để xử lý
	- `state`: 
		- open: Tin đã được duyệt để show lên trên app
		- close: Tin đã xử lý
		- pending: Tin đang chờ được duyệt
		- **Chú ý trên app chỉ select hiện các tin có state là `open`, `close`**
	- `images`: Ảnh đi kèm trong report
	- `follower`: List user follow tin dạng array lưu objectID của các user follow tin. Ví dụ user click nút follow trên app thì report sẽ tự thêm objectID của user đó vào array. 
	- `isanym`: Bool cái này là true thì tức là tin đăng là tin ẩn danh, ko hiện người gửi				
	- `report_type`: Pointer cái này trỏ vào 1 row trong `Report_Category` loại report khi tạo report mới
- Report_Category: Lưu các thông tin khi tạo mới report. (Xem app iOS)
	- `is_child` : Nếu là false thì sẽ là tin ở mức lớn (Môi trường, Giao thông), nếu là true thì là tin ở mức chi tiết là con của tin ở mức lớn (Cây đỏ, Cầu sập, ...)
	- `parent`: Trỏ vào loại tin ở mức lớn nếu is_child là true
	
- User:
	- full_name: Full name của user
	- phone_number: Số điện thoại
	- following: Array - Các `Report` mà user đang theo dõi (Khi click nút theo dõi trên mobile sẽ update trường )
- Contact: Dùng để gửi tin nhắn tới 4P
	- `title`: Title của tin nhắn
	- `content`: Content của tin nhắn
	- `owner`: Pointer - Nếu user đã login thì truyền User vào trường này
 	- `phone_number`: Nếu user chưa login thì trong form contact hiện thêm 1 ô để điền số điện thoại vào 
- Events: Dùng để lưu các sự kiện. Có 2 cách để tạo sự kiện. Bắt buộc User phải login mới cho tạo event
	- Tạo mới 1 sự kiện từ trang list event 
	- Sự kiện gắn với 1 `Report`. Trong detail của 1 report làm nút `Tạo sự kiện` (Đã có trong bản iOS).
	- Các param của `Events`:
		- `event_name` : Tên của event
		- `description`: Nội dung của event
		- `images` : Các ảnh trong event (Đối với event tạo mới từ report khi tạo event tự lấy link ảnh của report và save vào). Đối với event tạo mới thì up ảnh và nhét link vào
		- `location`: Vị trí của event
		- `owner`: Người tạo event (Tạo event bắt buộc phải login)
		- `title`: Title của event
		- `phone_number`: Trong form tạo event yêu cầu nhập phone number của người tạo (Tự động lấy phone number của user đã login điền vào, cho phép nó sửa để nhập số khác)
		- `org` : Tên tổ chức tạo event ví dụ "UBND TP Hà Nội", "UNDP", cho nó nhập vào
- INFOAPP: lưu các thông tin của version hiện tại để checkupdate
	- `download_link`: Link tải app
	- `build_num`: Build num của app. Check nếu buildnum trên Parse > buildnum hiện tại của app thì alert update lên cho user (Yes, No alert)
	- version: Version của bản app hiện tại
	- `platform`: android hay ios, app nào thì chỉ select trường của app đó- 
- NEWS: Phần tin tức hiện ra ở tab tin tức. Cái này chỉ nhập trên server, client chỉ lấy xuống
	- `description`: Nội dung của tin
	- `images`: Các ảnh của tin
	- `location`: Vị trí xảy ra tin
	- `title` : Title của tin
- Notification: Cái tab thông báo trong app (iOS), select bảng notification để hiển thị trong tab đó, click vào 1 notification sẽ nhảy qua nội dung của 1 post, tuỳ theo `notification_type` có thể là `news`, `report`, `event`. Loại nào thì nhảy qua nội dung đó 
	- `notification_type`: Loại notification để xem nội dung
	- `is_read`: Update cái này là true, false. True nếu đã đọc, false nếu chưa đọc để change số ở notification.
- SupportLocation: Hiển thị trong phần liên hệ của (Xem app iOS)
	- Hiển thị map các thông tin hỗ trợ như thùng rác, nhà vệ sinh ko buồn đái đái bậy lại mất tièn
- SupportNumber: Như ở trên cho xem số 113 còn gọi (Xem app iOS)

## Task iOS
- Khi user click vào "Hồ sơ" -> Hiện ra form login, hoặc sau khi user tạo xong 1 `Report` thì alert lên cho nó là "Bạn có muốn đăng nhập để chúng tôi xử lý dễ dàng hơn?"- Chọn Có - Chọn Không. 
- Form login yêu cầu có các trường sau:
	- Username
	- Password 
	- FullName - Không bắt buộc	
	- PhoneNumber  - Không bắt buộc
	- Email - Không bắt buộc
- Thêm check update app qua API Parser sử dụng thông tin từ INFOAPP
- Trong phần `News` hiện thêm tên người gửi (sử dụng trường `org` trong bản NEWS)
- Thêm tên người gửi trong report, lấy ra theo User trong trường Owner, trường hợp tin `isanym` thì ko hiện tên người gửi
- Bỏ nút request
- Thêm nút `Theo dõi` (icon dấu *) trong mỗi report, cả ở ngoài và trong detail. Thì update array `follower` với objectID của thằng đang sử dụng App. Đồi thời update trường `following` của bảng thêm objectID của report vào 
- Trong phần hồ sơ của user, mục tin đã lưu chuyển thành tin đã theo dõi, list ra hết các tin có objectID lưu trong trường `following` của user. Mục tin đã đăng select bảng `Report` lấy ra những tin nào owner chính là User đó.
- Gắn push notification Parse
- Phần "Thông báo" làm chuẩn lấy ở bảng "Notification" và làm update read, unread (sử dụng biến `is_read`). Update số notification màu đỏ ở tab đó như FB. Cứ read xong thì giảm số

## Task Android
- Khi user click vào "Hồ sơ" -> Hiện ra form login, hoặc sau khi user tạo xong 1 `Report` thì alert lên cho nó là "Bạn có muốn đăng nhập để chúng tôi xử lý dễ dàng hơn?"- Chọn Có - Chọn Không. 
- Form login yêu cầu có các trường sau:
	- Username
	- Password 
	- FullName - Không bắt buộc	
	- PhoneNumber  - Không bắt buộc
	- Email - Không bắt buộc
- Thêm check update app qua API Parser sử dụng thông tin từ INFOAPP
- Trong phần `News` hiện thêm tên người gửi (sử dụng trường `org` trong bản NEWS)
- Thêm tên người gửi trong report, lấy ra theo User trong trường Owner, trường hợp tin `isanym` thì ko hiện tên người gửi
- Bỏ nút request
- Thêm nút `Theo dõi` (icon dấu *) trong mỗi report, cả ở ngoài và trong detail. Thì update array `follower` với objectID của thằng đang sử dụng App. Đồi thời update trường `following` của bảng thêm objectID của report vào 
- Trong phần hồ sơ của user, mục tin đã lưu chuyển thành tin đã theo dõi, list ra hết các tin có objectID lưu trong trường `following` của user. Mục tin đã đăng select bảng `Report` lấy ra những tin nào owner chính là User đó.
- SupportLocation: Hiển thị trong phần liên hệ của (Xem app iOS)
	- Hiển thị map các thông tin hỗ trợ như thùng rác, nhà vệ sinh ko buồn đái đái bậy lại mất tièn
- SupportNumber: Như ở trên cho xem số 113 còn gọi (Xem app iOS)
- Gắn push notification Parse 
- Phần "Thông báo" làm chuẩn lấy ở bảng "Notification" và làm update read, unread (sử dụng biến `is_read`). Update số notification màu đỏ ở tab đó như FB. Cứ read xong thì giảm số

## Task Design
- Design trang list event tương tự như trang `Report` thêm 1 nút ở trên cùng như FB Feed tạo mới status
- Design trang tạo event mới event có các thông tin sau:
	- `event_name` : Tên của event
	- `description`: Nội dung của event
	- `images` : Các ảnh của event
	- `location`: Vị trí của event
	- `title`: Title của event
	- `phone_number`: Trong form tạo event yêu cầu nhập phone number của người tạo (Tự động lấy phone number của user đã login điền vào, cho phép nó sửa để nhập số khác)
	- `org` : Tên tổ chức tạo event ví dụ "UBND TP Hà Nội", "UNDP", cho nó nhập vào
- Design trang login, signup
- Làm infographic


## Task Web
- Quản lý user (ban, xoá, cập nhật thông tin, số lượng Report, cho phép xem được số lượng Report của user, bấm vào 1 user sẽ show ra 1 bảng list hết các Report của họ)
- Notification (Request để báo có báo cáo, tin nhắn mới) hiển thị dưới dạng số notification màu đỏ.
- Quản lý contact: Show ra các tin nhắn trong bảng `Contact` mà user gửi tới
- Quản lý tin tức: Cho phép tạo tin tức, xoá tin tức, ...
- Tất cả các trường hiện trên Parser đều phải có trong CMS để thêm, cập nhật, sửa xoá.
- Gắn API của thằng này cho phần SMS : http://esms.vn/SMSAPI/Overview
- APIKEY:
	- 	Api Key:ACCB50F2EF96F6DFDECC3339E4E309Get
	- 	Secrect Key:59B2975452E2416A70797BFAFAD5E8Get