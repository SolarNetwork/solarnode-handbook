# Networking

SolarNodeOS uses the [systemd-networkd][systemd-networkd-man] service to manage network devices and their settings.
A _network device_ relates to a physical network hardware device or a software networking component, as recognized
and named by the operating system. For example, the first available ethernet device is typically named `eth0` and
the first available WiFi device `wlan0`.

## Network configuration

Network configuration is stored in [`.network`][network-unit] files in the `/etc/systemd/network`
directory. SolarNodeOS comes with default support for ethernet and WiFi network devices.

The default `10-eth.network` file configures the default ethernet network `eth0` to use DHCP to automatically
obtain a network address, routing information, and DNS servers to use.

## DHCP configuration

SolarNodeOS networks are configured to use DHCP by default. If you need to re-configure a network to
use DHCP, change the configuration to look like this:

```ini title="Ethernet network with DHCP configuration"
[Match]
Name=eth0

[Network]
DHCP=yes
```

Use a **Name** value specific to your network.

## Static network configuration

If you need to use a static network address, instead of DHCP, edit the network configuration file
(for example, the `10-eth.network` file for the ethernet network), and change it to look like this:

```ini title="Ethernet network with static address configuration"
[Match]
Name=eth0

[Network]
DNS=1.1.1.1

[Address]
Address=192.168.3.10/24

[Route]
Gateway=192.168.3.1
```

Use **Name**, **DNS**, **Address**, and **Gateway** values specific to your network. The same static
configuration for a single address can also be specified in a slightly more condensed form, moving
everything into the `[Network]` section:

```ini title="Ethernet network with condensed single static address configuration"
[Match]
Name=eth0

[Network]
Address=192.168.3.10/24
Gateway=192.168.3.1
DNS=1.1.1.1
```


## WiFi network configuration

The default `20-wlan.network` file configures the default WiFi network `wlan0` to use DHCP to
automatically obtain a network address, routing information, and DNS servers to use. To configure
the WiFi network SolarNode should connect to, run this command:

```sh title="Configuring the SolarNode WiFi network"
sudo dpkg-reconfigure sn-wifi
```

You will then be prompted to supply the following WiFi settings:

 1. Country code, e.g. `NZ`
 2. WiFi network name (SSID)
 3. WiFi network password

!!! note "Note about WiFi support"

	WiFi support is provided by the `sn-wifi` package, which may not be installed. See the [Package
	Maintenance](packages.md#install-packages) section for information about installing packages.

## WiFi Auto Access Point mode

For initial setup of a the WiFi settings on a SolarNode it can be helpful for SolarNode to create
its own WiFi network, as an access point. The `sn-wifi-autoap@wlan0` service can be used for this.
When enabled, it will monitor the WiFi network status, and when the WiFi connection fails for any
reason it will enable a `SolarNode` WiFi network using a gateway IP address of `192.168.16.1`. Thus
when the SolarNode access point is enabled, you can connect to that network from your own device and
reach the [Setup App](../setup-app/index.md) at `http://192.168.16.1/` or the command line via `ssh
solar@192.168.16.1`.

The default `21-wlan-ap.network` file configures the default WiFi network `wlan0` to act as an
Access Point

This service is not enabled by default. To enable it, run the following:

```sh
sudo systemctl enable --now sn-wifi-autoap@wlan0
```

Once enabled, if SolarNode cannot connect to the configured WiFi network, it will create its own
`SolarNode` network. By default the password for this network is `solarnode`. The Access Point
network configuration is defined in the `/etc/wpa_supplicant/wpa_supplicant-wlan0.conf` file, in a
section like this:

```
### access-point mode
network={
    ssid="SolarNode"
    mode=2
    key_mgmt=WPA-PSK
    psk="solarnode"
    frequency=2462
}
```

!!! tip

	If you do not have a `wpa_supplicant-wlan0.conf` file you can make a copy of the example
	provided by the `sn-wifi` package:

	```sh
	# copy the example
	sudo cp /usr/share/solarnode/example/wpa_supplicant-wlan0.conf  /etc/wpa_supplicant/

	# set appropriate file permissions
	sudo chmod 600 /etc/wpa_supplicant/wpa_supplicant-wlan0.conf
	```

	Then edit the `/etc/wpa_supplicant/wpa_supplicant-wlan0.conf` as needed.

## Firewall

SolarNodeOS uses the [nftables][nftables] system to provide an IP firewall to SolarNode. By
default only the following incoming TCP ports are allowed:

| Port | Description |
|:-----|:------------|
| 22   | SSH access |
| 80   | HTTP SolarNode UI |
| 8080 | HTTP SolarNode UI alternate port |

### Open additional IP ports

You can edit the `/etc/nftables.conf` file to add additional open IP ports as needed. A good place
to insert new rules is **after** the lines that open ports 80 and 8080:

```
# Allows HTTP
add rule ip filter INPUT tcp dport 80 counter accept
add rule ip filter INPUT tcp dport 8080 counter accept
```

For example, if you would like to open UDP port 50222 to support the Weatherflow Tempest weather
station, add the following **after** the above lines:

```
# Allow WeatherFlow Tempest messages
add rule ip filter INPUT udp dport 50222 counter accept
```

### Reload firewall rules

If you make changes to the firewall rules in `/etc/nftables.conf`, run the following command
to reload the firewall configuration:

```sh
sudo systemctl reload nftables
```

[dhcp]: https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol
[dns]: https://en.wikipedia.org/wiki/Domain_Name_System
[network-unit]: https://manpages.debian.org/bullseye/systemd/systemd.network.5.en.html
[nftables]: https://en.wikipedia.org/wiki/Nftables
[systemd-networkd-man]: https://manpages.debian.org/bullseye/systemd/systemd-networkd.8.en.html
