# RedmiBook 13 (2020) · Comet Lake · macOS 12 Monterey OpenCore EFI

> 小米 RedmiBook 13（TIMI TM1921，Comet Lake-U / i5-10210U）在 macOS 12.7.6 下**扬声器与耳机口均正常出声**的 OpenCore EFI。
> 本仓库**不包含任何序列号（三码）**，下载后必须自行生成，详见 [docs/02-生成三码.md](docs/02-生成三码.md)。

---

## ⚠️ 先说三件事

1. **不要直接拿去登录 iCloud。** 本仓库的 `config.plist` 已清空 `SystemSerialNumber` / `MLB` / `SystemUUID` / `ROM` 四个字段。你必须先生成一套**只属于你自己**的三码，否则 App Store、iCloud、iMessage 均不可用。
2. **绝对不要把别人的三码填进来。** 同一套三码被多台机器登录，会被 Apple 判定为异常，轻则 iMessage/FaceTime 无法激活，重则 Apple ID 被临时锁定。详见 [DISCLAIMER.md](DISCLAIMER.md)。
3. **先备份你自己的 EFI。** 任何替换操作前，务必把原 EFI 整个复制一份到 U 盘或另一块硬盘。EFI 被覆盖且没有备份，机器会直接无法开机。

---

## 一、这个 EFI 的特殊之处：ALC256 耳机口修好了

大部分 RedmiBook 13 的黑苹果 EFI 都卡在同一个坑上——**内置扬声器有声，插耳机没声**。

原因是 ALC256 的耳机节点 `0x21` 是一个**四段组合口**（耳机 + 麦克风共用），但绝大多数现成 layout 把它按"普通 1/8 英寸耳机口"配置。引脚定义与实际硬件不符，macOS 的插孔检测就映射不到这条通路。

本包在 `AppleALC.kext` 里把该节点的 Pin Configuration 从 `0x03211420` 改为 `0x022B1050`，`Connection Type` 由 `0x1`（1/8" 耳机口）改为 `0xB`（**Combination 组合口**），与真机一致。改完之后插拔耳机自动切换，内置扬声器与耳机同时可用。

细节见 [docs/03-版本与组件清单.md](docs/03-版本与组件清单.md) 第 4.4 节。

---

## 二、适用范围

### 已验证的硬件

| 项目 | 规格 |
|------|------|
| 机型 | 小米 RedmiBook 13（2020 款，机型代号 **TIMI TM1921**，Comet Lake-U 平台） |
| CPU | Intel Core i5-10210U（4 核 8 线程，1.6 GHz 基频） |
| 核显 | Intel UHD Graphics（Comet Lake-U GT2），`AAPL,ig-platform-id = 0x3E9B0000`，显存 2048 MB |
| 内存 | 8 GB（板载，不可扩展） |
| 声卡 | Realtek ALC256（`0x10EC0256` / 十进制 `283902550`，子系统 `1D72:1921`），`layout-id = 101` |
| 无线 | Intel 无线网卡（`8086:02A4`），`AirportItlwm 2.1.0` 驱动，支持 802.11ac |
| 蓝牙 | Intel 蓝牙，`IntelBluetoothFirmware 2.2.0` + `BlueToolFixup 2.6.4` |
| 触摸板 | I2C HID（`VoodooI2C 2.7.1` + `VoodooI2CHID 1.0`） |
| 键盘 | PS/2（`VoodooPS2Controller 2.3.3`） |
| 引导 | OpenCore（`OpenCore.efi` 构建于 2023-01-02） |
| 系统 | macOS 12.7.6（21H1320） |

### 可以试，但不保证

同平台（Comet Lake-U + ALC256 + Intel 网卡）的其它轻薄本，例如 RedmiBook 14/16、部分小米笔记本 Pro、以及联想小新 / 华为 MateBook D 的 Comet Lake 版本，**有可能**直接启动，但以下项目大概率需要调整：

- `SSDT-PNLF.aml`（亮度）与 `SSDT-EC-USBX-LAPTOP.aml`（EC）几乎一定需要按你的 DSDT 重做；
- `UTBMap.kext`（USB 端口映射）是**本机专用**，别的机型插口定义不同，需用 USBToolBox 重新生成；
- 声卡：如果你的 ALC256 不是组合口，`0x022B1050` 反而可能让耳机失效，需要按你的 codec dump 改回；
- `CPUFriend` 未启用，功耗策略按本机默认。

### 明确不支持

- 独立显卡机型（本配置 `-wegnoegpu` 禁用独显）；
- macOS 13 Ventura 及以上（`AirportItlwm` 与部分 kext 需换版本，本包未适配）；
- Apple Silicon / AMD 平台（完全不同体系）。

---

## 三、目录结构

```
.
├── EFI/                          # 直接放到 EFI 分区根目录
│   ├── BOOT/
│   │   ├── BOOTx64.efi           # OpenCore 引导入口
│   │   ├── .contentFlavour
│   │   └── .contentVisibility
│   └── OC/
│       ├── ACPI/                 # 11 个 SSDT（仅 config 实际引用的）
│       ├── Drivers/              # 5 个 UEFI 驱动
│       ├── Kexts/                # 27 个内核扩展
│       ├── Resources/            # OpenCanopy 图形界面资源（官方主题）
│       ├── Tools/                # modGRUBShell.efi、CFGLock.efi
│       ├── OpenCore.efi
│       └── config.plist          # 已脱敏
├── docs/
│   ├── 01-安装与使用.md
│   ├── 02-生成三码.md
│   ├── 03-版本与组件清单.md
│   └── 04-故障排查.md
├── README.md
├── DISCLAIMER.md                 # 免责声明（务必阅读）
├── CHANGELOG.md
├── THIRD-PARTY.md                # 第三方组件与许可
├── SHA256SUMS.txt                # 全部文件的校验和
├── LICENSE
├── .gitattributes                # 二进制文件按 binary 处理，避免换行符污染
└── .gitignore
```

