# CAMBIO DE LINUX A WINDOWS INSTANTANEAMENTE

## Introduccion

Si tienes problemas con la instalación de GRUB y Windows Boot EFI juntos en la misma partición por falta de memoria en la **NVRAM**, y has terminado instalándolos por separado en dos particiones diferentes, añadiendo posteriormente la entrada de Windows a GRUB, y luego decidiste crear un script automático para que te lance desde Linux directamente a Windows sin necesidad de apagar o reiniciar el equipo, pero te has topado con el problema de que no tienes espacio en la NVRAM para hacerlo de la forma tradicional, esta documentación es para ti.

### 1. Crear script ejecutable para arrancar Windows automáticamente

Primero creamos el script en una ubicación. La recomendable sería *`/usr/local/bin`*, y añadimos la configuración

```bash
nano /usr/local/bin/windows

#!/bin/bash

sudo grub-editenv /boot/grub/grubenv set windows_once=1

sudo grub-reboot 'osprober-efi-4C2D-70BB'

sudo reboot
```

- Este es el archivo de entorno de GRUB *`/boot/grub/grubenv`*, donde guardaremos nuestra variable personalizada **`windows_once=1`**. Esta variable será la encargada de indicarle a GRUB que debe realizar el arranque especial de Windows una sola vez.

- **`grub-reboot`** es una herramienta de GRUB que nos permite indicar qué entrada queremos arrancar en el siguiente reinicio. En este caso, indicamos **`osprober-efi-4C2D-70BB`**, que corresponde a nuestra entrada de Windows. La última línea, **`sudo reboot`**, se encarga de reiniciar el equipo.

*Para localizar vuestro propio identificador **`osprober-efi-4C2D-70BB`**, utilizaremos la herramienta **`os-prober`**, que se encarga de detectar otros sistemas operativos instalados y permite a GRUB generar sus correspondientes entradas.*

Podemos localizar el identificador que utiliza GRUB mediante

```bash
grep "osprober" /boot/grub/grub.cfg
```

En mi caso, obtenemos

`menuentry 'Windows Boot Manager (en /dev/nvme0n1p1)' --class windows --class os $menuentry_id_option 'osprober-efi-4C2D-70BB' {`

### 2. Crear script en el directorio *`/grub.d`*

En este directorio se construyen las configuraciones para GRUB. Para saber dónde se encuentra, podemos utilizar:

```bash
find / -name "grub.d" 2>/dev/null
```

En mi caso, se encuentra en *`/etc/grub.d`*.

Dentro podemos observar diferentes scripts, como **`00_header`**, que es el encargado de generar la parte inicial y global de la configuración de GRUB.

También podremos observar que todos los scripts tienen un número determinado al principio del nombre. Estos números indican el orden en el que se van ejecutando los scripts.

Por ejemplo:

```bash
ls /etc/grub.d

00_header
10_linux
15_uki
```

Nuestro script tiene que ejecutarse después de **`00_header`** y antes de los scripts que generan las entradas de los sistemas operativos. Por este motivo, podemos utilizar un número entre **`01 y 09`**. En nuestro caso, escogeremos el número **`06`**.

El nombre **`06_windows_once`** está compuesto por el número que determina el orden de ejecución y el nombre que nosotros hemos elegido para identificar el script. El guion bajo **`_`** no es obligatorio; forma parte simplemente del nombre que hemos decidido darle.

Crearemos el archivo y añadimos la siguiente configuración

```bash
nano /etc/grub.d/06_windows_once

#!/bin/sh

cat <<'EOF'

if [ "${windows_once}" = "1" ]; then

    set timeout=0

    set timeout_style=hidden

    set windows_once=0

    save_env windows_once

fi

EOF
```

Este script indica a GRUB que, cuando se genere la configuración, compruebe si nuestra variable **`windows_once`** tiene el valor **`1`**.

Si la variable tiene ese valor:

- **`set timeout=0`** → establece el tiempo de espera de GRUB en 0 segundos.
- **`set timeout_style=hidden`** → oculta el menú de GRUB durante ese arranque.
- **`set windows_once=0`** → cambia nuestra variable nuevamente a `0`, para que esta configuración no se aplique en el siguiente arranque.
- **`save_env windows_once`** → guarda el nuevo valor `0` en **`/boot/grub/grubenv`**.

De esta forma, el arranque especial hacia Windows solo se aplica una vez. Después de arrancar Windows y volver a iniciar normalmente, GRUB recuperará su comportamiento habitual, incluyendo el menú y sus 5 segundos de espera.

El paso final sería importar nuestra configuración mediante este comando **`sudo grub-mkconfig -o /boot/grub/grub.cfg`**

Ahora reiniciamos nuestro equipo para que los cambios se apliquen correctamente.

### 3. Uso diario

A partir de ahora, para pasar a Windows estando en Linux, simplemente escribiremos en el terminal el nombre del archivo que hemos creado en el paso 1:

**`windows`**

Un detalle importante, para poder ejecutar nuestro script, primero tendremos que darle permisos de ejecución mediante el comando **`sudo chmod +x /usr/local/bin/windows`**.


