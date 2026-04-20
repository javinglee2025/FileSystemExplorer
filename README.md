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



**当前版本**：0.3.9
