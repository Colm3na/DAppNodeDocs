# Troubleshooting

## VPN Connection issues

The VPN is one of the pillars of the whole DAppNode functioning, you will need a somehow decent and stable internet connection to be able to connect to the server.

### Os & Ios Users

As with OpenVPN the config file is automated you should not have any problem to access caused by the VPN configuration so possible causes might be:

#### NAT Loopback disabled

You are in the same local area network that the DAppNode you want to connect to, and your router does not support or does not have enabled NAT LOOPBACK, so there are IP conflicts that prevent you from connecting.

To solve it you have to figure out if your router is doing NAT LOOPBACK and activate it if it is not the case . You can easily know it by opening the terminal and typing ping + DynDNS domain is given at the end of the installation as server address. If the ping is successful (100% packets are transmitted with no packet loss) your router is doing NAT LOOPBACK, if it is not solving you have to either enter the admin panel of your router and look for that option and activate it, or edit the server address in the VPN profile and substitute the domain given by the internal IP of the DAppNode server (usually starting with 192.168……..).

This will allow you to connect from the same local network but not from outside, to connect to your DAppNode from outside your network you will need to download another VPN profile pointing to the DynDNS address (exactly as it is downloaded)

#### VPN ports closed

If your router is doing NAT LOOPBACK and you can't access your DAppNode it is very likely that the ports used to establish the VPN connection with the server are not available. To solve this you have to enter the router admin UI and open the 1194 UDP (used for establishing the VPN connection and 8090 TCP (used to get the credentials from the OTP link) ports.

If you have NAT LOOPBACK activated and the VPN ports are opened it is very likely that an extension you have installed or any setup you have that affects DNS routing might be interfering in the connection, please deactivate any piece of your configuration that can have influence DNS solving.

If you are still experiencing problems to connect from your mac / iPhone please ping us in our channels, we will be happy to help!.

### Windows users

The above situations are also relevant for Windows users, please check them before continuing reading.

If you have NAT LOOPBACK, the ports are opened and still, you can't connect the problem is in the configuration/ VPN client. Make sure you have thoroughly followed the instructions given to set up your VPN profile step by step and that you have run the following commands that can be needed for Windows machines.

Sometimes when having configured the VPN profile following the instructions the connection can not be established and you will get a “The network connection between your computer and the VPN server could not be established because the remote server is not responding”

To fix this error, a one-time registry change is required because the VPN server and/or client is behind NAT (e.g. home router). Run the following from an elevated command prompt. You must reboot your PC when finished.

#### For Windows Vista, 7, 8.x and 10

`REG ADD HKLM\SYSTEM\CurrentControlSet\Services\PolicyAgent /v AssumeUDPEncapsulationContextOnSendRule /t REG_DWORD /d 0x2 /f`

Note that you have to run this command as elevated command prompt and then reboot your computer.

#### For Windows XP

`REG ADD HKLM\SYSTEM\CurrentControlSet\Services\RasMan\Parameters /v ProhibitIpSec /t REG_DWORD /d 0x0 /f`

### Linux & Android

If you can't import the file in Linux Open VPN client make sure you have the network-manager-openvpn and network-manager-openvpn-gnome packages installed.

