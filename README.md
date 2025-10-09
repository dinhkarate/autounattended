## Chức năng của script này
* Tự động điền ngôn ngữ, múi giờ, layout bàn phím
* Tự động nhập tên người dùng, mật khẩu Administrator
* Tự động chia phân vùng ổ đĩa, chọn ổ cài đặt, bỏ qua màn hình OOBE (Out-of-box experience)
* Tự động tham gia domain, cài đặt drivers, chạy scripts sau khi cài

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

## Hướng dẫn modify một số trường
### Index của image
Cho ai không biết thì index của file ISO là cái này

![image.png](https://images.viblo.asia/3dbba2ac-9d2c-4ee6-8dcf-62c10526f2e9.png)

Mình luôn cài bản số 4 là Datacenter (Desktop Experience), ứng với số index bên dưới, bạn có thể thay đổi để phù hợp.

```xml
            <MetaData wcm:action="add">
              <Key>/IMAGE/INDEX</Key>
              <Value>4</Value>
            </MetaData>
```

### Password mặc định cho Administrator
Ở đây mình đang đặt là ``admin``, bạn có thể thay đổi theo trường mà mình muốn
```powershell
      <UserAccounts>
        <AdministratorPassword>
          <Value>admin</Value>
          <PlainText>true</PlainText>
        </AdministratorPassword>
      </UserAccounts>
      <AutoLogon>
        <Username>Administrator</Username>
        <Password>
          <Value>admin</Value>
          <PlainText>true</PlainText>
        </Password>
        <Enabled>true</Enabled>
        <LogonCount>1</LogonCount>
      </AutoLogon>
```
### Chạy script Powershell sau khi cài đặt
Có một đoạn như thế này, bình thường mình sẽ sử dụng ``irm link | iex`` để tiến hành cài đặt, bạn có thể modify lại nếu muốn. còn không thì nên giữ nguyên thì hơn
```powershell
          <CommandLine>powershell -ExecutionPolicy Bypass -Command "echo Hello Powershell"</CommandLine>
```

## Link Script
Mình để link script ở repo này: https://github.com/dinhkarate/autounattended

## Lưu ý
* Script này chỉ hoạt động ở các bản **Windows Server Evaluation**, nếu sử dụng Windows 10, Windows 11 và các bản Windows Server khác => bạn có thể tham khảo bên [đây](https://github.com/memstechtips/UnattendedWinstall)
* Được thiết kế riêng cho Windows 2022 nên hoạt động tốt nhất trên Windows Server 2022
