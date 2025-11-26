PE (Portable Executable) Format
I. Khái niệm PE (Portable Executable)

PE file là định dạng file được Windows sử dụng cho các file thực thi như .exe, .dll, .sys, ...

PE header chứa tất cả thông tin cần thiết để hệ điều hành nạp chương trình vào bộ nhớ và thực thi nó.

Thành phần PE Header
Thành phần	Mô tả
1. MS-DOS Header	Phần header của chương trình DOS cũ (chủ yếu để tương thích)
2. MS-DOS Stub	Một đoạn code in thông báo: "This program cannot be run in DOS mode."
3. PE Signature	Dấu hiệu nhận diện file PE (4 bytes: 0x50 0x45 0x00 0x00)
4. PE File Header	Thông tin cơ bản về file (số lượng sections, thời gian tạo, flags)
5. Optional Header	Thông tin cần thiết để loader thực thi chương trình
6. Section Table	Mô tả các section
7. Sections	Thực tế dữ liệu (mã máy, dữ liệu, tài nguyên...)
II. Các thành phần chi tiết
1. MS-DOS Header (64 bytes)

Chứa:

Magic number MZ (0x4D5A)

e_lfanew (offset 0x3C) → trỏ tới PE Header

typedef struct _IMAGE_DOS_HEADER { 
    WORD e_magic;      // MZ = 0x5A4D
    WORD e_cblp;       // Bytes on last page
    ...
    LONG e_lfanew;     // Offset to PE header
} IMAGE_DOS_HEADER;

2. PE Signature

Giá trị cố định: "PE\0\0" (50 45 00 00h)

3. COFF File Header (PE File Header)

Thông tin về file thực thi:

typedef struct _IMAGE_FILE_HEADER {
    WORD  Machine;              // Kiến trúc CPU (0x14C cho x86)
    WORD  NumberOfSections;     // Số lượng section
    DWORD TimeDateStamp;        // Thời gian tạo
    DWORD PointerToSymbolTable; // Không dùng trong file PE
    DWORD NumberOfSymbols;      // Không dùng trong file PE
    WORD  SizeOfOptionalHeader; // Kích thước của Optional Header
    WORD  Characteristics;      // Các flag: EXE, DLL, 32-bit, ...
} IMAGE_FILE_HEADER;

Trường	Vai trò
Machine	Xác định CPU (0x14C = Intel 386)
NumberOfSections	Số lượng section
TimeDateStamp	Thời gian file được tạo
Characteristics	Cờ xác định loại file
4. Optional Header

(Rất quan trọng đối với loader.)

typedef struct _IMAGE_OPTIONAL_HEADER {
    WORD Magic;                  // 0x10B cho PE32
    BYTE MajorLinkerVersion;
    BYTE MinorLinkerVersion;
    DWORD SizeOfCode;
    DWORD AddressOfEntryPoint;    // Điểm bắt đầu thực thi
    DWORD BaseOfCode;
    DWORD BaseOfData;
    DWORD ImageBase;              // Địa chỉ load mặc định
    DWORD SectionAlignment;       // Căn chỉnh trong bộ nhớ
    DWORD FileAlignment;          // Căn chỉnh trong file
    ...
    IMAGE_DATA_DIRECTORY DataDirectory[16]; // Import, Export, Resource,...
} IMAGE_OPTIONAL_HEADER32;

Trường	Vai trò
Magic	0x10B (PE32) hoặc 0x20B (PE32+)
AddressOfEntryPoint	Entry Point
ImageBase	Địa chỉ cơ sở của image
SectionAlignment	Căn chỉnh section trong RAM
FileAlignment	Căn chỉnh section trong file
DataDirectory	Bảng Import, Export, Resource,…
5. Section Table (Section Headers)
typedef struct _IMAGE_SECTION_HEADER {
    BYTE Name[8];
    DWORD VirtualSize;
    DWORD VirtualAddress;
    DWORD SizeOfRawData;
    DWORD PointerToRawData;
    DWORD Characteristics;  // Quyền R/W/X
} IMAGE_SECTION_HEADER;

Section	Vai trò
.text	Mã lệnh
.data	Dữ liệu có thể thay đổi
.rdata	Dữ liệu chỉ đọc
.rsrc	Tài nguyên (icon, ảnh, ...)
III. Cách Windows Loader nạp chương trình

Windows xử lý PE như sau:

Đọc MS-DOS Header → tìm offset e_lfanew

Xác nhận PE Signature "PE\0\0"

Đọc COFF File Header → số section

Đọc Optional Header, gồm:

ImageBase

AddressOfEntryPoint

SectionAlignment, FileAlignment

DataDirectory → Import Table

Tạo vùng bộ nhớ tại ImageBase

Nạp từng section

Xử lý Relocation nếu ImageBase thay đổi

Load DLL từ Import Table

Nhảy đến EntryPoint để thực thi

IV. Các thành phần quan trọng cần lưu ý

e_lfanew: Offset PE Header

PE Signature: "PE\0\0"

NumberOfSections

ImageBase

AddressOfEntryPoint

DataDirectory (Import/Export/Resource/Relocation)

Các section: .text, .data, .rdata, .rsrc

Relocation Table

Import Table
