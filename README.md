# FileSystemExplorer

**开发单位**：广州智皓计算机技术有限公司  
**主要开发者**：JavingLee (javinglee2025@users.noreply.github.com)

---

FileSystemExplorer 是一款面向 Windows 平台的专业数据恢复与取证分析软件，采用 GTK4 + Rust 构建。软件以只读方式访问原始存储设备，避免对源数据造成二次损坏。

## 主要功能

### 设备支持
- 物理硬盘、逻辑盘符（C:、D: 等）
- 虚拟磁盘镜像：VMDK、VHD、VHDX、QCOW2、RAW/IMG
- LDM 动态磁盘、Windows Storage Spaces

### 文件系统支持（19 种）
- **Windows**：NTFS、FAT12/16/32、exFAT、ReFS
- **Linux**：EXT2/3/4、XFS、Btrfs、ZFS、F2FS
- **Apple**：HFS+、APFS
- **Unix**：UFS、ISO 9660
- **嵌入式/其他**：UBIFS、SquashFS、JFFS2、HMDFS（鸿蒙）
- **安防监控**：DHFS 4.1（大华）、HIKVISION（海康）

> **已知限制**：ReFS 删除文件扫描/恢复不可用（微软未公开元数据结构），
> 对该格式可通过深度扫描（签名雕刻）恢复文件内容（无文件名与目录结构）。

### 监控视频提取
- 大华 DHFS 4.1 / 海康 HIKVISION 专有文件系统挂载浏览（虚拟目录：分区/通道/录像段）
- 已删除录像恢复（DHFS 空闲描述符、海康未引用数据块）
- DHAV 帧级雕刻兜底：通道解复用 + 时序重组（删除/格式化/元数据损坏场景）
- 导出格式：原生 .dav/.hik、裸 H.264/H.265 码流、MP4 转封装（需外部 ffmpeg）
- 设备树右键"监控视频提取"专用面板：通道/时段筛选、批量导出、案件审计

### 数据恢复
- 快速扫描（基于元数据）与深度扫描（基于签名+片段分析）
- 文件雕刻：PDF、PNG、ZIP
- 片段重组：内容策略 / 元数据策略 / 混合策略
- NTFS USN 日志分析、VSS 卷影复制
- APFS / ZFS 事务（世代）访问
- 断点续扫（SSDB 扫描状态数据库）

### 阵列重建
- 支持 RAID 0/1/5/6/10/50/60/1E/JBOD
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
- 7 个内置主题（含 LiquidGlass 等特效主题）

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

## 便携模式

FileSystemExplorer 支持便携模式，所有运行时数据写入 exe 同目录下的 `Data\` 子目录，
不向 C 盘 `%LOCALAPPDATA%` 写入日志、缓存、配置等文件——适合现场取证时搭配写保护锁使用。

### 启用方式

- **标记文件**：在 `FileSystemExplorer.exe` 同目录创建空文件 `FileSystemExplorer.portable`
  （可通过设置页"数据存储 → 启用便携模式"按钮自动创建）
- **环境变量**：设置 `FSX_DATA_DIR` 为自定义数据根路径（最高优先级，现场取证用）
- **已有 Data 目录**：exe 同目录下已存在 `Data\` 目录时自动进入便携模式

### 自定义数据目录（重定向）

便携模式下，数据默认写入 exe 旁的 `Data\` 文件夹。如需重定向到其他位置（例如
exe 放在证据源盘、数据写到外置安全盘的现场取证场景），可在设置 → 数据存储 →
"数据目录"点击"浏览…"选择目标目录，软件会在 exe 旁写入 `data_root.txt`
（首行为目标绝对路径），重启后数据根切换到该路径。点击"取消重定向"删除锚点
文件即可恢复默认（exe 旁 `Data\`）。

解析链优先级：`FSX_DATA_DIR` 环境变量 → `data_root.txt` 重定向 → exe 旁 `Data\`
（默认便携）→ `%LOCALAPPDATA%`（安装模式，写 C 盘）。重定向仅便携模式生效；
`data_root.txt` 内容无效（相对路径/空/含 BOM）时静默降级到默认便携目录。

### 便携模式须知

- **需激活后使用**：便携模式不提供 7 天试用，必须输入许可证激活
- **跨机需重新激活**：激活信息绑定机器硬件，便携介质换机使用时需重新激活
- **运行中不可拔出介质**：数据根在可移动介质上，拔出会导致写入失败和数据丢失
- **OS 层写入不可消除**：操作系统自身的 Prefetch、Amcache、文件对话框 MRU 等仍会记录程序
  运行痕迹。**源盘写保护才是取证洁净的正解**，便携模式仅消除应用层 C 盘写入

### 切回安装模式

删除 exe 旁的 `FileSystemExplorer.portable` 标记文件后重启即可。数据不会自动迁移，
请在设置页"打开数据目录"手动拷贝所需文件到 `%LOCALAPPDATA%\FileSystemExplorer`。

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
```

