# 环境准备：测试钱包 + Foundry + lREACT

日期：2026-11-16

## 已完成

1. 创建仅用于测试的 OKX Wallet 钱包，公开地址：`0x6b0c7846751c79f4a921e7b619eeb4f058714c1f`。
2. 安装 Foundry 1.9.0，并运行 `forge --version`、`cast --version` 核实版本。
3. 领取 Sepolia ETH 和 Lasna lREACT；[Lasna 地址证明](https://lasna.reactscan.net/address/0x6b0c7846751c79f4a921e7b619eeb4f058714c1f)。本日核对钱包余额为 `0.454991799999049918` lREACT。
4. 创建公开学习仓库，包含 README、notes 和环境证明材料。
5. 克隆训练营模板并运行本地测试。原模板 8 项测试通过；订阅恢复修复后的 13 项测试通过。

## 证明材料

- [Forge 原始版本输出](../evidence/forge-version.txt)
- [Cast 原始版本输出](../evidence/cast-version.txt)
- [Foundry 版本输出截图](../evidence/foundry-version-output.jpg)：将本机真实命令输出在浏览器中展示后截图。
- [Lasna 钱包余额截图](../evidence/lasna-wallet-balance.jpg)：Lasna 浏览器可能将测试网原生币标作 REACT；本任务称为 lREACT。

## 后续实践记录

已在 Sepolia → Lasna → Sepolia 跑通事件、Reactive 执行和目标链回调，并完成额度耗尽后调用 topUp 恢复订阅的真实测试网验证。

- [模板修复 PR #2](https://github.com/ethpanda-org/reactive-builder-camp/pull/2)
- [真实测试网验证记录](https://github.com/ethpanda-org/reactive-builder-camp/pull/2#issuecomment-6915443514)
