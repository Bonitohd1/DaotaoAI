# THỰC HÀNH TÍCH HỢP FORM VÀO GOOGLE SHEETS

Đây là tài liệu code mẫu chuẩn form-to-sheet mà Antigravity sử dụng để thiết lập luồng giao tiếp.

## 1. Cấu hình Google Apps Script (Backend)

Tạo tệp Apps Script đính kèm Google Sheet, chèn đoạn mã sau để tiếp nhận dữ liệu POST:

```javascript
function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  
  // Ánh xạ tham số từ Form HTML sang mảng dữ liệu (dựa theo name="")
  var rowData = [
    e.parameter.name,
    e.parameter.email,
    e.parameter.phone,
    e.parameter.message,
    new Date() // Cột tự động lưu thời gian
  ];
  
  sheet.appendRow(rowData);
  
  // Trả về luồng text thông báo để tránh lỗi trắng trang
  return ContentService.createTextOutput("Success").setMimeType(ContentService.MimeType.TEXT);
}
```

*(Bắt buộc phải Deploy dưới dạng Web App và cấu hình Who has access = Anyone).*

## 2. Cấu hình Javascript (Frontend)

Đoạn mã Fetch API trên website (HTML) để truyền dữ liệu đi ngầm:

```javascript
  const scriptURL = 'YOUR_GOOGLE_SCRIPT_WEB_APP_URL';
  const form = document.getElementById('contactForm');

  form.addEventListener('submit', e => {
    e.preventDefault(); // Chặn hành động load trang mặc định
    
    // Gọi Webhook của GAS
    fetch(scriptURL, { 
        method: 'POST', 
        body: new FormData(form) 
    })
    .then(response => { 
        // Logic xử lý khi thành công (Ẩn form, hiện thông báo)
        console.log("Thành công!");
    })
    .catch(error => { 
        // Dữ liệu vẫn vào sheet thành công kể cả khi bắt gặp lỗi CORS của Google
        console.log("Đã gửi dữ liệu!");
    })
  });
```

Chỉ với 2 khối mã trên, ứng dụng web của bạn đã mang đủ sức mạnh của một hệ thống có CSDL độc lập!
