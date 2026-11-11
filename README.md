# networking

A minimal network management script (`net`) for wired and WiFi interfaces,
with optional MAC randomization and VPN status detection. Designed to run
without NetworkManager.

# requirements

The `net` script depends on the following tools. Most ship with a base Debian/
Ubuntu install or are one `apt install` away.

## core (required)

| # | package            | binary used            | purpose                                  |
|---|--------------------|------------------------|------------------------------------------|
| 1 | iproute2           | `ip`                   | interface up/down, address queries       |
| 2 | isc-dhcp-client    | `dhclient`             | obtain/release IPv4 leases               |
| 3 | traceroute         | `traceroute`           | connectivity + VPN gateway detection     |
| 4 | iputils-ping       | `ping`                 | `net stats` latency measurement          |
| 5 | wpasupplicant      | `wpa_supplicant`       | WiFi association                         |
| 6 | wpasupplicant      | `wpa_passphrase`       | generate wpa_supplicant configs (`wifi w`)|
| 7 | iw                 | `iw`                   | query current WiFi link / SSID           |
| 8 | wireless-tools     | `iwlist`               | `wifi l` scan for nearby SSIDs           |
| 9 | procps             | `pkill`                | stop wpa_supplicant on disconnect        |

## optional (required only if feature enabled)

| # | package     | binary used    | purpose                                  |
|---|-------------|----------------|------------------------------------------|
| 10| macchanger  | `macchanger`   | MAC randomization on connect             |
|   |             |                | (only if `USE_MACCHANGER=1` in net.conf) |

## system services

| # | service             | purpose                          |
|---|---------------------|----------------------------------|
| 1 | systemd-resolved    | DNS resolution                   |
| 2 | systemd-timesyncd   | system clock synchronization     |

## install (Debian/Ubuntu)

```bash
sudo apt install \
    iproute2 \
    isc-dhcp-client \
    traceroute \
    iputils-ping \
    wpasupplicant \
    iw \
    wireless-tools \
    procps \
    macchanger
```

# configs

System and script configuration files used by this setup.

1. /etc/network/interfaces   # base interface config (loopback, etc.)
2. /etc/dhcp/dhclient.conf   # DHCP client options
3. /etc/systemd/timesyncd.conf # NTP servers
4. /etc/systemd/resolved.conf  # DNS / DNS-over-TLS
5. /net/net.conf              # `net` script config (see configs/net.conf template)

Templates for the last four live in `configs/` in this repo.

# usage

```
net wire c|d                 # connect / disconnect wired interface
net wifi c [ssid]            # connect WiFi (default: $WIFI_CON from net.conf)
net wifi d                   # disconnect WiFi
net wifi w <ssid> <password> # write a wpa_supplicant config for <ssid>
net wifi l                   # list nearby SSIDs
net status                   # print connectivity: n / c / v (none/connected/vpn)
net stats                    # print packet loss and latency to $TEST_IP
```

Run as root:
```bash
sudo ./net wifi c
```

Override config path per-invocation:
```bash
NET_CONF=/tmp/test.conf sudo -E ./net wifi c
```
