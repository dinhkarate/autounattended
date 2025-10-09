## Cách sử dụng file autounattend.xml
### Cách 1: Sử dụng USB boot

1. Chuẩn bị USB bootable với Windows ISO.
2. Gắn USB và copy file `autounattend.xml` vào thư mục gốc (root) của USB — không đặt trong thư mục con.

Cấu trúc thư mục USB nên như sau:

```
USB:\
├── autounattend.xml
├── setup.exe
├── sources\
├── boot\
```

3. Boot máy từ USB.
4. Quá trình cài đặt Windows sẽ tự động đọc file `autounattend.xml` và bắt đầu cài đặt mà bạn chỉ cần ngồi rung đùi chờ.

### Cách 2: Chỉnh sửa file ISO

1. Bung (extract) file ISO của Windows ra một thư mục trên máy tính.
2. Copy file `autounattend.xml` vào thư mục gốc của ISO đã bung (thường là thư mục chứa `setup.exe`).
3. Sử dụng công cụ `oscdimg.exe` (từ [Windows ADK](https://learn.microsoft.com/vi-vn/windows-hardware/get-started/adk-servicing)) để pack lại thành file ISO mới.

Ví dụ lệnh pack lại:

```
oscdimg.exe -m -o -u2 -udfver102 -bootdata:2#p0,e,b<boot_folder>\etfsboot.com#pEF,e,b<boot_folder>\efisys.bin <source_folder> <output_iso>
```

Trong đó:
- `<boot_folder>`: Thư mục boot trong ISO đã bung.
- `<source_folder>`: Thư mục chứa nội dung ISO đã bung và file `autounattend.xml`.
- `<output_iso>`: Đường dẫn file ISO mới.

4. Sử dụng file ISO mới để cài đặt Windows, có thể tạo Rufus, Ventoy hoặc đưa vào Proxmox, VM
