# D2 读懂费用：用 cast 查询 Lasna 系统合约参数

## 查询结果

使用 Foundry 的 `cast` 查询 Reactive Lasna 测试网的系统合约。RPC 返回的 Chain ID 为 `5318007`。三次查询固定在区块 **8854420**，以保证参数对应同一状态。RPC URL 是 `https://lasna-rpc.rnk.dev/`，系统合约地址是 `0x8888888888888888888888888888888888888888`。

```bash
cast call 0x8888888888888888888888888888888888888888 '_gasPriceCoeffPer1000()(uint256)' --block 8854420 --rpc-url https://lasna-rpc.rnk.dev/
# 1000

cast call 0x8888888888888888888888888888888888888888 '_extraGas()(uint256)' --block 8854420 --rpc-url https://lasna-rpc.rnk.dev/
# 100000 [1e5]

cast call 0x8888888888888888888888888888888888888888 '_maxChargeGas()(uint256)' --block 8854420 --rpc-url https://lasna-rpc.rnk.dev/
# 50000 [5e4]
```

`[1e5]` 和 `[5e4]` 是 `cast` 对十进制数的科学计数法提示。参数可能变化，该结果代表上述区块的链上状态。

## 1. 一次 react() 的费用怎么算？

根据官方文档，费用公式为：

```text
fee = 基础 gas 价格 × 费率系数 × (实际消耗 gas + 固定附加 gas)
```

基础 gas 价格取 `tx.gasprice` 和 `block.basefee` 中较大的值。费率系数为 `_gasPriceCoeffPer1000 / 1000`，固定附加 gas 为 `_extraGas`。本次查询的系数是 `1000 / 1000 = 1`，固定附加量为 `100000 gas`。

假设计费用的实际消耗是 `50000 gas`，基础价格是 `100 gwei`，则：

```text
100 gwei × 1 × (50000 + 100000)
= 15000000 gwei
= 0.015 lREACT
```

这是假设计数，不是实测交易费用。`_maxChargeGas = 50000` 是结算付款时可用的 gas 上限，不直接加进费用公式。

## 2. balance、reserves、debt 分别是什么？

| 项目 | 含义 |
| --- | --- |
| balance | Reactive 合约自身持有的原生币余额 |
| reserves | 系统合约记录的预付款，费用优先从中扣除 |
| debt | 系统合约记录的尚未付清的费用 |

系统在投递新交易前检查欠款。只要 `debt > 0`，就暂停调用该合约的 `react()`，直到欠款清零。目标链的回调合约也有独立的余额、预付款和欠款机制。

直接向合约转币只增加合约的 `balance`，不会自动清偿已有欠款。向系统合约调用 `depositTo(目标合约地址)` 会先清偿该目标合约的欠款，再将余款计入 `reserves`。

## 3. react() 回滚后还收费吗？

收费。系统合约捕获 `react()` 的回滚、记录 `ReactiveContractReverted`，并结算此次执行消耗的费用。

对合约设计的影响：

- 对不满足业务条件的普通事件使用判断后 `return`，不把 `revert` 当作事件过滤方式。已经发生的执行和适用的附加费用仍会计费。
- 在订阅时尽量限定源链、合约地址和 topic，减少无关事件进入 `react()`。
- 先检查事件类型和数据布局再解码，减少意外回滚。
- 监控资金和欠款，并设计暂停或退订方式，避免事件持续消耗资金。

## 参考资料

- [Reactive Economy：费用、余额与欠款](https://dev.reactive.network/economy)
- [Reactive Contracts：订阅与事件处理](https://dev.reactive.network/reactive-contracts)
