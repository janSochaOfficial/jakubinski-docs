# hasła 
login: `administrator`
hasło: `zaq1@WSX`

# konfiguracja karty sieciowej

- `sudo su`
- `cd etc/netplan`
- `nano 50-cloud-init.yaml`
	- tutaj zmiany zgodnie z poleceniem
- `netplan try`
- `ip a`

# zmiana nazwy
- `nano /etc/hosts`
	- zmień ==server== na ==nazwisko==
	- dodaj linijkę "10.0.0.1 ==nazwisko== ==nazwisko==.pracownia10.local" (pod ==nazwiskiem==)
- `nano /etc/hostname`
	-  zmień ==server== na ==nazwisko==
# zmiana daty
- `timedatectl set-timezone Europe/Warsaw`
- `date`

# dhcp
- `apt update` i ewentualnie `apt upgrade`
- `apt install isc-dhcp-server`
- `nano /etc/dhcp/dhcpd.conf`
	- usuwamy "#" przy `option domains-name` i zmieniamy na` pracownia10.local 8.8.8.8;`
	- przy `option domains-name-servers` dodajemy `10.0.0.1;`
	- usuwamy "#" przy `log-facility local7;`
	- pod `subnet 10.0.0.0` zgodnie ze screen-1
	- `dhcpd -t` testowanie konfiguracji

- `systemctl start isc-dhcp-server`
- `systemctl status isc-dhcp-server`

- `cd /etc/default`
- `nano isc-dhcp-server`
	- przy `INTERFACES04 "enp0s8"`
	- restartujemy dhcp `systemctl restart isc-dhcp-server`

# ustawienie domyślnej konfiguracji
- `echo 1 > /proc/sys/net/ipv4/ip_forward` wstawienie jedynki
- `cat /proc/sys/net/ipv4/ip_forward` sprawdzenie czy została wstawiona
- `nano /etc/sysclt/conf`
- usuń # przy `net.ipv4.ip_forward=1`

# odblokowanie zapory
- `iptables -t nat -A POSTROUTING -o enp03 -j MASQUERADE` jednorazowe odblokowanie zapory
- `nano /etc/rc.local`
	 ```
	 #!bin/sh -e
	 iptables -t nat -A POSTROUTING -o enp03 -j MASQUERADE
	 
	exit 0
	 ```
- w katalogu "/" `ls -lah` "lsh = list all hidden"
- `chmod 755 /etc/rc.local`

# Instalacja SAMBA
- `apt update` && `apt upgrade`
- `apt install samba`
- `systemctl status smbd` sprawdzenie czy się pobrało

# Konfiguracja
- `mkdir /home/anonymus`
- `chmod 777 /home/anonymus`
- `ls -lrth /home | tail -1` sprawdzenie grupy do której należy folder
- `chown nobody /home/anonymus`
- `chgrp nogroup /home/anonymus`
- `ls -lrth /home | tail -1` powinno być nobody nogroup zamiast root root
