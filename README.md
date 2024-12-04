# Deploy an ICP ledger locally

This example shows how to deploy an ICP ledger locally. It mainly follows this [instructions](https://internetcomputer.org/docs/current/developer-docs/defi/tokens/ledger/setup/icp_ledger_setup#deploying-the-ledger-locally). 


## Configure the dfx.json file

You can refer to the [dfx.json](./dfx.json) file to see how to configure the ICP ledger canister. But you still need to modify it a little bit.

### Check the binary revision

At the time of writing, the replica binary revision is `f9085dbe8d86dcca77b05adaee93d608498dae0b`, you can check the [releases](https://dashboard.internetcomputer.org/releases) to get the latest release and replace `f9085dbe8d86dcca77b05adaee93d608498dae0b` with your expected revision number.

### Update the accounts

There're two accounts in the [dfx.json](./dfx.json) file:
- Minting account: `e58e55e265c5b21ccaf0d51d2a2bdaca7c6fae990dd440919c7148e9d621fc78`
  This is the account that can mint tokens by calling `transfer` method.
- Default account: `8494c01329531c06254ff45dad87db806ae6ed935ad6a504cdbc00a935db7b49`
  This is the account that will be initialized with a given token number.

Please replace them with your account, run `dfx ledger account-id` to get the account ids.

## Deploy

- Start local dfx  
  `dfx start --clean --background`

- Deploy the ICP ledger canister
  `dfx deploy --specified-id ryjl3-tyaaa-aaaaa-aaaba-cai icp_ledger_canister`
  Use `--specified-id` to specify the exact same canister id as the one of the ICP ledger on the mainnet.

## Interact with the ledger

Run
```bash
dfx canister call icp_ledger_canister name
```
will get
```bash
(record { name = "Local ICP" })
```

Run
```bash
dfx canister call icp_ledger_canister symbol
```
will get
```bash
(record { symbol = "LICP" })
```

### Minting

Minting is a transfer call from the minting account. You can run below command to mint from the minting account.

```bash
dfx ledger transfer --amount 100 --memo 12345 --fee 0 8494c01329531c06254ff45dad87db806ae6ed935ad6a504cdbc00a935db7b49 --identity minter
```

- Make sure you're using the minting identity which is consistent with the minting account you set in the dfx.json file
- For minting operation, the `--fee` has to be `0`.
