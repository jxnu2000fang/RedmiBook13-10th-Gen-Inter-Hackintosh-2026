# 第三方组件与许可

本仓库的 `EFI/` 目录内是二进制文件，其著作权归各上游项目所有，按各自的许可证分发。本文档仅作清单与出处说明，**不改变任何上游许可证的效力**。

使用、修改、再分发这些二进制文件时，请遵守对应项目的许可证。

---

## 特别说明：被修改的组件

| 组件 | 修改内容 | 依据 |
|------|----------|------|
| `AppleALC.kext` | `Contents/Info.plist` 中 `HDAConfigDefault` 里 `LayoutID = 101` 条目的 `ConfigData`（节点 `0x21` 的四条引脚配置动词）；并将该条目的 `Comment` 去除作者用户名前缀 | AppleALC 采用 BSD-3-Clause，允许修改与再分发；**修改后的二进制不由上游项目背书** |

其余所有 kext、驱动、ACPI 文件均为上游原样二进制，未做任何修改。

---

## 一、引导器

| 组件 | 上游项目 | 许可证 |
|------|----------|--------|
| OpenCore（`OpenCore.efi`、`BOOTx64.efi`、`Drivers/*.efi`） | [acidanthera/OpenCorePkg](https://github.com/acidanthera/OpenCorePkg) | BSD-3-Clause |
| OpenCanopy 主题资源（`OC/Resources/`） | 同上 | BSD-3-Clause |

`Tools/modGRUBShell.efi` 基于 [GRUB](https://www.gnu.org/software/grub/)（GPL-3.0）修改，作为独立 EFI 二进制分发。

---

## 二、内核扩展

| Kext | 上游项目 | 许可证 |
|------|----------|--------|
| `Lilu.kext` | [acidanthera/Lilu](https://github.com/acidanthera/Lilu) | BSD-3-Clause |
| `VirtualSMC.kext` 及 `SMCBatteryManager` / `SMCProcessor` / `SMCSuperIO` / `SMCLightSensor` | [acidanthera/VirtualSMC](https://github.com/acidanthera/VirtualSMC) | BSD-3-Clause |
| `WhateverGreen.kext` | [acidanthera/WhateverGreen](https://github.com/acidanthera/WhateverGreen) | BSD-3-Clause |
| `AppleALC.kext` ⚠️ 已修改 | [acidanthera/AppleALC](https://github.com/acidanthera/AppleALC) | BSD-3-Clause |
| `HibernationFixup.kext` | [acidanthera/HibernationFixup](https://github.com/acidanthera/HibernationFixup) | BSD-3-Clause |
| `BrightnessKeys.kext` | [acidanthera/BrightnessKeys](https://github.com/acidanthera/BrightnessKeys) | BSD-3-Clause |
| `RestrictEvents.kext` | [acidanthera/RestrictEvents](https://github.com/acidanthera/RestrictEvents) | BSD-3-Clause |
| `VoodooPS2Controller.kext` | [acidanthera/VoodooPS2](https://github.com/acidanthera/VoodooPS2) | GPL-2.0 |
| `VoodooI2C.kext` / `VoodooI2CHID.kext` 及子插件 | [VoodooI2C/VoodooI2C](https://github.com/VoodooI2C/VoodooI2C) | GPL-2.1 |
| `AirportItlwm.kext` | [OpenIntelWireless/itlwm](https://github.com/OpenIntelWireless/itlwm) | GPL-2.0 |
| `IntelBluetoothFirmware.kext` / `IntelBluetoothInjector.kext` / `IntelBTPatcher.kext` | [OpenIntelWireless/IntelBluetoothFirmware](https://github.com/OpenIntelWireless/IntelBluetoothFirmware) | GPL-2.0 |
| `BlueToolFixup.kext` | [acidanthera/BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM) | BSD-3-Clause |
| `USBToolBox.kext` / `UTBMap.kext` | [USBToolBox/kext](https://github.com/USBToolBox/kext) | MIT |
| `ECEnabler.kext` | [1Revenger1/ECEnabler](https://github.com/1Revenger1/ECEnabler) | MIT |
| `NoTouchID.kext` | [al3xtjames/NoTouchID](https://github.com/al3xtjames/NoTouchID) | MIT |
| `NullEthernet.kext` | [RehabMan/OS-X-Null-Ethernet](https://github.com/RehabMan/OS-X-Null-Ethernet) | GPL-2.0 |
| `FeatureUnlock.kext` | [chris1111/FeatureUnlock](https://github.com/chris1111/FeatureUnlock) | MIT |
| `VerbStub.kext` | [syscl/VerbStub](https://github.com/syscl/VerbStub) | GPL-2.0 |
| `VoltageShift.kext` | [sicreative/VoltageShift](https://github.com/sicreative/VoltageShift) | MIT |

> 许可证以各上游仓库 `LICENSE` 文件为准。若某个条目的许可证标注有误，请提 issue 指正。

---

## 三、ACPI 补丁

`EFI/OC/ACPI/` 下的 `.aml` 文件为编译后的 ACPI 补丁，多数源自 [acidanthera/Getting-Started-with-ACPI](https://github.com/acidanthera/Getting-Started-with-ACPI) 与 OpenCore 官方文档的示例，由作者根据本机 DSDT 编译生成。

---

## 四、本仓库原创内容

以下内容由本仓库作者编写，采用 MIT 许可证（见 [LICENSE](LICENSE)）：

- `README.md`、`docs/` 下全部文档、`DISCLAIMER.md`、`CHANGELOG.md`、`THIRD-PARTY.md`
- `EFI/OC/config.plist` 中作者自行调整的配置项
- `EFI/OC/Kexts/AppleALC.kext/Contents/Info.plist` 中的引脚配置修改

**该 MIT 授权不覆盖 `EFI/` 目录下的其它二进制文件**，这些文件仍归各自上游项目所有。

---

## 五、未包含的组件

以下内容因版权或体积原因**未**包含在本仓库中，请自行获取：

- macOS 安装镜像（Apple 专有，须从 Mac App Store 获取）
- Windows 引导文件（`EFI/Microsoft`，微软专有）
- Xcode / Command Line Tools（用于编译 kext）
