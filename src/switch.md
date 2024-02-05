# Configurazione dello switch

Lo switch di base è un oggetto che nel caso più semplice può anche non essere configurato, in quanto mette in comunicazione tutte le porte fra di loro. Se vogliamo creare delle VLAN, però, ci serve una configurazione.

Per configurarlo abbiamo due possibilità:

- ci clicchiamo sopra e andiamo su CLI (comodo ma impossibile nella realtà)
- lo colleghiamo con un cavo console ad un PC o laptop, quindi andiamo sul computer ed apriamo un "Terminal" seriale (non un command prompt!)

Una volta dentro, per prima cosa creiamo le VLAN.

Ipotizziamo la seguente tabella

|VLAN| Descrizione|
|-----| -----|
|VLAN1|Amministrativa|
|VLAN10|Docenti|
|VLAN20|Studenti|

La VLAN1 è già creata di default, quindi non dobbiamo fare niente. Andiamo a creare le altre due.

Entriamo in modalità configurazione:

```bash
Switch> enable
Switch# configure terminal
```

quindi:

```bash
Switch(config)#vlan 10
Switch(config-vlan)#exit
Switch(config)#vlan 20
Switch(config-vlan)#exit
Switch(config)#
```

Assegniamo ora le VLAN alle porte. Nel nostro caso, vogliamo la seguente situazione:

|Porta| Descrizione|
|-----| -----|
|FastEthernet 0/1|Studenti|
|FastEthernet 0/2|Studenti|
|FastEthernet 0/3|Docenti|
|GigabitEthernet 0/1|Router|

```bash
Switch(config)#interface FastEthernet 0/1
Switch(config-if)#switchport access vlan 20
Switch(config-if)#exit
Switch(config)#interface FastEthernet 0/2
Switch(config-if)#switchport access vlan 20
Switch(config-if)#exit
Switch(config)#interface FastEthernet 0/3
Switch(config-if)#switchport access vlan 10
```

Ora dobbiamo configurare la porta che va verso il server DHCP. Da questa porta deve poter passare il traffico di diverse VLAN, si dice quindi in questo caso che è una _porta di trunk_.

```bash
Switch(config)#interface GigabitEthernet 0/1
Switch(config-if)#switchport mode trunk
Switch(config-if)#exit
Switch(config)#
```

## Configurare il server DHCP

```bash
Switch(config)#ip dhcp excluded-address 10.0.0.1 10.0.0.10
Switch(config)#ip dhcp excluded-address 10.1.0.1 10.1.0.10
Switch(config)#ip dhcp pool vPool10
Switch(dhcp-config)#network 10.0.0.0 255.255.255.0
Switch(dhcp-config)#default-router 10.0.0.1
Switch(dhcp-config)#dns-server 4.4.4.4
Switch(dhcp-config)#exit
Switch(config)#ip dhcp pool vPool20
Switch(dhcp-config)#network 10.1.0.0 255.255.0.0
Switch(dhcp-config)# default-router 10.1.0.1
Switch(dhcp-config)# dns-server 4.4.4.4
Switch(dhcp-config)#exit
Switch(config)#
```

## Assegnazione indirizzi IP

```bash
interface vlan 10
ip address 10.0.0.5 255.255.255.0
interface vlan 20
ip address 10.1.0.5 255.255.0.0
```

## Configurazione router

```bash
Router>enable
Router#configure terminal
Router(config)#interface GigabitEthernet 6/0
Router(config-if)#no ip address
Router(config-if)#no shutdown
Router(config-if)#exit
Router(config)#interface GigabitEthernet 6/0.10
Router(config-subif)#encapsulation dot1Q 10
Router(config-subif)#ip address 10.0.0.1 255.255.255.0
Router(config-subif)#exit
Router(config)#interface GigabitEthernet 6/0.20
Router(config-subif)#encapsulation dot1Q 20
Router(config-subif)#ip address 20.0.0.1 255.255.0.0
Router(config-subif)#exit
Router(config)#
```

[Credits](https://www.computernetworkingnotes.com/ccna-study-guide/configure-dhcp-server-for-multiple-vlans-on-the-switch.html).
