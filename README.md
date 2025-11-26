# PE (Portable Executable) Format

## I. Khái niệm PE (Portable Executable)

PE file là định dạng file được Windows sử dụng cho các file thực thi như **.exe, .dll, .sys, ...**

PE Header chứa tất cả thông tin cần thiết để hệ điều hành nạp chương trình vào bộ nhớ và thực thi nó.

---

## II. Thành phần PE Header

| Thành phần          | Mô tả                                                         |
|---------------------|---------------------------------------------------------------|
| **MS-DOS Header**   | Header của chương trình DOS cũ (chủ yếu để tương thích)      |
| **MS-DOS Stub**     | Một đoạn code in thông báo: *"This program cannot be run in DOS mode."* |
| **PE Signature**    | Dấu hiệu nhận diện file PE (4 bytes: `0x50 0x45 0x00 0x00`)  |
| **PE File Header**  | Thông tin cơ bản về file (số lượng sections, thời gian tạo, flags) |
| **Optional Header** | Thông tin cần thiết để loader thực thi chương trình          |
| **Section Table**   | Mô tả các section                                             |
| **Sections**        | Thực tế dữ liệu (mã máy, dữ liệu, tài nguyên...)             |

---

## III. Các thành phần chi tiết

### 1. MS-DOS Header (64 bytes)

**Chứa:**

- Magic number: `MZ` (`0x4D5A`)
- `e_lfanew` (offset `0x3C`) → trỏ tới PE Header

```c
typedef struct _IMAGE_DOS_HEADER { 
    WORD e_magic;      // MZ = 0x5A4D
    WORD e_cblp;       // Bytes on last page

###2. PE Signature
    ...
    LONG e_lfanew;     // Offset to PE header
} IMAGE_DOS_HEADER;
