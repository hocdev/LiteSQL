# LiteSQL 2025 - Portable SQL Server Manager

<p align="center">
  <a href="#english"><b>English</b></a> | <a href="#tiếng-việt"><b>Tiếng Việt</b></a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Rust-2021_Edition-orange?logo=rust" alt="Rust">
  <img src="https://img.shields.io/badge/GUI-Slint_UI-blue?logo=slint" alt="Slint">
  <img src="https://img.shields.io/badge/SQL_Server-2014_to_2025-red?logo=microsoftsqlserver" alt="SQL Server">
  <img src="https://img.shields.io/badge/Platform-Windows_10%20%7C%2011%20%7C%20Server-0078D6?logo=windows" alt="Platform">
  <img src="https://img.shields.io/badge/Architecture-x64-brightgreen" alt="Architecture">
  <img src="https://img.shields.io/badge/License-MIT-green" alt="License">
</p>

---

<a name="english"></a>
# English

## 🌟 Overview

**LiteSQL 2025** is an ultra-lightweight, high-performance, fully portable management tool and runtime launcher for **Microsoft SQL Server** (supporting versions from **2014 up to 2025 Express / Enterprise**). 

Built with **Rust** and **Slint GUI**, LiteSQL enables instant SQL Server deployment without the bloat and complexity of the standard Windows installer. Simply unzip, double-click, and run SQL Server anywhere (USB drives, portable folders, development workstations, or cloud VPS servers).

---

## ✨ Key Features

- 🚀 **100% Portable & Zero Installation**: No MSI installers, no registry leftovers, no background services permanently running when closed.
- 🔄 **Smart Auto-Relocation**: Moving the folder to a different path or drive? LiteSQL automatically detects directory changes and relocates all system databases (`master`, `model`, `msdb`, `tempdb`) using minimal maintenance mode before launching.
- 🛡️ **Windows Server 2012/2016/2019/2022 Ready**:
  - Full compatibility with strict security policies on Windows Server editions.
  - Automatic Inbound **Windows Firewall** management via `netsh` (automatically opens ports & engine binary).
  - Safe self-signed TLS generation to prevent RDP certificate conflicts (`TDSSNIClient 0x80092004`).
  - Automatic `CHECK_POLICY = OFF` handling for development passwords (e.g. `123456`) under Windows Server Password Complexity Policies.
- 🔌 **Full DBNETLIB / OLE DB / ODBC Client Compatibility**:
  - Automatically configures client TCP aliases (`ConnectTo`) across **HKCU**, **HKLM (64-bit)**, and **WOW6432Node (32-bit)**.
  - Resolves `.`, `(local)`, `localhost`, `127.0.0.1`, `<InstanceName>` to active TCP ports for legacy 32-bit clients (`isqlw.exe`, Query Analyzer 2000, game servers like Mu Online, VB6/ASP apps).
  - Configures ODBC Drivers and System DSNs for instant connection via SSMS, Navicat, Excel, and Access.
- 🗄️ **Built-in Database Management**:
  - Real-time database list with size (MB), status (`ONLINE`/`OFFLINE`), and file paths.
  - **Create Database**: Specify names and custom storage paths.
  - **Attach Database**: Fast attachment with single MDF or MDF+LDF pairs.
  - **Detach / Drop Database**: Safe detachment or forced single-user deletion.
  - **Rename Database**: Immediate T-SQL rename with connection resets.
  - **Backup & Restore (.BAK)**: Full database backups and smart RESTORE with automatic logical file relocation (`RESTORE FILELISTONLY` -> `MOVE`).
- ⚡ **Memory Cap Control**: Dynamic buffer pool memory limitation (`sp_configure 'max server memory (MB)'`).
- 🛠️ **One-Click Query Analyzer (`isqlw`)**: Automatically downloads and launches the classic SQL Server Query Analyzer with automatic SA authentication.
- 🌐 **Multi-Language Support**: English, Tiếng Việt (Vietnamese), and 简体中文 (Simplified Chinese) loaded dynamically via Lua bundles (`plugin/i18n.lua`).
- 📥 **Plugin Extensibility**: SQL Server version mirrors and download links easily managed through `plugin/download.lua`.

