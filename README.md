# Pulse Docker: Docker automation for PulseChain nodes.

Forked from Eth Docker, a docker automation project for [Ethereum](https://ethereum.org/en/upgrades/) consensus and execution clients.

## Getting Started

Please see the [official Eth Docker documentation](https://eth-docker.net).

For a quick testnet start, you can install docker-ce:

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

Set up a user not name `root`, such as `docker`:

TODO: add instructions on setting up a docker user.

Finally - install, configure, and run Pulse Docker:

* `cd ~ && git clone https://github.com/faantam/pulse-docker.git && cd pulse-docker`
* `./ethd install`
* `./ethd config`
* `./ethd up`

## License

[Apache License v2](https://github.com/eth-educators/eth-docker/blob/main/LICENSE)

# Version

This is eth-docker v2.2.8.6