**校验下载完整性**（可选，但推荐）：

```bash
# macOS / Linux，在仓库根目录执行
shasum -a 256 -c SHA256SUMS.txt
```

**为什么不放 `EFI/Microsoft`：** 原 EFI 分区里还有 Windows 的 `bootmgr` 全套文件（约 27 MB），属于微软的专有二进制，与黑苹果无关，也不适合转载，已剔除。同理剔除了 `Boot/win10bootx64.efi`。

**为什么 kext 只有 27 个：** 原 EFI 里堆了 75 个 kext、90 条 `Kernel > Add` 条目，其中 59 条是禁用的历史尝试（FakeSMC 与 VirtualSMC 并存、VoodooHDA 与 AppleALC 并存），另有 6 个 `Kexts-AppleALC.old-*` 回滚目录、8 个 `config.plist.bak-*`、3 个 `.backup-*` 隐藏目录。这些对使用者毫无用处，且极易误启用导致冲突，已全部剔除。本包内**每条配置都有对应文件，每个文件都被配置引用**。

---

## 四、快速开始

1. **备份** 你自己现有的 EFI（整个 `EFI` 文件夹）到 U 盘。
2. 下载本仓库（`Code` → `Download ZIP`，或 `git clone`）。
3. 生成你自己的三码，填入 `EFI/OC/config.plist`（见 [docs/02-生成三码.md](docs/02-生成三码.md)）。
4. 挂载 EFI 分区，把本仓库的 `EFI` 文件夹内容覆盖进去（`BOOT` 与 `OC` 两个子目录同级替换）。
5. 重启，进 OpenCore 菜单选 macOS。首次启动建议先清一次 NVRAM（菜单里的 `Reset NVRAM`）。

完整步骤、验证方法与回滚方式见 [docs/01-安装与使用.md](docs/01-安装与使用.md)。

---

## 五、版本信息

| 组件 | 版本 / 说明 |
|------|-------------|
| OpenCore | `OpenCore.efi` 构建日期 2023-01-02，589,824 字节 |
| config.plist | 本仓库脱敏版，SHA256 `09efb62ae5554d7f15b780d1a145742667149c75487572f6abba8b4f66795cb5` |
| Lilu | 1.6.3 |
| VirtualSMC | 1.3.0（含 SMCBatteryManager / SMCProcessor / SMCSuperIO / SMCLightSensor） |
| WhateverGreen | 1.6.3 |
| AppleALC | 1.9.9（**定制**：ALC256 layout 101 引脚配置已修正组合口） |
| AirportItlwm | 2.1.0 |
| IntelBluetoothFirmware | 2.2.0 |
| VoodooI2C | 2.7.1 |
| VoodooPS2Controller | 2.3.3 |
| 目标系统 | macOS 12.7.6（21H1320） |

27 个 kext 的逐个版本、用途与依赖关系见 [docs/03-版本与组件清单.md](docs/03-版本与组件清单.md)。

---

## 六、已知限制（本机实测状态）

| 项目 | 状态 | 说明 |
|------|------|------|
| 内置扬声器 | ✅ 出声 | AppleALC `layout-id = 101` |
| 耳机口 | ✅ 出声，插拔自动切换 | 组合口引脚配置已修正（`0x022B1050`） |
| 内置麦克风 | ⚠️ 设备存在，未测录音 | `Input Source: Internal Microphone`，路径已通，实际录音质量未验证 |
| Wi-Fi | ✅ 可用 | `AirportItlwm` 需在「系统设置 → 网络」中手动添加，不走原生 AirPort 界面 |
| 蓝牙 | ⚠️ 部分可用 | Intel 蓝牙在 Monterey 下隔空投送/接力受限，属方案固有限制 |
| 读卡器 | ❌ 未启用 | 相关 kext 未包含 |
| 睡眠 / 唤醒 | ⚠️ 未系统验证 | `HibernationFixup` 已加载，未逐一测试 |
| 指纹 / 摄像头 | ❌ 不支持 | 无对应驱动方案 |

功能状态以**你自己的实测**为准。仓库维护者不对任何单项功能作可用性承诺。

---

## 七、免责声明（摘要）

本项目仅供**学习研究**与**个人设备备份**使用。在非 Apple 硬件上安装 macOS 违反 Apple 的软件许可协议（EULA），由此产生的一切后果由使用者自行承担。本仓库不提供、不含、也不鼓励使用任何 Apple 专有系统文件；不包含任何真实设备的序列号。

**完整条款见 [DISCLAIMER.md](DISCLAIMER.md)，使用前请务必阅读。**

---

## 八、致谢

本配置能跑起来，靠的是这些开源项目（完整列表与许可见 [THIRD-PARTY.md](THIRD-PARTY.md)）：

[OpenCore](https://github.com/acidanthera/OpenCorePkg) · [Lilu](https://github.com/acidanthera/Lilu) · [VirtualSMC](https://github.com/acidanthera/VirtualSMC) · [WhateverGreen](https://github.com/acidanthera/WhateverGreen) · [AppleALC](https://github.com/acidanthera/AppleALC) · [AirportItlwm](https://github.com/OpenIntelWireless/itlwm) · [IntelBluetoothFirmware](https://github.com/OpenIntelWireless/IntelBluetoothFirmware) · [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C) · [VoodooPS2](https://github.com/acidanthera/VoodooPS2) · [USBToolBox](https://github.com/USBToolBox/tool) · [Hackintool](https://github.com/headkaze/Hackintool)
