![图片](https://picx.zhimg.com/70/v2-82786aa936c953429acddafd19ae6de8_1440w.avis?source=172ae18b&biz_tag=Post)

# Beelink SER5 Max Hackintosh

基于 AMD Ryzen 7 5800H 的 Mini 主机，采用 OpenCore 引导。

**⚠️ 本机使用 Intel AX200 无线网卡，不支持隔空投送、随航等 Apple 生态功能。**

## 硬件信息

| 组件 | 型号 |
|------|------|
| CPU | AMD Ryzen 7 5800H (8核/16线程) |
| 内存 | 32GB DDR4 3200MHz |
| 硬盘 | 1TB NVMe SSD |
| iGPU | AMD Radeon Graphics (NootedRed) |
| 有线网卡 | Realtek RTL8125 2.5GbE |
| 无线网卡 | Intel AX200 (WiFi 6 + 蓝牙 5.0) |
| 声卡 | Realtek ALC (layout-id=58) |

## 软件信息

| 项目 | 版本 |
|------|------|
| macOS | Ventura 13.7.8 |
| OpenCore | 1.0.6 |
| SMBIOS | iMac20,1 |

### 启动参数 (boot-args)

```
revpatch=sbvmm revblock=media keepsyms=1 alcid=58 darkwake=0 -NRedDPDelay
```

| 参数 | 作用 |
|------|------|
| `revpatch=sbvmm` | 启用 SecureBootModel 虚拟机补丁 |
| `revblock=media` | 阻止系统更新推送 |
| `keepsyms=1` | 保留内核符号，便于 panic 调试 |
| `alcid=58` | 声卡 layout-id |
| `darkwake=0` | 优化休眠唤醒 |
| `-NRedDPDelay` | NootedRed DP 显示延迟修复 |

### SIP 与安全设置

- SIP: 完全启用 (`csr-active-config: AAAAAA==`)
- SecureBootModel: `j160`
- Vault: `Optional`
- BlacklistAppleUpdate: 已启用

## 加载的 Kexts

| Kext | 版本 | 说明 |
|------|------|------|
| Lilu.kext | 1.7.3 | 核心补丁框架 |
| VirtualSMC.kext | 1.3.8 | SMC 模拟 |
| NootedRed.kext | 0.8.10 | AMD iGPU 驱动 |
| AMDRyzenCPUPowerManagement.kext | 0.7.2 | AMD CPU 电源管理 |
| SMCAMDProcessor.kext | 1.0 | AMD 处理器传感器 |
| AppleALC_5800H.kext | 1.8.5 | 声卡驱动 (layout-id 58) |
| RealtekRTL8111.kext | 3.0.4 | 有线网卡驱动 |
| AirportItlwm_for_Ventura.kext | 2.2.0 | Intel WiFi (Ventura) |
| BFixup.kext | 1.0.1 | AMD 蓝牙修复 |
| BlueToolFixup.kext | 2.7.2 | 蓝牙固件加载 |
| IntelBluetoothFirmware.kext | 2.5.0 | Intel 蓝牙固件 |
| IntelBTPatcher.kext | 2.5.0 | Intel 蓝牙补丁 |
| IntelBluetoothInjector.kext | 2.5.0 | Intel 蓝牙注入 |
| FeatureUnlock.kext | 1.1.9 | 解锁 Sidecar 等功能 |
| NVMeFix.kext | 1.1.4 | NVMe 电源管理修复 |
| RestrictEvents.kext | 1.1.7 | 事件限制 (禁用更新提示等) |
| USBPorts.kext | 1.0 | USB 端口定制 |
| USBToolBox.kext | 1.1.1 | USB 工具集 |
| UTBMap.kext | 1.1 | USB 映射 |
| SMCRadeonSensors.kext | 2.4.0 | AMD GPU 传感器 |
| AppleMCEReporterDisabler.kext | 1.2 | MCE 报告禁用 |
| Display-5c98-270.kext | - | 显示器 EDID 修复 |
| Display-2613-2700.kext | - | 显示器 EDID 修复 |

以及多个版本 AirportItlwm 以支持跨版本启动（Sonoma 14.4+ / Sonoma / Monterey / BigSur）。

## 更新记录

### 2026-07-20
- 优化启动参数：移除 `debug=0x100` `-lilubetaall`，新增 `darkwake=0` `-NRedDPDelay` 📺
- NootedRed 更新至 0.8.10
- Lilu 更新至 1.7.3
- BlueToolFixup 更新至 2.7.2
- 新增 Display-2613-2700.kext、Display-5c98-270.kext 显示器 EDID 修复
- 清理冗余 kext 和旧配置文件，统一为单 config.plist
- 新增 AGENTS.md 智能代理指导文件
- 接入 Argus-Flash CI 自动审查

### 2025-12-02
- 更新 OpenCore 至 1.0.6

### 2025-09-04
- 更新 OpenCore 至 1.0.5
- 其他驱动例行更新

### 2025-03-06
- 更新 OpenCore 至 1.0.4
- 其他驱动例行更新

### 2024-12-21
- 更新 OpenCore 至 1.0.3

### 2024-11-30
- Lilu.kext 更新 1.7.0
- NVMeFix.kext 更新 1.1.2
- IntelBluetoothInjector.kext 更新 2.5.0
- IntelBTPatcher.kext 更新 2.5.0
- IntelBluetoothFirmware.kext 更新 2.5.0

### 2024-10-26
- BlueToolFixup.kext 更新至 1.2.1

### 2024-09-20
- 初始化项目

## 鸣谢

- [Acidanthera](https://github.com/acidanthera) — OpenCore 核心工具与驱动
- [Dortania](https://github.com/dortania) — OpenCore 安装指南与文档
- [NootedRed](https://github.com/ChefKissInc/NootedRed) — AMD iGPU 驱动
- [黑果小兵](https://blog.daliansky.net/) — 项目参考资料与 EFI 基础