---

## 📥 Quick Start

### 1. Requirements
- **OS**: Windows 10, Windows 11, Windows Server 2012 / 2012 R2 / 2016 / 2019 / 2022 (x64).
- **Permissions**: Run as **Administrator** (required to configure SQL Server network ports, aliases, and registry parameters).

### 2. Running LiteSQL
1. Extract the `LiteSQL2025` folder to any directory (e.g., `D:\LiteSQL2025`).
2. Right-click `LiteSQL.exe` -> **Run as Administrator**.
3. Configure your preferences:
   - **Instance Name**: Default is `mssql`.
   - **TCP Port**: Default is `2433` (or `1433`).
   - **Login Mode**: Mixed Mode (SQL Server + Windows Authentication) or Windows Authentication.
4. Click **Start** to launch SQL Server.
5. Click **Query Analyzer** or connect from your favorite database tool (SSMS, Navicat, DBeaver, etc.) using:
   - **Server**: `127.0.0.1,2433` or `127.0.0.1` (via registered alias)
   - **Username**: `sa`
   - **Password**: `123456` (or your configured password)

---

## ⚙️ Configuration (`SConfig.ini`)

LiteSQL stores settings in `SConfig.ini` located in the root directory:

```ini
[Setting]
InstName=mssql            # SQL Server instance name
ServPort=2433             # TCP listening port
LoginMode=2               # 1 = Windows Auth, 2 = Mixed Mode
AutoRun=False             # Auto-start with Windows
AutoStart=True            # Automatically start SQL Server when LiteSQL opens
SqlAutoClose=True         # Automatically shutdown SQL Server when LiteSQL exits
Guard=False               # Auto-restart SQL Server on unexpected crash
Lang=en                   # Language: en | vi | zh
MaxMemory=0               # Max memory in MB (0 = unlimited)
```

---

## 🔨 Building from Source

