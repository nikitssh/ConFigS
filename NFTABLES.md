# ROUTER Y FIREWALL

## ROUTER

### Comandos

Para que nuestra máquina haga de router solo necesitamos un comando que dejaría cruzar al todo el tráfico a través de redes/interfaces diferentes.

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

Para verificar que está bien usaríamos este comando.

```bash
sysctl net.ipv4.ip_forward
```

O este

```bash
cat /proc/sys/net/ipv4/ip_forward
```

*Si ponemos al final un **`0`**, el tráfico no podría pasar.*

## Firewall

Para que nuestro firewall funcione de manera correcta, tendríamos que poner el comando anterior que sería **`sudo sysctl -w net.ipv4.ip_forward=1`**.

Ahora empezaríamos a redactar nuestro archivo de configuración donde se encuentre **`NFTABLES`**. Y aplicamos por ejemplo toda esta configuración.

```bash
sudo nano /etc/nftables.conf


#!/usr/sbin/nft -f

flush ruleset

table ip nat {
    chain postrouting {
        type nat hook postrouting priority 100;
        policy accept;

        ip saddr 192.168.2.0/24 oifname "enp0s3" masquerade
    }
}

table inet filter {
    chain input {
        type filter hook input priority filter;
    }

    chain forward {
        type filter hook forward priority 0;
        policy drop;

        ct state established,related accept

        ip saddr 192.168.0.0/22 oifname "enp0s3" udp dport 53 accept
        ip saddr 192.168.0.0/22 oifname "enp0s3" tcp dport 53 accept

        ip saddr 192.168.1.0/24 ip daddr 192.168.2.0/24 tcp dport 22 drop
        ip saddr 192.168.1.0/24 ip daddr 192.168.2.0/24 icmp type echo-request drop
    }

    chain output {
        type filter hook output priority filter;
    }
}
```

1. NAT

* **`table ip nat`**: En esta parte ponemos un nombre que nosotros queramos para definir la tabla, también si queremos que funcione también IPv6 tendríamos que poner **`table inet nat`**.
* **`type nat hook postrouting priority 100;`**: Aquí indicamos que tipo de tabla es que sería **`NAT`** y le ponemos una prioridad, es decir en que orden se ejecutarían nuestras tablas, en filter normalmente se pone 0.
* **`policy accept;`**: Aquí ponemos que todo lo que sale a internet se acepte por defecto.
* **`ip saddr 192.168.2.0/24 oifname "enp0s3" masquerade`**: Aquí indicamos que red es la que puede salir a internet y de que interfaz lo hará, luego la transforma con **`masquerade`** a la IP pública del router.

2. Filter
* **`ct state established,related accept`**: Esta regla acepta el tráfico como por ejemplo de una página web y las conexiones relacionadas con esa conexión que han sido establecidas.
* **`ip saddr 192.168.0.0/22 oifname "enp0s3" udp dport 53 accept`**: Este tipo de regla permite el tráfico de protocolo udp (DNS) a través de la interfaz que sale a la red.
* **`ip saddr 192.168.1.0/24 ip daddr 192.168.2.0/24 tcp dport 22 drop`**: Y esta regla deniega que las máquinas de la red 192.168.1.0 puedan acceder por puerto 22 (SSH) a la red 192.168.2.0.

## Comprobación

* Para comprobar la sintaxis usaremos **`sudo nft -c -f /etc/nftables.conf`**.
* Para aplicar las reglas usaremos **`sudo nft -f /etc/nftables.conf`**.
* Para ver que reglas están activa en este momento **`sudo nft list ruleset`**.

Para ver si una página web está accesible usaríamos

```bash
curl -I https://example.com
```

Para comprobar si un puerto está abierto en alguna máquina usaríamos comando **`nc -vz "IP de la máquina" "PUERTO"`**.

Ejemplo:

```bash
nc -vz 192.168.50.10 80
```

*Comprueba si el puerto **`80(HTTP)`** está abierto en la máquina **`192.168.50.10`**.*

Otra comprobación sería por ejemplo consultar la hora con el servicio **NTP** con este comando, si quitamos la -q, sincronizaría la hora en vez de consultarla.

```bash
ntpdate -q 0.pool.ntp.org
```

Para ver el Log de paquetes descartados o aceptados ponemos estas reglas.

```bash
sudo nft insert rule inet filter forward log prefix "FW-DROP: " counter drop #paquetes denegados
sudo nft insert rule inet filter forward log prefix "FW-ACCEPT: " counter accept #paquetes aceptados
```

*Para cambiar esa regla de drop o accept a accept o drop primero tendríamos que ejecutar este comando y ver el número que nos salga y eliminarla.*

```bash
sudo nft list chain inet filter forward -a
sudo nft delete rule inet filter forward handle 12 #El 12 es el número de la regla
```

Y ahora podremos ver las reglas bloqueadas o aceptadas en tiempo real con

```bash
sudo journalctl -f
```
