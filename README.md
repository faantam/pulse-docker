# Pulse Docker: Docker automation for PulseChain nodes.

Forked from Eth Docker, a docker automation project for [Ethereum](https://ethereum.org/en/upgrades/) consensus and execution clients.

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

Finally - install, configure, and run Pulse Docker:
* `cd ~ && git clone https://github.com/faantam/pulse-docker.git && cd pulse-docker`
* `./ethd install`
* `./ethd config`
* `./ethd up`

## Updating

To update Pulse Docker and the images, run:
* `./ethd update`
* `./ethd up`

## Connecting to local Grafana

Connect to http://YOURSERVERIP:3000/, log in as admin/admin, and set a new password.

> Do not expose the Grafana port to the Internet. You can use [SSH tunneling](https://www.howtogeek.com/168145/how-to-use-ssh-tunneling/)
> to reach Grafana securely over the Internet.

## License

[Apache License v2](https://github.com/eth-educators/eth-docker/blob/main/LICENSE)

# Version

This is eth-docker v2.2.8.6
