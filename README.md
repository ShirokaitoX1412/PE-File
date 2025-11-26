# PE FILE 
## I. Khái niệm PE (Portable Executable)
- PE file là định dạng file được Windows sử dụng cho các file thực thi như .exe, .dll, .sys, .ocx...
- PE Header chứa tất cả thông tin cần thiết để hệ điều hành nạp chương trình vào bộ nhớ và thực thi nó, bao gồm cấu trúc sections, entry point, và các dữ liệu quan trọng khác.

## II. Thành phần PE Header

- MS-DOS Header	Header của chương trình DOS cũ, chủ yếu để tương thích. Chứa trường e_lfanew chỉ tới PE Header.
- MS-DOS Stub	Một đoạn code DOS nhỏ in thông báo: "This program cannot be run in DOS mode."
- PE Signature	Dấu hiệu nhận diện file PE (4 bytes: 0x50 0x45 0x00 0x00)
- PE File Header	Thông tin cơ bản về file: số lượng section, thời gian tạo, machine type, characteristics, ...
- Optional Header	Thông tin cần thiết để loader thực thi chương trình: entry point, base address, image size, stack/heap size, dữ liệu quan trọng khác như Import Table, Export Table, Resource Table,...
- Section Table (Section Headers)	Mô tả các section: tên section, kích thước trong file và memory, đặc tính (read/write/execute), offset đến dữ liệu thực tế.
- Sections	Thực tế dữ liệu: mã máy (.text), dữ liệu (.data), tài nguyên (.rsrc), bảng import/export, debug info, ...
## III. Các section thường gặp trong PE
- .text	Chứa mã máy thực thi (executable code).
- .data	Chứa dữ liệu khởi tạo (initialized data).
- .rdata	Chứa dữ liệu chỉ đọc, ví dụ như chuỗi, bảng import/export.
- .bss	Chứa dữ liệu chưa khởi tạo (uninitialized data).
- .rsrc	Chứa tài nguyên như icon, menu, dialog, version info, ...
- .reloc	Chứa thông tin relocation để loader điều chỉnh địa chỉ khi nạp file ở memory khác.
- .idata	Chứa Import Table: thông tin DLL và hàm được import.
- .edata	Chứa Export Table: các hàm xuất ra nếu là DLL.
- .tls	Chứa Thread Local Storage nếu chương trình sử dụng TLS.
## IV. Các bảng dữ liệu quan trọng trong Optional Header
- Import Table	Liệt kê các DLL và hàm mà file PE sử dụng.
- Export Table	Liệt kê các hàm mà DLL xuất ra.
- Resource Table	Chứa thông tin về icon, menu, dialog, version, bitmap,...
- Relocation Table	Thông tin điều chỉnh địa chỉ khi load file tại base address khác.
- Exception Table	Thông tin xử lý ngoại lệ (exception handling) nếu có.
- Debug Table	Chứa thông tin debug (PDB path, symbols).
- Load Config Table	Chứa thông tin bảo mật và cấu hình loader.
- ound Import Table	Thông tin import đã được liên kết sẵn (binding).
V. Các thông tin quan trọng khi phân tích PE (Malware Analysis)
- Entry Point (EP) – địa chỉ mà chương trình bắt đầu thực thi.
- Image Base – địa chỉ trong memory mà PE dự kiến được load.
- Characteristics – flags chỉ định tính chất file (DLL, EXE, 32/64-bit, executable, system file…).
- Checksum – giá trị checksum của PE, thường dùng để phát hiện thay đổi.
- Import/Export Functions – các hàm được dùng/đưa ra; malware thường import các API nguy hiểm như CreateRemoteThread, VirtualAlloc.
- TLS Callbacks – malware có thể dùng Thread Local Storage để thực thi code trước khi entry point.
- Section Entropy – sections với entropy cao thường chứa dữ liệu nén hoặc mã obfuscate.
