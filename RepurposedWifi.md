Here is my /etc/network/interfaces file:
```
auto lo
iface lo inet loopback
auto eth0
iface eth0 inet static
    address 192.168.2.1
    netmask 255.255.255.0
allow-hotplug wlan0
iface wlan0 inet manual
wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
iface default inet dhcp

```
Here is my /etc/network/if-up.d file:
```
#!/bin/sh
echo “1” > /proc/sys/net/ipv4/ip_forward
iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE
```
Here is my wpa_supplicant.conf:
```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
        ssid=“home-f901f82d28-Wireless”
        psk=“neighborspassword1”
        proto=RSN
        key_mgmt=WPA-PSK
        pairwise=TKIP
        group=TKIP WEP104 WEP40
        auth_alg=OPEN
}
network={
        ssid=“HOME-213C”
        psk=“neighborspassword2”
        proto=RSN
        key_mgmt=WPA-PSK
        pairwise=TKIP
        auth_alg=OPEN
}
```

I just noticed this but you should probably change the ip_forward param in sysctl not have it in ifup, damn I'm stupid,
