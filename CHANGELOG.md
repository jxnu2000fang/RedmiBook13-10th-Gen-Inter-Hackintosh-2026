# 变更记录

格式参考 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.1.0/)，版本号遵循 [语义化版本](https://semver.org/lang/zh-CN/)。

---

## [1.0.0] — 2026-10-29

首个公开版本。基于当日本机 EFI 分区的**最终状态**整理，**包含 12:00 完成的 ALC256 耳机修复**。

> 说明：当天上午曾生成过一版草稿（基于 08:01 的 EFI 备份），那一版的耳机口仍无声，由于尚未发布，已作废，本 1.0.0 为其替代。

### 音频修复（本版重点，也是本 EFI 的核心价值）

- 修正 `AppleALC.kext` 中 ALC256 / `LayoutID = 101` 条目的耳机节点 `0x21` 引脚配置：
  **`0x03211420` → `0x022B1050`**；
- 关键位段：`Connection Type` 由 `0x1`（1/8" 耳机口）改为 `0xB`（**Combination 组合口**），与 RedmiBook 13 实际的三合一插孔一致；
- 修复前：仅内置扬声器出声，插耳机无反应；修复后：扬声器与耳机均正常，插拔自动切换；
- 修改位置：`IOKitPersonalities → as.vit9696.AppleALC → HDAConfigDefault[663] → ConfigData`；
- `CodecID`（`283902550`）与 `LayoutID`（`101`）未变，`WakeConfigData` 未变。

### 安全处理

- **清空 `PlatformInfo > Generic` 中的 `SystemSerialNumber`、`MLB`、`SystemUUID`、`ROM`**，改为空值；
- 同步清空 `PlatformInfo > DataHub` 与 `PlatformInfo > SMBIOS` 段中的序列号与 UUID 副本（防止 `Automatic` 改为 `false` 后泄露）；
- 中性化 `AppleALC.kext/Contents/Info.plist` 中 `Comment` 字段的作者用户名前缀；
- 全仓库二进制与文本双重搜索，确认作者原始三码与用户名无任何残留；
- 剔除 `EFI/Microsoft`（27 MB 微软专有引导文件）与 `EFI/Boot/win10bootx64.efi`。

### 精简

- 剔除 8 个 `config.plist.bak-*`（含旧序列号的历史备份）；
- 剔除 6 个 `Kexts-AppleALC.old-*` / `.broken-*` 临时回滚目录；
- 剔除 3 个 `OC/.backup-20261029-*` 隐藏目录（修声卡时的回滚点）；
- kext 从 75 个精简至 27 个，只保留 `Kernel > Add` 中实际启用的项；
- ACPI 从 42 个精简至 11 个，只保留 `ACPI > Add` 中实际启用的项；
- Drivers 从 20 个精简至 5 个，Tools 从 7 个精简至 2 个；
- `Kernel > Add` 条目从 90 条精简至 31 条，`ACPI > Add` 从 31 条精简至 11 条，使配置与文件一一对应；
- 体积：**92 MB → 约 35 MB**。

### 未改动

- 除身份字段与上述 `Comment` 外，`config.plist` 的所有键值（`boot-args`、`DeviceProperties`、`Kernel > Quirks`、`ACPI > Patch`、`NVRAM`、`Misc > Security` 等）保持与作者机器一致，未做「优化」；
- 所有 kext 二进制均为原始文件，**未经重新编译**（`AppleALC.kext` 仅改了 plist 里的引脚配置与注释）。

### 新增文档

- `README.md`：适用范围、目录结构、快速开始、音频修复说明、版本摘要、已知限制；
- `docs/01-安装与使用.md`：备份、替换、首次启动、逐项验证（含耳机验证）、回滚；
- `docs/02-生成三码.md`：OCAT 与 GenSMBIOS 两种生成方式、序列号可用性判断、Apple ID 风险说明；
- `docs/03-版本与组件清单.md`：27 个 kext、5 个驱动、11 个 ACPI 补丁的逐个版本与用途，音频修复的完整解码；
- `docs/04-故障排查.md`：十类常见故障，含「耳机无声」的完整排查树；
- `DISCLAIMER.md`、`THIRD-PARTY.md`、`CHANGELOG.md`、`SHA256SUMS.txt`。

### 已知问题

- `config.plist` 为 OpenCore 0.6.x 布局（`Misc > Boot` 段），缺 0.7.0+ 的顶层 `Boot` 段，`ocvalidate` 会提示，实测不影响启动；
- 读卡器未启用；
- 睡眠/唤醒未做系统性验证；
- 内置麦克风设备存在，录音质量未验证；
- 蓝牙在 Monterey 下隔空投送/接力受限（方案固有限制）。

---

## 今后版本的记录方式

- **修正版**（文档错字、补充说明）：`1.0.x`；
- **配置调整**（换 layout、增删 kext、适配新系统版本）：`1.x.0`；
- **不兼容变更**（换机型、换 OpenCore 大版本）：`2.0.0`。

每次改动请在此文件记录：改了什么、为什么改、影响哪些硬件。

> ⚠️ **升级 `AppleALC` 会覆盖本仓库的音频修复。** 升级前务必先备份 `EFI/OC/Kexts/AppleALC.kext`，升级后按 [docs/04-故障排查.md](docs/04-故障排查.md) 第 3 节重新核对 `ConfigData`。
