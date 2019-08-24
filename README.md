# Raspberry Pi VPN Server using a Docker container

## 1 - Instructions

I had been looking for a way to turn my Raspberry PI into a VPN server so that I can access my Octopi from my phone while away. I tried a few continers and finally got this one working. Sharing the knowledge with the community in case this helps anyone.

### 1.1 - Install Docker

Run the following command:

```bash
curl -sSL https://get.docker.com | sh
```
> **Note:** remember to add the pi user to the docker group. The instructions are provided when the scripts finishes running.

### 1.2 - Create the docker image from source

Note there's an image on Docker hub, but if you run that image, it will not work. These commands will create an image that will run from the Pi:

```bash
git clone https://github.com/hwdsl2/docker-ipsec-vpn-server.git
cd docker-ipsec-vpn-server
docker build -t hwdsl2/ipsec-vpn-server .
```
At the end of building the image, you should have a new image called:

```text
hwdsl2/ipsec-vpn-server:latest 
```

You can verify this by running:

```bas
docker images ls
```

### 1.3 Create the environment file

Create the (``` nano vpn.env``` file and add the following lines:

```text
VPN_IPSEC_PSK=your_ipsec_pre_shared_key
VPN_USER=your_vpn_username
VPN_PASSWORD=your_vpn_password
```

If you want to have more users add the following lines

```text
VPN_ADDL_USERS=additional_username_1 additional_username_2
VPN_ADDL_PASSWORDS=additional_password_1 additional_password_2
```

If you want to change the Google DNS server, add the following lines:

```text
VPN_DNS_SRV1=1.1.1.1
VPN_DNS_SRV2=1.0.0.1
```

### 1.2 Verify IPsec NETKEY kernel module

Run the following command:

```bash
sudo modprobe af_key
```

> **Note:** If you get an error this may indicate a problem with your Noobs installation

### 1.3 Launch the container

Run the following command:

```bash
docker run \
    --name vpnserver \
    --env-file ./vpn.env \
    --restart=always \
    -p 500:500/udp \
    -p 4500:4500/udp \
    -d --privileged \
    hwdsl2/ipsec-vpn-server
```

### 2.0 More instructions

The author of the container has more instructions at:

https://github.com/hwdsl2/docker-ipsec-vpn-server/blob/master/README.md