### Prerequisites
- [Rust & Cargo](https://rustup.rs/) (version 1.80 or newer recommended)
- Windows SDK & MSVC Build Tools (Visual Studio 2019/2022 C++ build tools)

### Build Steps

```powershell
# 1. Clone repository
git clone https://github.com/hocdev/LiteSQL.git
cd LiteSQL/litesql-rust

# 2. Check code compilation
cargo check

# 3. Build optimized release binary
cargo build --release

# The compiled binary will be located at:
# target/release/LiteSQL.exe
```

---

## 📂 Project Structure

```
LiteSQL2025/
│
├── LiteSQL.exe                 # Main portable executable
├── SConfig.ini                 # Application configuration
├── assets/
│   └── screenshot.png          # UI Screenshot
├── MSSQL/                      # SQL Server Binaries & Data
│   ├── Binn/                   # sqlservr.exe and core engine DLLs
│   ├── DATA/                   # MDF and LDF database files
│   ├── Log/                    # ERRORLOG and event traces
│   ├── Backup/                 # Default backup folder (.BAK)
│   └── isqlw/                  # Query Analyzer tool
├── plugin/                     # Extensible Lua plugins
│   ├── download.lua            # CDN download links & SQL versions
│   └── i18n.lua                # Multilingual translations (VI, EN, ZH)
└── litesql-rust/               # Rust source code
    ├── Cargo.toml
    ├── build.rs
    ├── src/
    │   ├── main.rs             # Application entrypoint & single instance mutex
    │   ├── engine.rs           # SQL Server process runner & firewall rules
    │   ├── registry.rs         # Win32 registry, ODBC & DBNETLIB aliases
    │   ├── sql_client.rs       # Async TDS client (Tiberius) for DB management
    │   ├── net_helper.rs       # LAN IP detection & TCP table inspection
    │   ├── tray.rs             # Native Win32 system tray message pump
    │   ├── ui_slint.rs         # Slint UI callbacks and business logic
    │   ├── installer.rs        # Auto-download & zip extractor for tools
    │   └── i18n.rs             # Embedded Lua translation loader
    └── ui/
        └── appwindow.slint     # Modern Slint GUI layout
```

---

## 🔧 Troubleshooting

### 1. `[DBNETLIB][ConnectionOpen (Connect()).]SQL Server does not exist or access denied`
- **Cause**: Legacy 32-bit apps cannot resolve the custom port, TLS certificate conflict on Windows Server, or Windows Firewall blocked the connection.
- **Solution**: LiteSQL automatically registers 32-bit WOW6432Node aliases, enables safe self-signed TLS certificates, and configures Windows Firewall rules. Ensure you are running LiteSQL as **Administrator** so it can write registry aliases.

### 2. Cannot connect with `sa` password on Windows Server
- **Cause**: Windows Server enforces strict password complexity.
- **Solution**: LiteSQL automatically executes `CHECK_POLICY = OFF` when configuring the SA password. If manually creating logins, ensure password policies are disabled for local development.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---
---

<a name="tiếng-việt"></a>
# Tiếng Việt

[Quay lại đầu trang / Back to top](#litesql-2025---portable-sql-server-manager)

## 🌟 Giới thiệu

**LiteSQL 2025** là công cụ quản lý và môi trường khởi chạy **Microsoft SQL Server** hoàn toàn Portable, siêu nhẹ và đạt hiệu năng tối đa (hỗ trợ các phiên bản từ **SQL Server 2014 đến 2025 Express / Enterprise**).

Được viết bằng ngôn ngữ **Rust** và xây dựng giao diện hiện đại với **Slint GUI**, LiteSQL giúp bạn triển khai SQL Server ngay lập tức mà không cần cài đặt bộ cài nặng nề của Windows. Chỉ cần giải nén và nhấp đúp chuột là có thể chạy SQL Server ở bất cứ đâu (ổ cứng di động, USB, máy trạm phát triển phần mềm hoặc máy chủ VPS Windows Server).

---

## ✨ Tính năng nổi bật

- 🚀 **100% Portable & Không cần cài đặt**: Không dùng trình cài đặt MSI rườm rà, không để lại rác trong Registry, không chạy ngầm dịch vụ Windows khi đóng ứng dụng.
- 🔄 **Tự động dời đường dẫn thông minh (Auto-Relocation)**: Khi bạn sao chép hoặc chuyển thư mục sang ổ đĩa khác, LiteSQL sẽ tự động phát hiện và cập nhật lại đường dẫn vật lý của tất cả database hệ thống (`master`, `model`, `msdb`, `tempdb`) ở chế độ bảo trì tối thiểu trước khi khởi động.
- 🛡️ **Hỗ trợ toàn diện Windows Server 2012 / 2016 / 2019 / 2022**:
  - Tương thích hoàn toàn với các chính sách bảo mật khắt khe trên các bản Windows Server.
  - Tự động cấu hình **Windows Firewall** mở cổng TCP và cho phép file `sqlservr.exe` kết nối mạng.
  - Cơ chế tạo chứng chỉ TLS tự ký an toàn, loại bỏ lỗi xung đột chứng chỉ Remote Desktop RDP (`TDSSNIClient error 0x80092004`).
  - Tự động áp dụng `CHECK_POLICY = OFF` cho mật khẩu tài khoản `sa` (ví dụ: `123456`) để tránh bị chính sách mật khẩu phức tạp của Windows Server từ chối.
- 🔌 **Tương thích toàn diện DBNETLIB / OLE DB / ODBC**:
  - Tự động đăng ký Client TCP Aliases (`ConnectTo`) trên cả **HKCU**, **HKLM (64-bit)** và **WOW6432Node (32-bit)**.
  - Phân giải các chuỗi kết nối như `.`, `(local)`, `localhost`, `127.0.0.1`, `<Tên_Instance>` về đúng cổng TCP cho các ứng dụng 32-bit truyền thống (`isqlw.exe`, Query Analyzer 2000, các game server như Mu Online, ứng dụng VB6/ASP).
  - Tự động thiết lập ODBC Drivers và System DSN giúp kết nối ngay lập tức từ SSMS, Navicat, Excel và Access.
- 🗄️ **Quản lý Database tích hợp**:
  - Hiển thị danh sách CSDL theo thời gian thực kèm dung lượng (MB), trạng thái (`ONLINE`/`OFFLINE`), và đường dẫn file vật lý.
  - **Tạo Database mới**: Đặt tên và chọn thư mục lưu trữ tùy ý.
  - **Attach Database**: Đính kèm database nhanh chóng từ file MDF hoặc cặp MDF + LDF.
  - **Detach / Xóa (Drop) Database**: Gỡ bỏ hoặc xóa database an toàn với cơ chế buộc ngắt kết nối `SINGLE_USER`.
  - **Đổi tên Database**: Đổi tên CSDL bằng lệnh T-SQL tức thì.
  - **Sao lưu & Phục hồi (.BAK)**: Tạo file backup nhanh và khôi phục CSDL thông minh tự động dời file logic (`RESTORE FILELISTONLY` -> `MOVE`).
- ⚡ **Giới hạn RAM linh hoạt**: Thiết lập mức RAM tối đa cho buffer pool SQL Server (`sp_configure 'max server memory (MB)'`).
- 🛠️ **Mở nhanh Query Analyzer (`isqlw`)**: Tự động tải về và mở công cụ Query Analyzer cổ điển với cơ chế tự đăng nhập tài khoản `sa`.
- 🌐 **Đa ngôn ngữ**: Hỗ trợ sẵn Tiếng Việt, Tiếng Anh và Tiếng Trung Quốc tải động qua file Lua (`plugin/i18n.lua`).
- 📥 **Mở rộng qua Plugin**: Dễ dàng tùy biến link tải và các phiên bản SQL Server qua `plugin/download.lua`.

---

## 📥 Hướng dẫn sử dụng nhanh

### 1. Yêu cầu hệ thống
- **Hệ điều hành**: Windows 10, Windows 11, Windows Server 2012 / 2012 R2 / 2016 / 2019 / 2022 (bản 64-bit).
- **Quyền hạn**: Chạy dưới quyền **Administrator** (cần thiết để cấu hình mạng, cổng TCP, Firewall và Registry của SQL Server).

### 2. Các bước khởi chạy
1. Giải nén thư mục `LiteSQL2025` vào bất kỳ thư mục nào (ví dụ: `D:\LiteSQL2025`).
2. Nhấp chuột phải vào `LiteSQL.exe` -> chọn **Run as Administrator**.
3. Lựa chọn cấu hình mong muốn:
   - **Tên Instance**: Mặc định là `mssql`.
   - **Cổng TCP**: Mặc định là `2433` (hoặc `1433`).
   - **Chế độ đăng nhập (Login Mode)**: Mixed Mode (SQL Server + Windows Auth) hoặc Windows Auth.
4. Nhấn **Start** để khởi chạy SQL Server.
5. Nhấn nút **Query Analyzer** hoặc kết nối từ các công cụ quản trị (SSMS, Navicat, DBeaver, v.v.) với thông số:
   - **Máy chủ (Server)**: `127.0.0.1,2433` hoặc `127.0.0.1` (nhờ alias tự động)
   - **Tài khoản**: `sa`
   - **Mật khẩu**: `123456` (hoặc mật khẩu do bạn thiết lập)

---

## ⚙️ Cấu hình (`SConfig.ini`)

LiteSQL lưu trữ các thiết lập trong file `SConfig.ini` tại thư mục gốc:

```ini
[Setting]
InstName=mssql            # Tên Instance của SQL Server
ServPort=2433             # Cổng mạng TCP lắng nghe
LoginMode=2               # 1 = Windows Auth, 2 = Mixed Mode
AutoRun=False             # Khởi động cùng Windows
AutoStart=True            # Tự động chạy SQL Server khi mở LiteSQL
SqlAutoClose=True         # Tự động tắt SQL Server khi đóng LiteSQL
Guard=False               # Tự động khởi động lại nếu SQL Server bị văng bất ngờ
Lang=vi                   # Ngôn ngữ giao diện: vi | en | zh
MaxMemory=0               # Giới hạn RAM tối đa (MB), 0 = không giới hạn
```

---

## 🔨 Hướng dẫn biên dịch từ mã nguồn (Build from Source)

### Điều kiện tiên quyết
- [Rust & Cargo](https://rustup.rs/) (khuyến nghị phiên bản 1.80 trở lên)
- Windows SDK & MSVC Build Tools (Visual Studio C++ build tools)

### Các bước thực hiện

```powershell
# 1. Clone kho mã nguồn
git clone https://github.com/hocdev/LiteSQL.git
cd LiteSQL/litesql-rust

# 2. Kiểm tra biên dịch mã nguồn
cargo check

# 3. Build bản phát hành tối ưu
cargo build --release

# File thực thi đầu ra sẽ nằm tại:
# target/release/LiteSQL.exe
```

---

## 📂 Cấu trúc thư mục dự án

```
LiteSQL2025/
│
├── LiteSQL.exe                 # File thực thi chính của ứng dụng
├── SConfig.ini                 # File cấu hình ứng dụng
├── assets/
│   └── screenshot.png          # Ảnh chụp màn hình giao diện
├── MSSQL/                      # Bộ nhị phân & dữ liệu của SQL Server
│   ├── Binn/                   # File sqlservr.exe và các thư viện lõi
│   ├── DATA/                   # Các file cơ sở dữ liệu MDF và LDF
│   ├── Log/                    # File nhật ký ERRORLOG
│   ├── Backup/                 # Thư mục sao lưu mặc định (.BAK)
│   └── isqlw/                  # Công cụ SQL Query Analyzer
├── plugin/                     # Các plugin mở rộng bằng Lua
│   ├── download.lua            # Link tải và danh sách phiên bản SQL Server
│   └── i18n.lua                # Bộ từ điển đa ngôn ngữ (VI, EN, ZH)
└── litesql-rust/               # Mã nguồn viết bằng Rust
    ├── Cargo.toml
    ├── build.rs
    ├── src/
    │   ├── main.rs             # Điểm khởi đầu ứng dụng & Mutex đơn phiên
    │   ├── engine.rs           # Điều khiển tiến trình SQL & quản lý Firewall
    │   ├── registry.rs         # Win32 Registry, ODBC & DBNETLIB Aliases
    │   ├── sql_client.rs       # Client TDS bất đồng bộ (Tiberius) quản lý DB
    │   ├── net_helper.rs       # Nhận diện IP LAN & bảng kết nối TCP
    │   ├── tray.rs             # Khay hệ thống Windows thuần Win32
    │   ├── ui_slint.rs         # Xử lý giao diện người dùng Slint
    │   ├── installer.rs        # Tự động tải và giải nén công cụ
    │   └── i18n.rs             # Trình nạp ngôn ngữ nhúng Lua
    └── ui/
        └── appwindow.slint     # File thiết kế giao diện Slint
```

---

## 🔧 Xử lý sự cố thường gặp (Troubleshooting)

### 1. `[DBNETLIB][ConnectionOpen (Connect()).]SQL Server 不存在或拒绝访问。`
- **Nguyên nhân**: Ứng dụng 32-bit không nhận diện được cổng tùy chỉnh, xung đột chứng chỉ TLS trên Windows Server, hoặc bị Windows Firewall chặn.
- **Cách khắc phục**: LiteSQL đã tích hợp tự động đăng ký alias cho 32-bit WOW6432Node, tự tạo chứng chỉ self-signed TLS và tự mở Windows Firewall. Bạn chỉ cần đảm bảo luôn chạy ứng dụng bằng quyền **Administrator**.

### 2. Không kết nối được bằng tài khoản `sa` trên Windows Server
- **Nguyên nhân**: Windows Server bật sẵn chính sách bảo mật bắt buộc mật khẩu phức tạp.
- **Cách khắc phục**: LiteSQL đã tự động áp dụng cờ `CHECK_POLICY = OFF` khi đồng bộ mật khẩu `sa`.

---

## 📄 Giấy phép (License)

Dự án này được phân phối theo giấy phép [MIT License](LICENSE).

---

<p align="center">
  Phát triển với ❤️ bằng <b>Rust</b> & <b>Slint</b>
</p>
