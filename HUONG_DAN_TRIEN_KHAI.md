# Bản đồ số biển đảo Việt Nam — triển khai đa thiết bị

## Mô hình quyền
- Khách truy cập: đọc dữ liệu bản đồ công khai.
- Quản trị viên: đăng nhập Firebase Authentication bằng Email/Password; chỉ UID được chỉ định trong Firebase Rules có quyền ghi.
- Không lưu mật khẩu trong HTML.
- Tệp HTML có giao diện đăng nhập, xem dữ liệu cloud trực tiếp, thêm địa điểm, xuất/nhập JSON (chỉ quản trị viên được nhập).

> Trước khi cấu hình Firebase, website ở chế độ xem thử/local. Không có đường link công khai nào được tạo tự động bởi tệp HTML.

## A. Tạo Firebase
1. Mở https://console.firebase.google.com/ và tạo một project.
2. Trong Project settings > General > Your apps, thêm Web App và sao chép cấu hình `firebaseConfig`.
3. Authentication > Sign-in method: bật **Email/Password**.
4. Authentication > Users: tạo tài khoản quản trị bằng email của bạn và mật khẩu mạnh. Không chia sẻ mật khẩu.
5. Mở người dùng vừa tạo, sao chép **UID**.
6. Realtime Database > Create Database. Chọn vùng phù hợp (ví dụ Singapore nếu được cung cấp). Sau khi tạo, sao chép URL Database chính xác.
7. Realtime Database > Rules: dán nội dung từ `firebase-database.rules.json`, thay `PASTE_ADMIN_UID_HERE` bằng UID quản trị thật, rồi Publish.

Rules mẫu:
```json
{
  "rules": {
    "places": {
      ".read": true,
      ".write": "auth != null && auth.uid === 'PASTE_ADMIN_UID_HERE'"
    }
  }
}
```
Quy tắc này chỉ cấp quyền ghi vào nhánh `places` cho tài khoản có UID chính xác. Người khác có thể đọc dữ liệu; không được phép ghi. Không thay bằng `.write: true`.

## B. Cấu hình tệp HTML
Mở `ban_do_so_bien_dao_viet_nam_cloud_ready.html` bằng VS Code/Notepad. Tìm:
```js
const FIREBASE_CONFIG = {
  apiKey: "PASTE_FIREBASE_API_KEY",
  authDomain: "PASTE_PROJECT_ID.firebaseapp.com",
  databaseURL: "https://PASTE_DATABASE_NAME-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "PASTE_PROJECT_ID",
  appId: "PASTE_FIREBASE_APP_ID"
};
const ADMIN_UID = "PASTE_ADMIN_UID";
```
Thay bằng các giá trị từ Firebase Console. Dùng nguyên `databaseURL` Firebase cung cấp, không tự đoán tên vùng/URL. `ADMIN_UID` phải trùng UID đã đặt trong Rules. Firebase Web API key là cấu hình định danh dự án, không phải mật khẩu; bảo mật thực sự nằm ở Authentication và Rules. Không đưa service-account/private key vào trình duyệt.

## C. Đưa website lên mạng bằng GitHub Pages
1. Tạo tài khoản GitHub nếu chưa có và tạo repository mới, ví dụ `ban-do-bien-dao`.
2. Đổi tên tệp đã cấu hình thành `index.html`, tải lên repository.
3. Mở repository > Settings > Pages. Chọn Deploy from a branch, chọn `main` và `/ (root)`, Save.
4. Chờ GitHub Pages triển khai; link sẽ có dạng `https://TEN-GITHUB.github.io/ban-do-bien-dao/`. Đây là dạng ví dụ, link thật do GitHub hiển thị.
5. Mở link bằng máy tính/điện thoại. Bản đồ cần Internet để tải Leaflet và tiles OpenStreetMap.
6. Trong website, đăng nhập quản trị để thấy khu vực quản trị. Nếu database trống, bấm **Đưa dữ liệu mẫu lên cloud**. Sau đó thêm địa điểm hoặc nhập JSON; các thiết bị sẽ nhận cập nhật từ Realtime Database.

Tài liệu: https://pages.github.com/ và https://firebase.google.com/docs/database/web/start

## D. Kiểm tra bảo mật trước khi chia sẻ
- Mở link trong cửa sổ ẩn danh: bản đồ xem được nhưng không có quyền nhập liệu.
- Thử ghi dữ liệu từ tài khoản không phải quản trị: phải bị từ chối bởi Firebase Rules.
- Đăng xuất tài khoản quản trị sau khi hoàn tất.
- Chỉ chia sẻ tài khoản quản trị với người sở hữu; bật bảo mật tài khoản Google/email.
- Nếu lộ tài khoản, đổi mật khẩu và xóa/đổi tài khoản quản trị ngay.
- Tọa độ, tên và mô tả trong dữ liệu mẫu là mục đích giáo dục, một số điểm đánh dấu khái quát. Hãy đối chiếu nguồn chính thống trước khi dùng trong báo cáo chính thức.
