# FileSystemExplorer

**开发单位**：广州智皓计算机技术有限公司  
**主要开发者**：JavingLee (javinglee@qq.com)

---

FileSystemExplorer 是一款面向 Windows 平台的专业数据恢复与取证分析软件，采用 GTK4 + Rust 构建。软件以只读方式访问原始存储设备，避免对源数据造成二次损坏。

## 主要功能

### 设备支持
- 物理硬盘、逻辑盘符（C:、D: 等）
- 虚拟磁盘镜像：VMDK、VHD、VHDX、QCOW2、RAW/IMG
- LDM 动态磁盘、Windows Storage Spaces

### 文件系统支持（17 种）
- **Windows**：NTFS、FAT12/16/32、exFAT、ReFS
- **Linux**：EXT2/3/4、XFS、Btrfs、ZFS、F2FS
- **Apple**：HFS+、APFS
- **Unix**：UFS、ISO 9660
- **嵌入式/其他**：UBIFS、SquashFS、JFFS2、HMDFS（鸿蒙）

### 数据恢复
- 快速扫描（基于元数据）与深度扫描（基于签名+片段分析）
- 文件雕刻：PDF、PNG、ZIP
- 片段重组：内容策略 / 元数据策略 / 混合策略
- NTFS USN 日志分析、VSS 卷影复制
- APFS / ZFS 事务（世代）访问
- 断点续扫（SSDB 扫描状态数据库）

### RAID 重建
- 支持 RAID 0/1/5/6/10/1E/JBOD
- 自动检测 RAID 配置（熵分析、元数据、签名、统计策略）
- 支持设置磁盘起始扇区偏移
- 重建后作为虚拟设备直接浏览和恢复
- 内置 RAID 计算器

### 磁盘镜像
- 创建 RAW、E01/Ex01、VHDX、VMDK 镜像
- 支持加载已有镜像进行分析
- 高级选项：正向/反向读取、并行模式、直接 SCSI、跳过空扇区
- 坏扇区处理：填充零并继续 / 跳过 / 中止
- 哈希校验：MD5 / SHA1 / SHA2 / SHA3 / CRC32
- E01 支持案件元数据嵌入

### 界面与预览
- 文件浏览器：树形目录 + 文件列表 + 预览区
- 文件预览：文本、图片（支持双击全尺寸查看）、ICO
- 十六进制查看器：支持搜索历史、多编码、多实例同步
- 15 个内置主题（含 LiquidGlass 等特效主题）

## 运行要求

- **操作系统**：Windows 10/11 或 Windows Server 2016+
- **权限**：访问物理磁盘需要管理员权限，软件启动时会自动通过 UAC 申请提升
- **运行环境**：可执行文件同级目录需包含 `runtime/` 文件夹（安装包已自动包含）

## 安装与部署

- 图形安装：运行 `FileSystemExplorer-Setup-*.exe` 按向导安装
- 静默安装：`FileSystemExplorer-Setup-*.exe /VERYSILENT /SUPPRESSMSGBOXES /NORESTART`
- 安装后自检：`FileSystemExplorer.exe --self-check --self-check-log="C:\path\check.log"`

详细说明请参阅：
- [用户手册](docs/USER_MANUAL.md)
- [部署说明](docs/DEPLOYMENT.md)
- [更新日志](CHANGELOG.md)

## 开发构建

```bash
# 调试构建（显示控制台窗口）
cargo build

# 调试运行
cargo run

# 发布构建（无控制台窗口，/SUBSYSTEM:WINDOWS）
cargo build --release

# 测试
cargo test --all

# 代码格式化与检查
cargo fmt
cargo clippy --all-targets --all-features

# 客户/授权版本（启用 RSA 授权、AES-GCM、试用模式）
cargo build --features customer

# 禁止构建计数器自动递增
FSX_NO_VERSION_BUMP=1 cargo build
```

## 主要依赖

