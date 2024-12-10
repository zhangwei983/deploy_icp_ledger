# Deploy an ICP ledger locally by using the Wasm file

This example shows how to deploy an ICP ledger locally. It mainly follows this [instruction](https://internetcomputer.org/docs/current/developer-docs/defi/tokens/ledger/setup/icp_ledger_setup#deploying-the-ledger-locally). 

## Generate a minter identity

Run the below command to generate a minter account for next steps.

```bash
dfx identity new minter
```

> [!NOTE]
> You can use an existing identity as the minting account without creating a new one.
> Make sure you replace the `minter` identity in the commands in the following steps with the right identity.

## Configure the dfx.json file

You can refer to the [dfx.json](./dfx.json) file to see how to configure the ICP ledger canister. But you still need to modify it a little bit.

### Check the binary revision

At the time of writing, the replica binary revision is `f9085dbe8d86dcca77b05adaee93d608498dae0b`, you can check the [releases](https://dashboard.internetcomputer.org/releases) to get the latest release and replace `f9085dbe8d86dcca77b05adaee93d608498dae0b` with your expected revision number.

### Update the accounts

There're two accounts needs to be replaced in the [dfx.json](./dfx.json) file:

- Minting account
  Replace `e58e55e265c5b21ccaf0d51d2a2bdaca7c6fae990dd440919c7148e9d621fc78` with the account returned by `dfx ledger account-id --identity minter`.
- Default account 
  Replace `8494c01329531c06254ff45dad87db806ae6ed935ad6a504cdbc00a935db7b49` with the account returned by `dfx ledger account-id --identity ***` to receive ICP tokens.

> [!NOTE]
> Make sure the default account is different to the minting account.
> For more init arguments, please refer to the ICP ledger [candid file](https://github.com/dfinity/ic/blob/master/rs/ledger_suite/icp/ledger.did#L293).

## Deploy

- Start local dfx  
  `dfx start --clean --background`

- Deploy the ICP ledger canister
  `dfx deploy --specified-id ryjl3-tyaaa-aaaaa-aaaba-cai icp_ledger`
  Use `--specified-id` to specify the exact same canister id as the one of the ICP ledger on the mainnet.

## Interact with the ICP ledger

Run
```bash
dfx canister call icp_ledger name
```
will get
```bash
(record { name = "Local ICP" })
```

Run
```bash
dfx canister call icp_ledger symbol
```
will get
```bash
(record { symbol = "LICP" })
```

### Minting

Minting is a transfer call from the minting account. You can run the below command to mint from the minting account.

```bash
dfx ledger transfer --amount 100 --memo 12345 --fee 0 $(dfx ledger account-id) --identity minter
```

> [!WARNING]
> - Make sure you're using the minting identity which is consistent with the minting account you set in the dfx.json file.
> - Please make sure `$(dfx ledger account-id)` is the account that you want to receive ICP tokens.
> - For minting/burning operation, the `--fee` has to be `0`.
