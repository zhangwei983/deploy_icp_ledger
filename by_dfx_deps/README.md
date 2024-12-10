# Deploy an ICP ledger locally by using `dfx deps`

This example shows how to deploy an ICP ledger locally by using `dfx deps`. 

> [!NOTE]
> This is a new feature which requires dfx version `0.24.3` or above.

## Configure the dfx.json file

You can refer to the [dfx.json](./dfx.json) file to see how to configure a pullable ICP ledger canister.

## Generate a minter identity

Run the below command to generate a minter account for next steps.

```bash
dfx identity new minter
```

> [!NOTE]
> You can use an existing identity as the minting account without creating a new one.
> Make sure you replace the `minter` identity in the commands in the following steps with the right identity.

## Start dfx

Please run the below command to start dfx locally.

```bash
dfx start --clean --background
```

## Run `dfx deps`

### Pull the dependent ICP ledger canister

Please run the below command to pull the ICP ledger canister.

```bash
dfx deps pull --network local
```

You will find a `deps` folder under your project directory, with a `pulled.json` file which contains informations about the ICP ledger.

### Prepare the ICP ledger init arguments

Please run the below command to prepare the init arguments for the ICP ledger.

```bash
dfx deps init ryjl3-tyaaa-aaaaa-aaaba-cai --argument "(variant { \
  Init = record {\
    minting_account = \"$(dfx ledger account-id --identity minter)\";\
    initial_values = vec {};\
    send_whitelist = vec {};\
    transfer_fee = opt record { e8s = 10_000 : nat64; };\
    token_symbol = opt \"LICP\";\
    token_name = opt \"Local ICP\"; \
  }\
})"
```

It will generate the `deps/init.json` file which contains the init argument in the command. You can change the arguments as you want. For more init arguments, please refer to the ICP ledger [candid file](https://github.com/dfinity/ic/blob/master/rs/ledger_suite/icp/ledger.did#L293).

> [!WARNING]
> Please replace the `minter` identity with the right identity if you didn't create it in the previous step.

### Deploy the ICP ledger locally

Please run the below command to deploy the ICP ledger locally.

```bash
dfx deps deploy
```

## Interact with the ledger

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
