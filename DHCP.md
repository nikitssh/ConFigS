# Configuracion de DHCP Server

## Instalacion

Lo primero que haremos es instalar un server DHCP, para ello usaremos ISC-DHCP-SERVER

```
sudo apt install isc-dhcp-server
```

## Configuracion

1. Primero vamos a asignar una interfaz donce va a funccioar nuesto DHCP servicio, para ello entramos en el ```/etc/default/isc-dhcp-server```

```
sudo nano /etc/default/isc-dhcp-server

INTERFACESv4="'interfaz'"
```

2. Siguiente paso sera indicar el rango de ip-s y mas informacion en este archivo sudo ```/etc/dhcp/dhcpd.conf```

```
sudo nano /etc/dhcp/dhcpd.conf

default-lease-time 1200;
max-lease-time 259200;

authoritative;

subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.50 192.168.1.100;
    option subnet-mask 255.255.255.0;
    option routers 192.168.1.10;
    option domain-name-servers 8.8.8.8;
    option broadcast-address 192.168.1.255;
}
```

- Este en este trozo indicamos cada cuento tiempo comparte la ip ```default-lease-time 1200```, que se cuenta en segundos y la otra line es el maximo que puede no cambiarse la ip ```max-lease-time 259200```.

3. Para dar una ip especifica a una maquina especifica vamos a entrar otravez a nuestro archivo config y ya sabiendo que MAC tiene nuestra maquina especifica, vamos a poner esto en el documente

```
sudo nano /etc/dhcp/dhcpd.conf

host cliente1 {
    hardware ethernet 08:00:27:12:34:56;
    fixed-address 192.168.1.20;
}
```

Luego pondriamos en marcha el servicio o si ya lo teniamos en marcha lo recargaremos y luego comprobamos que esta acvio

```
sudo systemctl start isc-dhcp-server
sudo systemctl restart isc-dhcp-server
sudo systemctl status isc-dhcp-server

```

## Comprobacion

### Server

1. Para ver eventos que estan pasando en DHCP server, ejecutariamos este comando especifico, tambien tenemos otra opcion para ver eventos en tiempo real que seria añadir una ```-f``` al final

```
sudo journalctl -u isc-dhcp-server
sudo journalctl -u isc-dhcp-server -f
```

2. Para ver ni nuestara configuracion esta bien

```
sudo dhcpd -t -cf /etc/dhcp/dhcpd.conf
```

3. Para ver estado actual

```
sudo cat /var/lib/dhcp/dhcpd.leases
```

### Cliente 

1. Para verificar la configuacion de DHCP para el cliente tendriamos que instalar para el ```isc-dhcp-client```

```
sudo apt install isc-dhcp-client
```

2. Para pedir nueva configuracion a DHCP server usaremos estos dos comandos

Esto se usara para liberar la IP que tenia el cliente de antes ```dhclient -r``` y esto para solicitarl de nuevo ```dhclient -v```

```
sudo dhclient -r
sudo dhclient -v
```