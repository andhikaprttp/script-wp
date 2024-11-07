
    /interface wireless set wlan1 disabled=no
    /interface vlan add name=vlan10 vlan-id=10 interface=ether2
    /interface vlan add name=vlan20 vlan-id=20 interface=ether2
    /ip address add address=192.168.71.100/24 interface=ether1
    /ip address add address=192.168.10.1/24 interface=vlan10                                                                            /ip address add address=192.168.20.1/24 interface=vlan20                                                                            /ip address add address=192.168.30.1/24 interface=ether3                                                                            /ip address add address=192.168.40.1/24 interface=wlan1                                                                             /ip dns set servers=8.8.8.8,192.168.30.2,192.168.30.3 allow-remote-requests=yes
    /ip pool add name=dhcp_pool_vlan10 ranges=192.168.30.2-192.168.30.5
    /ip pool add name=dhcp_pool_vlan20 ranges=192.168.30.2-192.168.30.5
    /ip dhcp-server add name=dhcp_vlan10 interface=vlan10 address-pool=dhcp_pool_vlan10 disabled=no
    /ip dhcp-server add name=dhcp_vlan20 interface=vlan20 address-pool=dhcp_pool_vlan20 disabled=no
    /ip dhcp-server add name=dhcp_wlan1 interface=wlan1 address-pool=dhcp_pool_vlan10 disabled=no
    /ip firewall nat add chain=srcnat out-interface=ether1 action=masquerade
    /ip route add gateway=192.168.71.1
    /tool ping google.com count=4
    /interface wireless set wlan1 ssid="andhikatkj" frequency=auto security-profile=default
    /ip hotspot setup interface=wlan1 address-pool=dhcp_pool_vlan10 dns-name=andhikatkj.local
    /ip hotspot profile set [find name=default] mac-auth-password=""
    /ip hotspot profile set [find name=default] mac-cookie-timeout=0s
    /ip hotspot user add name=guru password=gurutkj profile=default
    /ip hotspot user profile set [find name=default] rate-limit="5M/5M" shared-users=10
    /ip hotspot user add name=siswa password=siswatkj profile=default
    /ip hotspot user profile set [find name=default] rate-limit="2M/3M" shared-users=5
