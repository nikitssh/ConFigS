# Esplicacion detallada de CASE y funcciones

## La estructura

La estructura de un CASE es asi

```bash
case $VARIABLE in
    OPCION1)
        COSA QUE HACER
        ;;
    OPCION2)
        COSA QUE HACER
        ;;
    *)
        COSA POR DEFECTO
        ;;
esac
```

## Funcciones

* **`x)`**

Aqui lo que hace es guarda el numero que nosotros queramos en la variable var1 y luego compara si es un 1 o 2, si no es ninguno, saldra la salida *.

```bash
#!/bin/bash

echo -n "Sartu balore bat: "
read var1

case $var1 in
    1) echo "bat" ;;
    2) echo "bi" ;;
    *) echo "Aukera desegokia" ;;
esac
```

* **`|`**

En case, las | sirven pa definir dos cosas posibles en una, como si es un 1 o 2, imprime esto.

```bash
#!/bin/bash

echo -n "Zein sistema eragile erabiltzen duzu? "
read sistema

case $sistema in
Windows|windows)    #Si el usuario ha esciito Winodows o windows, imprime esto.
    echo "Windows gustatzen zait."
    ;;
Linux|linux)
    echo "Linux gustatzen zait."
    ;;
*)
    echo "Ez dut sistema eragile hori ezagutzen."
    ;;
esac
```

* **`[-]`**

En CASE, los [x-y] sirven para decir, si el valor se encuentra dentro de rango x o y, imprime esto, es decir esto [0-3] seria 0123.  

```bash
#!/bin/bash

# Nota eskatu
echo -n "Sartu nota: "
read nota

case $nota in
[0-4]*) echo "Ez gainditua" ;;
5|6) echo "Nahikoa" ;;
7|8) echo "Oso ongi" ;;
*) echo "Nota desegokia" ;;
esac
```

En este caso, tenemos la funccion [0-4] con un * al final, **`[0-4]*`**, esto quiere decir, que imprima esa respuesta, si lo que hemos metido en el texto, empieza con un numero que esta entre 01234, es decir, puede imprimir 38, o puede imprimir un 2bai, con un texto.

* **`$()`**

En este caso usaremos CASE mas una operacion aritmetica.

```bash
#!/bin/bash

eragiketa=$1
zenbaki1=$2
zenbaki2=$3

case $eragiketa in
+)
    echo $((zenbaki1 + zenbaki2))
    ;;
-)
    echo $((zenbaki1 - zenbaki2))
    ;;
*)
    echo "Eragiketa desegokia"
    exit 1
    ;;
esac
```

*Aqui lo que hacemos es meter unos paramentros y imprimimos en la pantalla el resultado, en este caso, necesitamos ponerlo en esta forma como **`$((zenbaki1 + zenbaki2))`**, porque un echo no hace operaciones aritmeticas, en cambio en un IF si lo podriamos poner como **`if ((zenbakia1 + zenbakia2))`**.*

*La ultima fila de **`exit 1`** significa que si se ejecuta la condicion **`*)`**, que guarde el codigo 1 en la variable **`0?`** que es donde se guardan las salidas de comandos.*

* **`%`**

```bash
#!/bin/bash

echo -n "Sartu jaiotza urtea: "
read urtea

hondarra=$((urtea % 12))

case $hondarra in
0) echo "Tximinoa" ;;
1) echo "Oilarra" ;;
2) echo "Txakurra" ;;
3) echo "Txerria" ;;
esac
```

Aqui lo que hace es, divide el numero entre 12 y te da lo que sobra.