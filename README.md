# Aweyuki Browser

Mốc hiện tại: chuẩn bị build Chromium Android ARM64 nguyên bản. Repo public chỉ chứa cấu hình và tài liệu; chưa có APK, patch extension hoặc giao diện profile.

## Build baseline

Workflow `.github/workflows/android.yml` chạy thủ công. Trong repo public, job dùng `ubuntu-24.04`, mở rộng vùng build và dừng trước khi tải source nếu còn dưới 100 GB. Log build và artifact baseline công khai; không dùng khóa riêng hay đưa log thiết bị vào job. Repo private có thể đặt `CHROMIUM_RUNNER_LABEL` để dùng runner Linux x64 đã cấp quyền.

`ci/chromium.lock` ghim tag, commit Chromium và commit `depot_tools`; `ci/baseline.gn` là cấu hình release không branding Chrome. Workflow lấy phụ thuộc theo `DEPS`, build `//chrome/android:chrome_public_apk` và lưu APK, checksum, GN args và metadata run với thời hạn 7 ngày nếu thành công.

Baseline giữ package và khóa debug mặc định của upstream, chỉ dùng trên máy thử. Bản phân phối nội bộ sau đó cần package ID riêng, khóa test ổn định lưu ngoài Git, và `versionCode` tăng cho lần cài đè. Không coi việc chuyển từ package baseline sang package riêng là kiểm thử cập nhật.

## Xác minh trên Android ARM64 thật

1. Ghi model máy, bản Android, run ID, revision và hash APK. Cài mới `ChromePublic.apk` và mở một trang HTTPS.
2. Với hai bản có cùng package và chứng thư ký, cài bản có `versionCode` cao hơn bằng `adb install -r` và kiểm tra dữ liệu site thử còn nguyên. Ghi rõ cặp bản đã dùng.
3. Đo PSS, thời gian khởi động lạnh, jank, số tab tải lại và dung lượng tải/cài theo cùng kịch bản 1/5/10 tab. Không so bản debug với bản release.

Xem [DECISIONS.md](DECISIONS.md) để phân biệt điều đã đối chiếu với các cổng còn chưa thử.
