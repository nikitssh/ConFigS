# Ver y configuracion de interfaces

## 1. Consultar informacion de interfaces

### 1.1 Ver las IP y sus máscaras en las interfaces

Muestra las direcciones IP asignadas a las interfaces de forma resumida:

```bash
ip -br addr
```

También puedes utilizar:

```bash
ip addr
```

### 1.2 Ver la configuración completa de las interfaces

Para ver toda la información de las interfaces:

```bash
ip addr
```

También puedes utilizar:

```bash
ip link
```

### 1.3 Ver los servidores DNS y el dominio

```bash
cat /etc/resolv.conf
```

## 2. Configuración de interfaces

El archivo que tendríamos que editar para configurar las interfaces sería **`/etc/network/interfaces`**.

### 2.1 Para poner una interfaz en modo DHCP

```bash
sudo nano /etc/network/interfaces


auto enp0s3

iface enp0s3 inet dhcp
```

### 2.2 Para configurar una interfaz con IP estática

```bash
sudo nano /etc/network/interfaces


auto enp0s3

iface enp0s3 inet static
    address 192.168.1.50/24
    gateway 192.168.1.1
    dns-nameservers 1.1.1.1 8.8.8.8
```

En el caso de tener dos interfaces y que una de ellas ya esté establecida como interfaz predeterminada, podemos comprobarlo mediante:

```bash
ip route
```

Si la otra interfaz se utiliza para una red que no necesita salir a Internet, normalmente **no debemos configurar otro `gateway`** en ella. De esta forma evitamos tener varias rutas por defecto.

El DNS tampoco tiene que configurarse necesariamente en cada interfaz. Los servidores DNS se configuran a nivel del sistema y dependen de cómo esté gestionada la red.

### Después de realizar cambios en la configuración

Podemos reiniciar el servicio de red para aplicar los cambios:

```bash
sudo systemctl restart networking
```
