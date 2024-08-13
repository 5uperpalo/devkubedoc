# OpenVPN *[1 min read]*

Open VPN server is running on 10.1.24.200:1194 (saiacheron SAI server)
Based on instructions from [Ubuntu OpenVPN manual](https://ubuntu.com/server/docs/how-to-install-and-use-openvpn).

## Add new user on a server

```
# create home directory for user and define bash as default shell
sudo useradd -m -s /bin/bash USERNAME
sudo passwd USERNAME
# possibly add user to sudo group
sudo usermod -aG sudo USERNAME
```

## Create VPN certs on saicheron for external collaborators
<details><summary>create certificates</summary>

```
sudo -i
cd /etc/openvpn/easy-rsa
./easyrsa gen-req USERNAME nopass
./easyrsa sign-req client USERNAME
```

</details>
<details><summary>copy, compress (possibly with encryption) certificates</summary>

```
mkdir sai_openvpn_certs
sudo cp /etc/openvpn/easy-rsa/pki/private/USERNAME.key sai_openvpn_certs/
sudo cp /etc/openvpn/easy-rsa/pki/issued/USERNAME.crt sai_openvpn_certs/
sudo cp /etc/openvpn/easy-rsa/pki/ca.crt sai_openvpn_certs/
sudo cp /etc/openvpn/ta.key
tar -czvf sai_openvpn_certs.tar.gz sai_openvpn_certs/
# with encryption
# tar -czv sai_openvpn_certs/ | openssl enc -aes-256-cbc -pbkdf2 -iter 100000 -e > sai_openvpn_certs.tar.gz.enc

```
</details>

## OpenVPN client

Follow the instructions [here](https://ubuntu.com/server/docs/vpn-client-software-implementations). For Windows: copy all created certificates + adjusted [.ovpn](https://5uperpalo.github.io/devkubedoc/ovpn_exampleexample_client.ovpn) to `C:\Program Files\OpenVPN\config`

## Allow static source IP on UDP 1194 to sai-jupyterhub.cttc.es

Create a ticket in [CTTC CSI system](https://glpi.cttc.es) to allow the communciation from a static IP address to the OpenVPN server
`https://glpi.cttc.es/`.
Related ticket to open static ports for Optare collaborators: [glpi](https://glpi.cttc.es/front/ticket.form.php?id=22032&).
