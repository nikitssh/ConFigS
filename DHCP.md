# Configuración de DHCP Server

## Instalación

Lo primero que haremos será instalar un servidor DHCP. Para ello utilizaremos **`ISC DHCP Server`**:

```bash
sudo apt install isc-dhcp-server
```

## Configuración

### 1. Indicar la interfaz donde funcionará el servidor DHCP

Primero vamos a indicar la interfaz de red en la que funcionará nuestro servicio DHCP. Para ello, editamos el archivo **`/etc/default/isc-dhcp-server`**.

```bash
sudo nano /etc/default/isc-dhcp-server


INTERFACESv4="enp0s3"
```

*Sustituiremos **`enp0s3`** por el nombre de la interfaz que queramos utilizar.*

### 2. Configurar el rango de IP y el resto de opciones

El siguiente paso será indicar el rango de IP, la máscara, la puerta de enlace, los servidores DNS y otra información del servicio DHCP.

Para ello, editamos y añadir la siguiente configuración:

```bash
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

* `default-lease-time 1200`: establece el **tiempo de concesión por defecto** de una dirección IP, expresado en segundos. En este caso, 1200 segundos (20 minutos).
* `max-lease-time 259200`: establece el **tiempo máximo de concesión** de una dirección IP, expresado en segundos. En este caso, 259200 segundos (3 días).
* `authoritative`: indica que este servidor es el servidor DHCP autorizado para esta red.
* `subnet`: indica la red en la que proporcionaremos direcciones IP.
* `range`: indica el rango de direcciones IP que el servidor puede asignar automáticamente.
* `option subnet-mask`: indica la máscara de red que recibirán los clientes.
* `option routers`: indica la puerta de enlace predeterminada que recibirán los clientes.
* `option domain-name-servers`: indica los servidores DNS que utilizarán los clientes.
* `option broadcast-address`: indica la dirección de broadcast de la red.

### 3. Asignar una IP fija a un cliente

Si queremos asignar una dirección IP específica a una máquina concreta, necesitaremos conocer su dirección MAC.

Volvemos a editar y añadimos:

```bash
sudo nano /etc/dhcp/dhcpd.conf


host cliente1 {

    hardware ethernet 08:00:27:12:34:56;

    fixed-address 192.168.1.20;
}
```

En este caso:

* `host cliente1`: establece el nombre que utilizaremos para identificar al cliente.
* `hardware ethernet`: indica la dirección MAC del cliente.
* `fixed-address`: indica la dirección IP que queremos asignar siempre a ese cliente.

*La IP `192.168.1.20` debe pertenecer a la red configurada, pero normalmente se utiliza fuera del rango dinámico (`192.168.1.50 - 192.168.1.100`) para evitar conflictos.*

### 4. Iniciar y comprobar el servicio

Para iniciar el servicio:

```bash
sudo systemctl start isc-dhcp-server
```

Si el servicio ya estaba en ejecución y hemos realizado cambios en la configuración, podemos reiniciarlo:

```bash
sudo systemctl restart isc-dhcp-server
```

Para comprobar su estado:

```bash
sudo systemctl status isc-dhcp-server
```

## Comprobación

### Server

#### 1. Ver los eventos del servidor DHCP

Para consultar los eventos registrados por el servicio DHCP:

```bash
sudo journalctl -u isc-dhcp-server
```

Si queremos ver los eventos **en tiempo real**, utilizaremos `-f`:

```bash
sudo journalctl -u isc-dhcp-server -f
```

#### 2. Comprobar que la configuración es correcta

Podemos comprobar la sintaxis de la configuración antes de reiniciar el servicio:

```bash
sudo dhcpd -t -cf /etc/dhcp/dhcpd.conf
```

Si no aparece ningún error, la configuración tiene una sintaxis válida.

#### 3. Consultar las concesiones actuales

Para consultar las concesiones DHCP registradas:

```bash
sudo cat /var/lib/dhcp/dhcpd.leases
```

Este archivo contiene información sobre las direcciones IP que el servidor ha concedido a los clientes.

### Cliente

#### 1. Instalar el cliente DHCP

Para utilizar `dhclient`, podemos instalar el paquete `isc-dhcp-client`:

```bash
sudo apt install isc-dhcp-client
```

#### 2. Liberar y solicitar una dirección IP

Para liberar la concesión DHCP actual:

```bash
sudo dhclient -r
```

Y para solicitar una nueva dirección IP al servidor DHCP:

```bash
sudo dhclient -v
```

La opción `-v` muestra información detallada durante el proceso de solicitud DHCP.
