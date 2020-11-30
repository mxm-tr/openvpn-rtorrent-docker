# VPN Torrent downloader

It features:
 * rTorrent (a torrent client),
 * flood (a web interface for rTorrent),
 * OpenVPN Client (to tunnel traffic through your ISP) and Server (Optional for testing)
 * a simple iptables firewall to allow rTorrent to only access the internet through a VPN.

Built on top of this file:
https://gist.github.com/0xcaff/2c151e649aebe85cca7a2503cf6e0bd9#file-docker-compose-yml

## Setup

If you have a VPN provider, you will be provided a client configuration file to connect to it.
If you don't, use the setup described in the **Self hosted VPN server** section.

Place your `.ovpn` configuration file under `openvpn-data/client-conf/`

```bash
$ ls openvpn-data/client-conf
vpn.ovpn
```

Do the dab, run
```bash
docker-compose up
```

And visit http://localhost:3000

## Self hosted VPN server

If you don't have a VPN server yet, you can run your own for testing.

⚠️ While testing with it, privacy is not guaranteed! 

### 1. Generate your OpenSSL keys:

Ok, you have been warned enough now.

```bash
$ docker build -t media-center_openvpn-server ./docker-images/openvpn-server/
...
Successfully built 63a4b12f9784
Successfully tagged media-center_openvpn-server:latest

$ docker run --rm -it -v $(pwd)/openvpn-data/server-conf:/etc/openvpn media-center_openvpn-server ovpn_genconfig -u udp://openvpn-server
...
Successfully generated config
Cleaning up before Exit ...


$ docker run --rm -it -v $(pwd)/openvpn-data/server-conf:/etc/openvpn media-center_openvpn-server ovpn_initpki
...
An updated CRL has been created.
CRL file: /etc/openvpn/pki/crl.pem


$ docker run --rm -it -v $(pwd)/openvpn-data/server-conf:/etc/openvpn media-center_openvpn-server easyrsa build-client-full vpn nopass
docker run --rm -it -v $(pwd)/openvpn-data/server-conf:/etc/openvpn media-center_openvpn-server ovpn_initpki
docker run --rm -it -v $(pwd)/openvpn-data/server-conf:/etc/openvpn media-center_openvpn-server ovpn_initpki
```

They will be saved under openvpn-data/server-conf.

### 2. Uncomment the openvpn-server block in the docker-compose file:

This will launch an OpenVPN server using your generated keys.

Again, BEWARE! The two ends of the magical VPN pipe will be in your server!

# Thanks

To all the f****** geniuses in the Open Source community who makes this kind of clunky setup possible