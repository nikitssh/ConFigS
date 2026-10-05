# Configuración de DNS Server Autoritativo

## Instalación

Vamos a instalar un DNS server que se llama **`Bind9`**.

```bash
sudo apt install bind9 bind9utils bind9-doc
```

## Configuración

### Tendremos que hacer varias configuraciones

### 1. Configuración básica

Primero vamos a configurar el archivo **`named.conf.options`**.

```bash
sudo nano /etc/bind/named.conf.options


options {
    directory "/var/cache/bind";
    recursion yes;
    allow-query { 192.168.1.0/24; };
    dnssec-validation auto;
    auth-nxdomain no;
    listen-on { 192.168.1.50; 127.0.0.1; };
    listen-on-v6 { none; };
};
```

* **`recursion`**: Esto permite al servidor buscar y preguntar a otros servidores DNS el nombre solicitado por el cliente.
* **`allow-query`**: Indicamos quién puede hacer consultas a este server, en este caso todos los de la red **192.168.1.0/24**.
* **`dnssec-validation`**: Esto sirve para la validación de DNS para saber si no fue manipulado.
* **`auth-nxdomain`**: Controla cómo BIND trata las respuestas NXDOMAIN. **`NXDOMAIN`** significa que ese nombre de dominio no existe.
* **`listen-on listen-on-v6`**: Indica en qué IP debe escuchar, que sería el server.

## 2. Crear la zona

Vamos a crear nuestra zona, para ello editamos el archivo **`/etc/bind/named.conf.local`**.

```bash
sudo nano /etc/bind/named.conf.local


zone "zona.com" {
    type master;
    file "/etc/bind/db.zona.com";
};
```

## 3. Crear configuración de la zona

Para ello vamos a editar el archivo que hemos indicado en la configuración anterior, que sería **`db.zona.com`**.

```bash
sudo nano /etc/bind/db.zona.com


$TTL 86400

@ IN SOA dns.zona.com. administrador.zona.com. (
    2026092101 ; Serial
    3600       ; Refresh
    1800       ; Retry
    604800     ; Expire
    86400      ; Minimum TTL
)

@ IN NS dns.zona.com.

dns IN A 192.168.1.50

debian1 IN A 192.168.1.51
debian2 IN A 192.168.1.52
debian3 IN A 192.168.1.53

www IN CNAME debian1.zona.com.
files IN CNAME debian2.zona.com.
mail IN CNAME debian3.zona.com.
```

* **`SOA`**: Es el que indica de qué zona es responsable y los parámetros para ella.
* **`@`**: Este símbolo representa la propia zona, que sería **`zona.com`**.
* **`.`**; El punto significa que el nombre de nuestro DNS está completo.
* **`NS`**: Esto significa el **Name Server**, esto significa de qué servidor es responsable de esta zona, que en nuestro caso es **`dns.zona.com.`**.
* **`A`**: Significa simplemente el nombre -> IPv4.
* **`CNAME`**: Quiere decir que estás creando un alias para **`debian2`**, en este caso debian2 ahora podrá responder al nombre www.zona.com.
* **`dns.zona.com. administrador.zona.com.`**: Aquí indicamos que el **`dns.zona.com.`** es el DNS autoritativo, el **`administrador.zona.com.`** es el responsable de responder correos.
* **`2026092101 ; Serial`**: Esto sería la versión de nuestra zona, si hacemos algún cambio en la configuración de la zona, debemos cambiar ese número para que BIND registre el cambio.
* **`3600 ; Refresh`**: Indica cada cuánto tiempo un servidor secundario debería comprobar si la zona ha cambiado.
* **`1800 ; Retry`**: Si el servidor secundario intenta contactar con el servidor principal y falla, indica cuánto tiempo debe esperar para hacer otra vez el intento.
* **`604800 ; Expire`**: Es el tiempo máximo que un servidor secundario puede seguir usando la información que tiene si no consigue contactar con el servidor principal.
* **`86400 ; Minimum TTL`**: En configuraciones modernas de DNS, este campo del SOA se utiliza principalmente como TTL negativo, es decir, cuánto tiempo se puede cachear una respuesta de “ese nombre no existe”.

### Comprobación

Para activar nuestro servicio DNS **`sudo systemctl status bind9`** o **`sudo systemctl enable --now bind9`**.

Cuando haremos algún cambio habrá que reiniciar el servicio **`sudo systemctl restart bind9`** y verificamos si está en marcha luego con **`sudo systemctl status bind9`**.

Para verificar la sintaxis de nuestro archivo *db.zona.com* **`sudo named-checkconf`**.

También esto **`sudo named-checkzone niredomeinua.com /etc/bind/db.niredomeinua.com`**.

Para saber qué IP responde a un nombre, podemos usar **`dig`** para ello, **`dig @192.168.1.50 debian1.zona.com`**.

También podemos usar el **`nslookup`**, podemos especificar la IP de server **`nslookup debian1.zona.com 192.168.1.50`** o poner solo qué es lo que queremos encontrar *nslookup **`debian1.niredomeinua.com`**.

### Detalles importantes

Para que todas las comprobaciones funcionen correctamente, en los clientes tenemos que editar el archivo **`resolv.conf`**.

```bash
sudo nano /etc/resolv.conf


nameserver 192.168.1.50
search zona.com
```

Otra opción más interesante sería configurar el DHCP server para que reparta la IP del dominio y el DNS. Para ello accedemos a **`/etc/dhcp/dhcpd.conf`** y ponemos las siguientes líneas.

```bash
sudo nano /etc/dhcp/dhcpd.conf


default-lease-time 1200;
max-lease-time 259200;
authoritative;
subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.51 192.168.1.100;
    option subnet-mask 255.255.255.0;
    option routers 192.168.1.50;
    option broadcast-address 192.168.1.255;
    option domain-name-servers 192.168.1.50;
    option domain-name "zona.com";
}
```

Estas son las opciones que nos interesan para que nuestros ordenadores obtengan DNS de nuestra zona: **`domain-name-servers y domain-name`**.