| 类别 | Crate | 用途 |
|------|-------|------|
| **异步运行时** | [tokio](https://crates.io/crates/tokio) 1.35 | 全局异步运行时（全特性） |
| **并发** | [rayon](https://crates.io/crates/rayon) 1.8 | CPU 密集型任务并行 |
| | [crossbeam](https://crates.io/crates/crossbeam) 0.8 | 无锁数据结构、通道 |
| **GUI** | [gtk4](https://crates.io/crates/gtk4) 0.7 | GTK4 图形界面 |
| **Windows API** | [windows](https://crates.io/crates/windows) 0.62 | Windows 原生 API（IOCTL、NVMe、WMI 等） |
| | [wmi](https://crates.io/crates/wmi) 0.14 | WMI 查询 |
| **文件系统** | [ntfs](https://crates.io/crates/ntfs) 0.4 / ntfs-reader 0.4 | NTFS 解析 |
| **压缩** | [flate2](https://crates.io/crates/flate2) 1.0 | Gzip/Deflate |
| | [lz4](https://crates.io/crates/lz4) 1.28 / lz4_flex 0.13 | LZ4 解压 |
| | [lzma-rs](https://crates.io/crates/lzma-rs) 0.3 | LZMA/XZ |
| | [zstd](https://crates.io/crates/zstd) 0.13 | Zstandard |
| **序列化** | [serde](https://crates.io/crates/serde) 1.0 / serde_json 1.0 | 配置与数据序列化 |
| | [bincode](https://crates.io/crates/bincode) 1.3 | 二进制序列化（扫描状态） |
| **哈希校验** | [md-5](https://crates.io/crates/md-5) 0.10 / [sha1](https://crates.io/crates/sha1) 0.10 / [sha2](https://crates.io/crates/sha2) 0.10 / [sha3](https://crates.io/crates/sha3) 0.10 / [crc32fast](https://crates.io/crates/crc32fast) 1.3 | 镜像哈希 |
| **网络** | [tokio-tungstenite](https://crates.io/crates/tokio-tungstenite) 0.21 | WebSocket（HMDFS 远程访问） |
| | [reqwest](https://crates.io/crates/reqwest) 0.11 | HTTP 客户端 |
| **授权（customer feature）** | [rsa](https://crates.io/crates/rsa) 0.9 | RSA 签名验证 |
| | [aes-gcm](https://crates.io/crates/aes-gcm) 0.10 | AES-GCM 加密 |
| **其他** | [memmap2](https://crates.io/crates/memmap2) 0.9 | 内存映射 |
| | [chrono](https://crates.io/crates/chrono) 0.4 | 日期时间处理 |
| | [uuid](https://crates.io/crates/uuid) 1.6 | UUID 生成 |
| | [byteorder](https://crates.io/crates/byteorder) 1.5 | 字节序转换 |

## 架构要点

- **GTK4** 主线程运行 UI，更新通过 `glib::spawn_future_local`
- **单一全局 Tokio 运行时**（`src/runtime.rs`），禁止在其他地方创建独立运行时
- **I/O 调度器**（`src/core/io_scheduler.rs`，4 线程池）处理所有设备 I/O
- **rayon** 用于 CPU 密集型工作（扫描、片段重组、RAID 奇偶校验）
- **只读原则**：所有源设备操作必须通过 `Device::read_sector()` 等只读抽象

## 文档索引

| 文档 | 说明 |
|------|------|
| [docs/USER_MANUAL.md](docs/USER_MANUAL.md) | 用户使用手册 |
| [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md) | 静默安装/卸载与自检 |
| [CHANGELOG.md](CHANGELOG.md) | 版本更新日志 |
| [CLAUDE.md](CLAUDE.md) | 项目架构与开发规范（面向开发者） |
| [docs/TEST_MATRIX_ReFS.md](docs/TEST_MATRIX_ReFS.md) | ReFS 测试矩阵 |

---

**当前版本**：0.3.9
