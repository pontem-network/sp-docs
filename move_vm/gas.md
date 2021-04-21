# Fees & Gas

Every transaction in the Pontem network requires sender to pay fee, as well as it has maximum amount of weight which can be used during transaction execution.

## Weight

**Each transaction in the Pontem network has such parameter as weight**. Weight is a cost of operation in the blockchain. When transaction executing it uses resources of validators, like: CPU, memory, storage write/read operations. Each described operation has a cost in weight. 

Each block in Pontem network should be executed in time, usually it's 6 seconds, protocol converts these 6 seconds into weight using following logic (From official Substrate documentation):

> The amount of weight a block may contain is limited, and optional weight consumption (i.e. weight that is not required to be deployed as part of the block's initialization or finalization phases nor used in mandatory inherent extrinsics) will generally be limited through economic measures --- or in simple terms, through transaction fees. The fee implications of the weight system are covered in the Transaction Fees document.

> Substrate defines one unit of weight as one picosecond of execution time, that is 1012 weight = 1 second, or 1,000 weight = 1 nanosecond, on fixed reference hardware (Intel Core i7-7700K CPU with 64GB of RAM and an NVMe SSD).

> The maximum block weight should be equivalent to one-third of the target block time, allocating one third for block construction, one third for network propagation, and one third for import and verification. Doubling the block time would allow a doubling of the maximum block weight. These tuning options give runtime developers a way to make the optimal transaction per second vs. hardware requirement trade-offs for their use case. These trade-offs can be tuned with runtime updates to keep up with hardware and software improvements.

So when a user prepares a transaction to the network user's getting from Pontem network node required Weight for this transaction. Weight can be dynamic or fixed, for example, Move VM related transactions has dynamic weight based on gas.

Read more about [weight](https://substrate.dev/docs/en/knowledgebase/learn-substrate/weight) in Substrate documentation.

## Gas

{% hint style="info" %}
🧙‍♂️ When you don't use smart contracts transactions in the Pontem network you don't need to care about gas. Gas is used only in Move VM pallets and required by Move VM, this why we implemented compatibility gas <-> weight.
{% endhint %}

Due to compatibility with Move VM Pontem network also supports gas parameters by converting the amount of gas provided by the user into weight. Gas is a cost of operation in a Move Virtual Machine using only during execution of VM related transaction (scripts and modules execution/publishing).

The gas amount parameter is an u64 number and configured during the preparing transaction. So the max gas limit is u64 max number `18446744073709551615`, minimum is `1`.

During execution of transaction Move VM estimate gas spends using provided [Gas Cost Table](https://github.com/pontem-network/sp-move-vm/blob/f1eeb40b13e55e7d8fef6b60f14c2387ef9f6e9e/mvm/src/gas_schedule.rs#L17). Each operation described in the table costs a certain amount of gas. If the gas amount provided by the user is not enough to execute a smart contract transaction, VM will revert a transaction and return `Out Of Gas` error.

Gas converting to weight using next formula:

// TODO: update values.

```text
1. Estimate how much gas VM spends during 1 second.
2. Estimate how much weight spends for 1 second.
3. WEIGHT_PER_SECOND / GAS_PER_SECOND and get the amount WEIGHT_PER_GAS.
4. Safe mul provided gas on WEIGHT_PER_GAS.
```

You can see it our Move VM pallet code:

```rust
/// By inheritance from Moonbeam and from Dfinance (based on validators statistic), we believe max 4125000 gas is currently enough for block.
/// In the same time we use same 500ms Weight as Max Block Weight, from which 75% only are used for transactions.
/// So our max gas is GAS_PER_SECOND * 0.500 * 0.65 => 4125000.
pub const GAS_PER_SECOND: u64 = 11_000_000;

/// Approximate ratio of the amount of Weight per Gas.
/// u64 works for approximations because Weight is a very small unit compared to gas.
pub const WEIGHT_PER_GAS: u64 = WEIGHT_PER_SECOND / GAS_PER_SECOND;

pub struct MoveVMGasWeightMapping;

// Just use provided gas.
impl gas::GasWeightMapping for MoveVMGasWeightMapping {
    fn gas_to_weight(gas: u64) -> Weight {
        gas.saturating_mul(WEIGHT_PER_GAS)
    }

    fn weight_to_gas(weight: Weight) -> u64 {
        u64::try_from(weight.wrapping_div(WEIGHT_PER_GAS)).unwrap_or(u32::MAX as u64)
    }
}
```

{% hint style="info" %}
✈️ See our instruction how to send transaction and configure gas using [UI](../getting_started/substrate.md) or [CLI](../getting_started/cli.md).
{% endhint %}

You can estimate Gas before sending transaction using [RPC calls](./rpc.md).

## Maximum block weight

Average block time in Pontem network is 6 seconds. We reserve half of second for transaction processing, so maximum block weight is:

```rust
pub const MaximumBlockWeight: Weight = WEIGHT_PER_SECOND / 2; //  10^12 / 2.
```

Maximum gas limit for Move pallet transactions is 4125000, in the future, once network stabilized, we can increase this parameter.

## Fees

Pontem network transaction fees can be paid only in **PONT** coin. A transaction fee consists of two parts: length_fee (based on transaction size) and weight_fee (based on transaction weight). 

Based on the above, the final fee of a dispatchable is:

```text
fee =
  len(tx) * length_fee +
  WeightToFee(weight)
```

Additional fee to speed up transactions is a tip, which a user sets up when sending a transaction. See how to configure tips in [Polkadot UI](../getting_started/ui.md).

Read more about [fees](https://substrate.dev/docs/en/knowledgebase/runtime/fees) in Substrate documentation.
