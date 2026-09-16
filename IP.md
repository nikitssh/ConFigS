# Ver configuraciones de Interfaces

1. Te enseña las IPs y sus mascaras en las interfaces
```
ip r / ip -br addr
```

2. Te neseña la configuracion entera de las interfaces
```
ip a / ip /all
```

3. Para ver DNS y Dominio
```
cat /etc/resolv.conf
```

# Configuracion de Interfaces

### Para poner una interfaz en modo DHCP
```
auto enp0s3
iface enp0s3 inet dhcp
```

### Para poner una interfaz en static
```
auto enp0s3
iface enp0s3 inet static
    address 192.168.1.50/24
    gateway 192.168.1.1
    dns-nameservers 1.1.1.1 8.8.8.8
```

En el caso que tenemos dos interfaces y una ya esta establecida como predeterminada, lo podemos ver por `ip route`, no devemos de poner el gateway y DNS, el sistema lo detectara automaticamente

**Al final de hacer cualquier cambio vamos a recargar interfaces**
```
sudo systemctl restart networking
```

