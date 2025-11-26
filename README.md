# PE FILE 
## I Giới thiệu
- PE-File là một công cụ đơn giản viết bằng Python (với các file pe_parser.py, pe_viewer.py,...) giúp phân tích cấu trúc file PE (Portable Executable) — các file .exe, .dll, .sys, v.v —nhằm hiển thị chi tiết header, sections, bảng import/export, metadata và các thông tin hữu ích khác.
### Mục đích của dự án:
- Giúp người học hiểu rõ cấu trúc PE file.
- Hỗ trợ phân tích tĩnh (static analysis) — hữu ích cho malware analysis, reverse engineering.
- Có thể dùng làm công cụ nền tảng để phát triển các tính năng nâng cao (ví dụ: phát hiện packed/obfuscated PE, kiểm tra import suspicious API, so sánh PE, …).
### Tính năng
- Đọc và parse các phần chính của PE: DOS Header, NT Headers (File Header + Optional Header), Section Headers.
- Liệt kê các section — tên, kích thước (raw/memory), permissions (read/write/execute), offset, RVA/VA.
- Hiển thị các Data Directory — Import Table, Export Table, Resource Table, Relocation, Debug, TLS, v.v nếu có.
- Liệt kê các hàm được import (Import Table), giúp phát hiện hàm API được sử dụng (có thể dùng để phát hiện hành vi nguy hiểm).
- Cho phép hiển thị metadata — Entry Point (RVA), Image Base, SizeOfImage, SizeOfHeaders, Characteristics (architecture 32/64-bit, EXE vs DLL), timestamp compile (TimeDateStamp) if available.
- Cho phép so sánh / kiểm tra bất thường: chẳng hạn section có entropy cao, hoặc section chưa khớp với layout điển hình => có thể là dấu hiệu của packer / mã hóa.

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
## V. Các thông tin quan trọng khi phân tích PE (Malware Analysis)
- Entry Point (EP) – địa chỉ mà chương trình bắt đầu thực thi.
- Image Base – địa chỉ trong memory mà PE dự kiến được load.
- Characteristics – flags chỉ định tính chất file (DLL, EXE, 32/64-bit, executable, system file…).
- Checksum – giá trị checksum của PE, thường dùng để phát hiện thay đổi.
- Import/Export Functions – các hàm được dùng/đưa ra; malware thường import các API nguy hiểm như CreateRemoteThread, VirtualAlloc.
- TLS Callbacks – malware có thể dùng Thread Local Storage để thực thi code trước khi entry point.
- Section Entropy – sections với entropy cao thường chứa dữ liệu nén hoặc mã obfuscate.

## Hướng dẫn sử dụng
- Clone repo:

git clone https://github.com/ShirokaitoX1412/PE-File.git

Xác định file PE cần phân tích (ví dụ somefile.exe).

Chạy tool — ví dụ:

python pe_viewer.py somefile.exe


Hoặc nếu dùng parser riêng:

python pe_parser.py somefile.exe


Xem thông tin header, section, import/export, metadata — để phục vụ phân tích tĩnh, kiểm tra suspicious API, unpack / detect packer, v.v.
## Ứng dụng & Mở rộng

Repo này có thể được dùng / mở rộng cho các mục đích sau:

Học về định dạng PE — hiểu rõ cấu trúc file thực thi Windows.

Phân tích malware tĩnh — detect suspicious API, tìm dấu hiệu packer, obfuscation.

Tool nền tảng để build công cụ nâng cao hơn: unpacker, PE sanitizer, scanner, compare/validate PE, export/import extractor, resource dumper, etc.

Nghiên cứu / thực hành reverse engineering, malware analysis, forensic trên Windows PE.

 ## Tài liệu tham khảo

Giới thiệu về cấu trúc PE file (DOS Header, NT Headers, Section Headers, Data Directory…) — bài viết “Cấu trúc file PE (Portable Executable)” trên AdminVietNam. 
Admin Viet Nam
+1

Tổng quan về các section phổ biến, cách loader nạp PE vào memory, relocation, import/export, resource, … 
HackMD
+1

Vai trò của các table như Import, Export, Relocation, Resource, Debug, TLS… trong phân tích PE / malware analysis. 
Antoan Thong Tin Hai Phong
+1
