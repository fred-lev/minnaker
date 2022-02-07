# Minnaker

Note: This is relying on the work of [alexlep](https://github.com/alexlep) from this repo [spin-brewery](https://github.com/alexlep/spin-brewery)

Setup vm with at least 4VCPU and 16GB MEM

As time of writing the installation comes down to ruining the following commands as root:

On an ubuntu 18.04 vm run

## install docker and docker compose

Set docker-compose [release](https://docs.docker.com/compose/release-notes/)

```console
sudo -s
apt-get update
apt-get install \
apt-transport-https \
ca-certificates \
curl \
gnupg \
lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
curl -L "https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-$(uname -s)-$(uname -m)" \
       -o /usr/local/bin/docker-compose  \
       && chmod +x /usr/local/bin/docker-compose \
       &&  ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

```console
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

## Manage Docker as a non-root user

```console
sudo groupadd docker
sudo usermod -aG docker $USER
```

## install python3 and python3-yaml

```console
sudo apt-get install python3 python3-yaml
```

Then clone:

```console
git clone https://github.com/alexlep/spin-brewery
cd spin-brewery
python3 brewery.py --show-available-releases
export SPINNAKER_RELEASE=1.26.6
python3 brewery.py --generate-docker-compose
```

Create the required database in spin-db

```console
docker-compose up -d spin-db
docker exec -it spin-brewery-spin-db-1 sh -c 'exec mysql -uroot -pspinnaker < /opt/spinnaker/sql/mysql.sql'
```

Bring up the all stack:

```console
docker-compose up -d
```
