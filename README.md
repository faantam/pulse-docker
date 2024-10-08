# Pulse Docker: Docker automation for PulseChain nodes.

Forked from [Eth Docker](https://github.com/eth-educators/eth-docker), a simple yet configurable way to run [Ethereum](https://ethereum.org/roadmap/) nodes. Pulse Docker currently supports Go-Pulse and Prysm for its execution and consensus clients, respectively. You can run a validator node, manage keys, and run Grafana all from Pulse Docker!

####  Your donations are greatly appreciated and make a difference. Thank you for your support!

(PRC20) : `0x12d560D1d196eE4D62Ce827c9EEc2eD3ECc5827f`

## Getting Started

For a quick testnet start with Ubuntu, you can install docker-ce:

```bash
sudo apt-get update
sudo apt-get -y install ca-certificates curl gnupg lsb-release whiptail
sudo mkdir -p /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

Add the current Unix user to the `docker` group:

```bash
sudo usermod -aG docker ${USER}
```

Next - install and configure Pulse Docker:
* `cd ~ && git clone https://github.com/faantam/pulse-docker.git && cd pulse-docker`
* `./ethd install`
* `./ethd config`

Below is the GUI that will pop up after running `./ethd config`. Press `Enter` to accept the option or `Esc` to exit. Use the arrow keys to select different options.
![Network Options](https://github.com/user-attachments/assets/6809a5c9-19fc-4583-9c58-97d79c1ca83f)

If you want to use the path-based database scheme, then edit the `~/pulse-docker/.env` file. Change the line that says `EL_EXTRAS=` to `EL_EXTRAS=--state.scheme=path`.

To start syncing, run `./ethd up`. This will take at least eight hours. You can check the status by [going to Grafana](https://github.com/faantam/pulse-docker/tree/main?tab=readme-ov-file#connecting-to-local-grafana) or checking the Docker logs.

To see the full list of commands, run `./ethd -h`

## Connecting to local Grafana

Connect to http://YOURSERVERIP:3000/, log in as admin/admin, and set a new password. Replace "YOURSERVERIP" with localhost if you're on your validator computer. If you're on another computer within your LAN network, use your validator computer's private IP.

To see the available dashboards, go to http://YOURSERVERIP:3000/dashboards. These are my favorite ones that are preloaded in Pulse Docker: _Home Staking Dashboard_, _Pulse Docker Logs_, _Ethereum Metrics Exporter (Single)_, and _geth_dashboard_.

> Do not expose the Grafana port to the Internet. You can use [SSH tunneling](https://www.howtogeek.com/168145/how-to-use-ssh-tunneling/)
> to reach Grafana securely over the Internet.

## Creating keys

For mainnet, best practice is to create keys using a Linux Live USB and the official [staking-deposit-cli](https://gitlab.com/pulsechaincom/staking-deposit-cli). Here is a [YouTube walkthrough](https://www.youtube.com/watch?v=oDELXYNSS5w) for this process. Make sure to safeguard your mnemonic and only ever keep it offline! In steel and in a safe is best.

> The YouTube video is for Ethereum. The process is the same for PulseChain, except we use [this codebase](https://gitlab.com/pulsechaincom/staking-deposit-cli) and the `--chain=pulsechain` or `--chain=pulsechain-testnet-v4` flags when running the deposit.sh script.

### If you want to create keys with Pulse Docker, stay in this section (otherwise, skip to [Importing keys](https://github.com/faantam/pulse-docker/tree/main#importing-keys))

If you are going to use the deposit-cli that is bundled with Pulse Docker, please make sure to edit `.env` and that the `COMPOSE_FILE` line contains `:deposit-cli.yml`. You can edit with `nano .env`.

Make sure you're in the project directory, `cd ~/pulse-docker` by default.

When creating keys, you can specify a PulseChain address that withdrawals will be paid to. If you have a hardware wallet that withdrawals should go to, this is a good option.
> Make sure the PulseChain address is correct, you cannot change it after you deposit. You can also remove that parameter, in which  case withdrawals would be done with the mnemonic seed, not against a fixed address.

**Do not** set your withdrawal address to an exchange wallet. The funds will not
be credited, and you will battle support for them.

This command will create the keys to deposit PLS against:

`./ethd cmd run --rm deposit-cli-new --execution_address YOURHARDWAREWALLETADDRESS --uid $(id -u)`
> Specifying the uid is optional. If this is not done, the generated files will be owned by the user with uid `1000`

Choose the number of validators you wish to create.
> A validator is synonymous to one 32 million Pulse stake. Multiple validators can be imported to a single validator client.

The created files will be in the directory `.eth/validator_keys` in this project.
> staking-deposit-cli shows you a different directory, that's because it has a view from inside the container.
 
This is also where you'd place your own keystore files if you already have some for import.

### Test your Seed Phrase

From the project directory:

```
./ethd cmd run --rm deposit-cli-existing --folder seed_check --execution_address YOURHARDWAREWALLETADDRESS --uid $(id -u)
```
> Specifying the uid is optional. If this is not done, the generated files will be owned by the user with uid `1000`

Select your language preference.

Type your mnemonic seed.

Enter the index (key number). 
> If generated 1 previous validator key file and entered 1 initially, then it is index[0]. So you will enter 0. Hence, you are entering the index from which to start generating the key file from.

Enter how may new validators you wish to run.
> Enter the number of validators you entered when initially generating the key file.
> If you are running 1 previous validator and entered 1 initially, then enter 1.

Type any password you like, as you'll throw away the duplicate `keystore-m` files.

Compare the `deposit_data` JSON files to ensure the files are identical.
```
diff -s .eth/validator_keys/deposit_data*.json .eth/seed_check/deposit_data*.json
```

Cleanup duplicate deposit_data.
```
rm .eth/seed_check/*
```

## Importing keys

### Prysm - create a wallet

Prysm requires a wallet first. Run `./ethd cmd run --rm create-wallet`, which will set up a wallet and a password for it. You can then print the password with `./ethd keys get-prysm-wallet`

### Start the client and import keys

`./ethd up` to start the client and the client's keymanager API

`./ethd keys` to see all options available to you

`./ethd keys import` to import keys and their slashing protection data. This looks in `.eth/validator_keys` for `*keystore*.json` files and optionally `slashing_protection*.json` files.

If the key JSON files are in another directory, run:

`./ethd keys import --path PATHTOKEYS`

replacing `PATHTOKEYS` with the actual path where they are.

`./ethd keys list` to list all imported keys

> After import, the files in `.eth/validator_keys` can be safely removed from the node,
> once you have copied them off the node. You'll need the `deposit_data` file to
> deposit at the launchpad. The `keystore-m` files can be safeguarded in case
> the node needs to be rebuilt, or deleted and recreated from mnemonic if required.

## Depositing at launchpad

**Caution**: You may wish to wait until the consensus and execution client are fully synchronized before you deposit. Check their logs with `./ethd logs -f --tail 50 consensus` and `./ethd logs -f --tail 50 execution`. This safe-guards against the validator being marked offline if your validator is activated before the consensus client syncs.

Once you are ready, you can send PLS to the deposit contract by using
the `.eth/validator_keys/deposit_data-TIMESTAMP.json` file at the [Testnet launchpad](https://launchpad.v4.testnet.pulsechain.com/en/)
or [Mainnet launchpad](https://launchpad.pulsechain.com/).

## Updating

To update Pulse Docker and the images, run:
* `./ethd update`
* `./ethd up`

## Using a client's specific version

To use a specific version of a client, edit the `.env` file. Change the Docker tag of the relevant client.

For example: To use v3.1.1 for Go-Pulse, change `GETH_DOCKER_TAG=latest` to `GETH_DOCKER_TAG=v3.1.1`. To apply the changes, run `./ethd up`.

## Voluntary exit

To exit, run `./ethd cmd run --rm validator-exit` and follow the prompts.

## License

[Apache License v2](https://github.com/faantam/pulse-docker/blob/main/LICENSE)