> 授权模块（RSA 验签、AES-GCM、7 天试用）已无条件编译，无需额外 feature。

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
| | [lz4_flex](https://crates.io/crates/lz4_flex) 0.13 | LZ4 解压 |
| | [lzma-rs](https://crates.io/crates/lzma-rs) 0.3 | LZMA/XZ |
| | [zstd](https://crates.io/crates/zstd) 0.13 | Zstandard |
| **序列化** | [serde](https://crates.io/crates/serde) 1.0 / serde_json 1.0 | 配置与数据序列化 |
| | [bincode](https://crates.io/crates/bincode) 1.3 | 二进制序列化（扫描状态） |
| **哈希校验** | [md-5](https://crates.io/crates/md-5) 0.10 / [sha1](https://crates.io/crates/sha1) 0.10 / [sha2](https://crates.io/crates/sha2) 0.10 / [sha3](https://crates.io/crates/sha3) 0.10 / [crc32fast](https://crates.io/crates/crc32fast) 1.3 | 镜像哈希 |
| **网络** | [tokio-tungstenite](https://crates.io/crates/tokio-tungstenite) 0.21 | WebSocket（HMDFS 远程访问） |
| | [reqwest](https://crates.io/crates/reqwest) 0.11 | HTTP 客户端 |
| **授权** | [rsa](https://crates.io/crates/rsa) 0.9 | RSA 签名验证 |
| | [aes-gcm](https://crates.io/crates/aes-gcm) 0.10 | AES-GCM 加密 |
| **其他** | [chrono](https://crates.io/crates/chrono) 0.4 | 日期时间处理 |
| | [uuid](https://crates.io/crates/uuid) 1.6 | UUID 生成 |
| | [byteorder](https://crates.io/crates/byteorder) 1.5 | 字节序转换 |

## 架构要点

- **GTK4** 主线程运行 UI，更新通过 `glib::spawn_future_local`
- **单一全局 Tokio 运行时**（`src/runtime.rs`），禁止在其他地方创建独立运行时
- **I/O 调度器**（`src/core/io_scheduler.rs`，线程数 = CPU 核数 clamp(1, 8)）处理所有设备 I/O
- **rayon** 用于 CPU 密集型工作（扫描、片段重组、RAID 奇偶校验）
- **只读原则**：所有源设备操作必须通过 `Device::read_sync()` / `read_vec()` 等只读抽象

## 文档索引

| 文档 | 说明 |
|------|------|
| [docs/USER_MANUAL.md](docs/USER_MANUAL.md) | 用户使用手册 |
| [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md) | 静默安装/卸载与自检 |
| [CHANGELOG.md](CHANGELOG.md) | 版本更新日志 |
| [CLAUDE.md](CLAUDE.md) | 项目架构与开发规范（面向开发者） |
| [docs/TEST_MATRIX_ReFS.md](docs/TEST_MATRIX_ReFS.md) | ReFS 测试矩阵 |

---

**版本信息**：版本号以 `Cargo.toml` 的 `version` 字段为唯一来源（构建号由 `build.rs` 按 git 提交计数自动生成），各版本变更详见 [CHANGELOG.md](CHANGELOG.md)。
