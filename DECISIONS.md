# Quyết định kỹ thuật

## Nền Chromium và CI

- Chọn Chromium Android `158.0.8127.42`: [Version History](https://versionhistory.googleapis.com/v1/chrome/platforms/android/channels/stable/versions/all/releases?filter=endtime%3Dnone&order_by=starttime%20desc&page_size=10) ghi nhận stable đang phục vụ khoảng 24,875% khi tra cứu; các bản mới hơn trong kết quả khi đó ở mức 0,25%. Đã đối chiếu tag, commit, `chrome/VERSION` và [tài liệu build tại commit đã ghim](https://chromium.googlesource.com/chromium/src/+/52bdd21723d5db0c9ab11716d38feac74828cf84/docs/android_build_instructions.md). Đánh giá lại pin theo stable và thông báo bản vá trước khi phát APK.
- Dùng cây nguồn đầy đủ lấy qua Git và `gclient`, giữ Kiwi Chromium 105 làm tham chiếu. Không dùng workflow build ngoài của Kiwi, không mang khóa ký, API key hay thành phần độc quyền sang.
- Thử build thủ công trên runner miễn phí Ubuntu x64 của repo public. Mở rộng vùng build bằng action ghim theo SHA, rồi đo dung lượng thực thay vì giả định runner có 124 GB. Cho phép công khai log và APK baseline ký debug, không dùng khóa nội bộ hoặc đưa log thiết bị lên CI. Không build trên host ARM64. Repo private có thể dùng runner riêng qua `CHROMIUM_RUNNER_LABEL`.

## Cổng còn mở

| Hạng mục | Trạng thái và lựa chọn |
|---|---|
| Baseline ARM64 | Chưa có APK được xác minh. Workflow public thử runner miễn phí; cần máy Android ARM64 thật để chạy smoke test khi có APK. |
| Profile | Chưa thử. Dùng profile thường riêng của Chromium, không thay bằng ẩn danh hoặc chỉ phân vùng cookie theo site. Cờ `kAndroidMultiprofile` mặc định tắt tại revision này; cần kiểm tra vòng đời trên máy thật trước khi thêm UI. |
| MV3 local | Chưa thử. Revision tách hỗ trợ extension core của Desktop Android khỏi Android thông thường. Chỉ thêm luồng chọn và xác nhận gói local khi runtime, installer và ranh giới quyền đã được kiểm chứng. |
| Chrome Web Store | Chưa xác lập đường cài được hỗ trợ cho Android bên thứ ba. [Hướng dẫn của Google](https://support.google.com/chrome_webstore/answer/2664769) cho điện thoại là Add to Desktop. Nếu không qua cổng, ghi không đạt và tiếp tục mốc MV3 local riêng; không giả danh desktop hoặc bỏ xác minh. |
| MV2 | Chưa thử. Chỉ xét nếu còn runtime được hỗ trợ trên revision vá; không hạ Chromium hoặc khôi phục đường đã bị gỡ. |
| ShardBrowser | Chưa phân tích engine. Launcher desktop cho thấy cơ chế thư mục riêng theo profile; chưa có mẫu định danh để kiểm chứng tracker hoặc quyền website trong engine. Không lấy engine làm phụ thuộc. |
| Hiệu năng | Chưa đo. Ưu tiên PSS, dung lượng APK và độ mượt, theo thứ tự đó. Chốt ngưỡng sau khi có baseline; không tối ưu bằng cách tắt sandbox hay site isolation. |
