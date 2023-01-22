# Self host Standard-Notes on ARM devices (like Raspberry Pi or Apple M1/M2)
Standard Notes is a great Project that many people came to love and enhance. Since I am really paranoid on my notes (which probably don't interest any one else) self-deployment is the only way to keep all data in my own network. 

Neverless, if you can feel free to enhance the experience for everybody by contributing to the probject over at [How to Contribute to SN](https://docs.standardnotes.com/contribute/)!

There are some guides out there describing how to deploy it on ARM Devices like the Raspberry Pi, but I had to use different Guides and a lot of research to get it up and running. I hope this Guide will help you do this on the fly. 

In case you notice this does not work anymore, hit me up on  Discord (Teeseus#9899) or via the issues.

## Table of Contents

1. [Base dependencies](#base-dependencies)
	1. [install Docker](#install-docker)
	2. [install Docker-Compose](#install-docker-compose)
2. [install Standardnotes](#install-standardnotes)
	1. [Retag database Images](#retag-database-images)
	2. [Run the Standalone](#run-the-standalone)
	3. [Adding your subscription](#adding-your-subscription)
	4. [Configure the files server](#configure-the-files-server)

## Base dependencies

You will need some dependencies on your system. Install them if they are not already installed.

### install Docker

If you have not installed docker on your system yet, install it from the official script:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

Check if you can access the docker service:

```bash
docker run hello-world
``` 

### install Docker-Compose

If you have not installed docker-compose on your system, install it:

```bash
sudo apt-get install docker-compose
```

Check if docker-compose was installed correctly:

```bash
docker compose version
```

## Install Standardnotes

Standardnotes provides docker images for ARM-systems by now. Although some Modifications are needed. This Guide will go through it step by step, so you have the original and safe experience provided by Standardnotes!

### Retag database Images

Pull the ARM-compatible database and retag it:

```bash
docker pull mariadb
docker image tag mariadb:latest mysql:5.6
docker image tag mariadb:latest mysql:8
```

Now you have all images built locally.

### Run the Standalone

Clone the [Self Hosting Repository](https://github.com/standardnotes/self-hosted):

```bash
cd ~
git clone https://github.com/standardnotes/self-hosted
cd self-hosted
```

From here it goes closely to the [official Guide](https://docs.standardnotes.com/self-hosting/docker). Start of with setting up the environment and generate your own secrets:

```bash
bash ./server.sh setup
bash ./server.sh generate-keys
```

Start up the server:

```bash
bash ./server.sh start
```

### Adding your subscription

After you have registered an account to this server you will need to add a subscription to enable all features:

```bash
cd ~
cd self-hosted
bash ./server.sh create-subscription <your@email.here>
```

This will enable all features for your account.

### Configure the files server

Enter the `self-hosted` folder and edit your `.env` file.

```bash
cd ~/self-hosted
nano .env
```

Scroll down until you find the key `FILES_SERVER_URL`. Change the value to the ip or domain/url the server is accessible.

```bash
FILES_SERVER_URL=http://<accessible_server_ip_or_domain>:3125
```

In case you configured your server to use HTTPS, don't forget to use the https protocoll here as well.

In my case I have the files server (standard port 3125) rerouted at the main server on the path /files:

```bash
FILES_SERVER_URL=https://<your_domain>/files
```

Save the file and restart the docker containers:

```bash
bash ./server.sh stop
bash ./server.sh start
```

Now you should be able to upload files to your SN instance.
