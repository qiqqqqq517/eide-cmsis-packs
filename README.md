# eide-cmsis-packs

eIDE 远程芯片包源（CMSIS Pack）。目录 `packs/` 为平铺的 `.pack` 文件，可直接作为 eIDE 的在线安装源。

## 用法

`settings.json`（用户级，`EIDE.Repository.CmsisPack.Url` 的作用域是 machine）：

```json
"EIDE.Repository.CmsisPack.Url": "qiqqqqq517/eide-cmsis-packs/contents/packs",
"EIDE.Repository.UseProxy": true
```

然后在 eIDE 里「芯片支持包 → 从远程仓库安装」，列表即为 `packs/` 下的全部文件。
`UseProxy` 打开后 eIDE 会把 `api.github.com` 改写到 `api-github.em-ide.com`、`raw.githubusercontent.com` 改写到 `raw-github.em-ide.com`，避免未认证 API 的 60 次/小时限流。

## 内容

- 覆盖 [github0null/eide-cmsis-pack](https://github.com/github0null/eide-cmsis-pack) 现有全部条目，且每个条目都升级到官方索引中的最新版本。
- 额外补齐 ST 全系列 30 个器件支持包（C0/C5/F0/F1/F2/F3/F4/F7/G0/G4/G5/H5/H7/H7P/H7RS/L0/L1/L4/L5/MP1/N6/U0/U3/U5/WB0/WBA/WB/WL3/WL4/WL），含 `STM32H723VGTx` 所在的 `Keil.STM32H7xx_DFP`。
- 版本基准：Keil 官方索引 <https://www.keil.com/pack/index.pidx>（下载时 timestamp 2026-11-18T04:08:38Z）。
- 文件名规则：`<Vendor>.<Name>.<Version>.pack`，与 eIDE 的安装器期望完全一致（它按 `noSuffixName.split('.')` 推导 `<Vendor>/<Name>.<Version>` 目录）。

## 更新

```powershell
[Net.ServicePointManager]::SecurityProtocol='Tls12'
Invoke-WebRequest 'https://www.keil.com/pack/index.pidx' -OutFile index.pidx -UseBasicParsing
[xml]$x = Get-Content index.pidx -Encoding utf8
$x.index.pindex.pdsc | Where-Object { $_.name -match 'STM32' -or $_.vendor -match 'STMicro' } |
  ForEach-Object { '{0}.{1}.{2}.pack' -f $_.vendor, $_.name, $_.version; $_.url }
```

## 许可证

包本身由各厂商发布（Keil/ST/GigaDevice/HDSC/MindMotion/Nordic/Nuvoton/TI/SiliconLabs 等），随包附带各自的 LICENSE 与 EULA，本仓库仅做原样分发，不修改内容。商用前请核对对应厂商许可。
