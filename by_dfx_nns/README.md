# Deploy an ICP ledger locally by using `dfx nns`

This example shows how to deploy an ICP ledger locally by using `dfx nns`. Since the ICP ledger is part of the NNS, this command will also install an ICP ledger with canister ID `ryjl3-tyaaa-aaaaa-aaaba-cai`. 

It mainly follows this [instruction](https://internetcomputer.org/docs/current/developer-docs/developer-tools/cli-tools/cli-reference/dfx-nns#dfx-nns-install). 

This way of deploying the ICP ledger is heavily than the other two ways listed in this [document](../README.md).

## Install `dfx nns` extension

Please run the below command to install the `dfx nns` extension.

```bash
dfx extension install nns
```

> [!NOTE]
> There is no document for `dfx extension` under [dfx](https://internetcomputer.org/docs/current/developer-docs/developer-tools/cli-tools/cli-reference/) document page.
> You can run `dfx extension -h` to display the usage information.

## Generate `~/.config/dfx/networks.json` file

Generate `~/.config/dfx/networks.json` file with below content.

```json
{
  "local": {
    "bind": "127.0.0.1:8080",
    "type": "ephemeral",
    "replica": {
      "subnet_type": "system"
    }
  }
}
```

This is because the NNS canisters need to run on a `system` subnet.

## Start dfx

Please run the below command to start dfx locally.

```bash
dfx start --clean --background
```

## Install nns

Please run the below command install nns.

```bash
dfx nns install --ledger-accounts $(dfx ledger account-id)
```

The [dfx.json](dfx.json) file doesn't contain any definition for canisters, but it's necessary for `dfx nns install` command.

> [!NOTE]
> You need to provide the test account with `--ledger-accounts` to have ICP tokens initialized to your account.
> You can provide multiple ledger accounts which are separated by spaces.

After installing, you will see the below output

```bash
######################################
# NNS CANISTER INSTALLATION COMPLETE #
######################################

Backend canisters:
nns-registry          rwlgt-iiaaa-aaaaa-aaaaa-cai
nns-governance        rrkah-fqaaa-aaaaa-aaaaq-cai
nns-ledger            ryjl3-tyaaa-aaaaa-aaaba-cai
nns-root              r7inp-6aaaa-aaaaa-aaabq-cai
nns-cycles-minting    rkp4c-7iaaa-aaaaa-aaaca-cai
nns-lifeline          rno2w-sqaaa-aaaaa-aaacq-cai
nns-genesis-token     renrk-eyaaa-aaaaa-aaada-cai
nns-identity          rdmx6-jaaaa-aaaaa-aaadq-cai
nns-ui                qoctq-giaaa-aaaaa-aaaea-cai
nns-ic-ckbtc-minter   qjdve-lqaaa-aaaaa-aaaeq-cai
nns-sns-wasm          qaa6y-5yaaa-aaaaa-aaafa-cai


Frontend canisters:
internet_identity     http://qhbym-qaaaa-aaaaa-aaafq-cai.localhost:8080/
nns-dapp              http://qsgjb-riaaa-aaaaa-aaaga-cai.localhost:8080/
sns-aggregator        http://sgymv-uiaaa-aaaaa-aaaia-cai.localhost:8080/
```

You might see below messages keep being output to the console:

```bash
2024-12-02 05:28:00.194215548 UTC: [Canister sgymv-uiaaa-aaaaa-aaaia-cai] Getting upstream data...
2024-12-02 05:28:00.194215548 UTC: [Canister sgymv-uiaaa-aaaaa-aaaia-cai] Maybe have SNSs
2024-12-02 05:28:00.194215548 UTC: [Canister sgymv-uiaaa-aaaaa-aaaia-cai] Need to get more SNSs
2024-12-02 05:28:00.194215548 UTC: [Canister sgymv-uiaaa-aaaaa-aaaia-cai] Asking for more SNSs
2024-12-02 05:28:00.194215548 UTC: [Canister sgymv-uiaaa-aaaaa-aaaia-cai] Asked for more SNSs
2024-12-02 05:28:00.194215548 UTC: [Canister sgymv-uiaaa-aaaaa-aaaia-cai] Yay, got 0 SNSs: {"instances":[]}
```

These are logs from the `sns-aggregator` canister, please ignore them.

## Interact with the ICP ledger

Please open a new terminal to verify the deployment.

Run
```bash
dfx canister call ryjl3-tyaaa-aaaaa-aaaba-cai name
```
will get
```bash
(record { name = "Internet Computer" })
```

Run
```bash
dfx canister call ryjl3-tyaaa-aaaaa-aaaba-cai symbol
```
will get
```bash
(record { symbol = "ICP" })
```

Run
```bash
dfx ledger balance
```

If you add the ledger account to the previous initialization step, you will see output as below:
```bash
1000000000.00000000 ICP
```

## Open questions

### ICP ledger minting account

The log shows the ledger minting account is `082ecf2e3f647ac600f43f38a68342fba5b8e68b085f02592b77f39808a8d2b5`, not sure where it comes from.

Update:  
`082ecf2e3f647ac600f43f38a68342fba5b8e68b085f02592b77f39808a8d2b5` is the account id of the `nns-governance` canister.

Please run the below command to verify.

```bash
dfx ledger account-id --of-principal rrkah-fqaaa-aaaaa-aaaaq-cai
```

### Minting ICP

For now, adding testing ledger accounts is the only way to get testing ICP tokens while installing nns canisters. Not sure how to set up a minting account that can be used to transfer ICP tokens without limitations.

### ICP testing account

The log shows there are two default testing accounts:

- `5b315d2f6702cb3a27d826161797d7b2c2e131cd312aece51d4d5574d1247087`
- `2b8fbde99de881f695f279d2a892b1137bfe81a42d7694e064b1be58701e1138`

Not sure where they come from.

Update:  
[Here](https://internetcomputer.org/docs/current/developer-docs/developer-tools/cli-tools/cli-reference/dfx-nns#example-accessing-icp-on-the-command-line) describes the origination of these two accounts. You can still use `--ledger-accounts` to add extra testing accounts.