you can see the whole instructions for Linux [here](https://github.com/dappnode/DAppNode/wiki/OpenVPN-Client-Guide#Linux)

If you are still experiencing problems to connect from your Linux device please ping us in our channels, we will be happy to help!.

### After the v0.2.x migration, I still get the old L2TP credentials

When performing the migration from v0.1.0 after connecting via ssh or loggin in to my machine I get old L2TP credentials that can't be opened in my OpenVPN client.

The new VPN package has not finished the installation and it is not running yet, so you get old credentials again. Just wait a few minutes and connect again via SSH. Once the new VPN package is running you will get your brand new OpenVPN credentials.You can check that the migration has been correctly performed and that you are getting the new OpenVPN credentials by typing in the server terminal `docker ps` and checking that the installed core packages have the mention 0.2.0

![](https://i.imgur.com/BZYlAv2.png)

### I can't access the credentials OTP web link to download the .ovpn file

For the credentials file to be correctly donwloaded you need to have port 8090 TCP opened, please check that that port is opened.

### I see a message in the console saying that admin credentials expired

Once a file with credentials is downloaded, and used for connecting once, the link expires after that first connection and you see that message. If for any reason you need to get new credentials, connect via ssh to the server and run the command

```
dappnode_get dappnode_admin
```

If you get a "command not found" message you can run

```
docker exec -ti DAppNodeCore-vpn.dnp.dappnode.eth vpncli get dappnode_admin
```

### I can't connect with another device to my DAppNode

Only one device can access your DAppNode for each VPN credentials created; each device connected should have its own VPN configuration adding the desired devices in the Devices tab of your admin UI. You can add as many devices as you want! Be nice and provide all your friends and family access to the decentralized web, please!

### My ETH node never ends up syncing

If you are not on a 4 GB (ideally 8 GB) RAM configuration/your HD has not a high writing speed (SSD needed), it might happen that the server is not able to catch up with Ethereum Blocks, so it never gets synced. We are sorry but the server might not be able to cope up with the chain.

It is also possible that the initial sync gets stuck at a given snapshot of the first sync if this happens try removing ETHCHAIN volume and let the sync start again. To do so you have to enter in System / Ethchain / Open and hit remove volume, the existing synced snapshots will be erased and the sync will start again.

If you have a proper internet connection, an SSD disk and at least 4 GB RAM the initial sync should be fine.

### I can't access the ADMIN UI

You have to be connected to the VPN to access the ADMIN UI, in case you are connected and still not able to access just disconnect the VPN and connect it again.

### I can't install packages or it takes a lot of time to install them

For installing packages the ETH node should be synchronized. In case the node is sync and you are experiencing this, enter in System and restart IPFS and VPN and connect again.

### Ports that need to be opened

Please find in this table the ports that need to be opened for the smooth functioning of your DAppNode and installed packages.

| Service       | TCP   | UDP   |
| ------------- | ----- | ----- |
| VPN (OpenVPN) | 8090  | 1194  |
| Ethereum Node | 30303 | 30303 |
| IPFS          | 4001  | 4002  |

However, if your router supports UPnP, do not worry about this, it will manage all the ports stuff for you.

<!-- prettier-ignore-start -->
!!! info
    Note that SSH port (22) is only needed when you need ssh access to your server, and it's not a very good idea to let that port opened , consider restricting the access to your own IP or just open it when you need it and then close it
<!-- prettier-ignore-end -->

### I need to restore the system without losing any data

Execute this command in your DAppNode terminal, this will update the core packages to the latest versions without erasing any data from your volumes.

`sudo wget -O - https://github.com/dappnode/DAppNode/releases/download/v0.1.21/dappnode_install.sh | sudo UPDATE=true bash`

### I can't install a package using its ENS or IPFS hash

If the package has been uploaded to IPFS but it still does not have enough propagation in the network,it might be hard to install that package unless you are connected to an IPFS node that that allows direct or routed access to that package. As you can automatically peer-connect two IPFS nodes hosted in a DAppNode you can ask a DAppNode peer that already has installed hat package to connect your nodes. To do so, in the IPFS packages screen Go to Connect with peers and send the link to your peer. 


<p align="center">
    <img width="1000"src="https://github.com/dappnode/DAppNodeDocs/blob/master/docs/images/connectwithpeers.png?raw=true">
</p>

### I need to uninstall my DAppNode 

If for whichever strange reason you have to uninstall your DAppNode server you can do so by typing the following command in the DAppNode server console

```wget -qO - https://uninstaller.dappnode.io  |  sudo bash```

This command will uninstall DAppNode components (but not docker et al.): ⚠️ BEWARE! It will also delete all volumes and stored data!⚠️

