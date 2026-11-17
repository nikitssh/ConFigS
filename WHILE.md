# Esplicacion detallada de WHILE funcciones

## La estructura

La estructura de WHILE es asi

```bash
while [ CONDICION ]
do
    Instrucciones que se repiten
done
```

## Funcciones

* **`test`**

El **`test`** sirve para comprobar una condicion que en nuestro caso seria que comprueba si el $limite es mayor que **`-gt`** $i, (5 > i). El test es equivalente a [[]].

```bash
#!/bin/bash

limite=5
i=0

while test $limite -gt $i   #La condicion sigue ejecutandose mientras el $limite es mayor a $i   
do
    echo "balore $i"        #Imprime el valor de $i
    let i=$i+1              #Cada vez que llegue a esta condicion, a la $i se le sumara un uno
done
```

El **`let`** permite realizar las operaciones matematicas, en este caso hace que la variable i guarde el resultado de $i+1.

* **`-ne`**

El **`-ne`** significa no igual, es decir, un numero distincto.

```bash
#!/bin/bash

zenbakia=1

while test $zenbakia -ne 0                                  #Condicion termina si el numero introducido es 0.
do
    read -p "Sartu zenbaki bat (0 amaitzeko): " zenbakia    #Si le metemos un numero que no es 0, vuelve al bucle.
    echo "Sartutako zenbakia: $zenbakia"
done
    echo "Programa amaitu da"                               #Si se le ha metido un 0, el programa termina.
```

* **`$#`**

El **`$#`** cuenta cuentos parametros han sido intoducidos.

```bash
#!/bin/bash

if test $# -ne 2    #Si hay mas o menos de 2 parametros, entra en if.
then
    echo "Erabilera: $0 zenbaki1 zenbaki2"
    exit 1
fi

hasiera=$1
amaiera=$2

if ! [[ $hasiera =~ ^-?[0-9]+$ && $amaiera =~ ^-?[0-9]+$ ]]
then
    echo "Errorea: zenbaki osoak sartu behar dituzu"
    exit 1
fi

if test $hasiera -gt $amaiera
then
    laguntzailea=$hasiera
    hasiera=$amaiera
    amaiera=$laguntzailea
fi

i=$hasiera

while test $i -le $amaiera
do
    if test $((i % 2)) -eq 0
    then
        echo "$i zenbaki bikoitia da"
    else
        echo "$i zenbaki bakoitia da"
    fi
        i=$((i + 1))
done
```

* **`if ! [[ $hasiera =~ ^-?[0-9]+$ && $amaiera =~ ^-?[0-9]+$ ]]`**

* **`!`**: Cambia el resultado de positivo a negativo o al reves, es decir "si no es".
* **`=~`**:
* **`^`**:
* **`-?`**:
* **`+`**:
* **`$`**:

* **`-le`**: Esto significa menor o igual, es decir <=.

* **`while :`**:

El while con dos puntos delante es decir while : significa que es un comando que siempre termina correctamente, es decir, con esto nunca saldriamos del bucle creado.

```bash
#!/bin/bash

while :
do
    clear   #Limpiamos las consola

    echo "-------------------------------------"
    echo " MENU NAGUSIA"
    echo "-------------------------------------"
    echo "[1] Gaurko data eta ordua erakutsi"
    echo "[2] Direktorio honetako fitxategiak erakutsi"
    echo "[3] Egutegia erakutsi"
    echo "[4] Irten"
    echo "-------------------------------------"

    read -p "Sartu zure aukera [1-5]: " zureaukera

    case $zureaukera in
        1)
            date
            read -p "Sakatu ENTER jarraitzeko..."
            ;;

        2)
            echo "Uneko direktorioa: $(pwd)"
            ls -l
            read -p "Sakatu ENTER jarraitzeko..."
            ;;

        3)
            cal
            read -p "Sakatu ENTER jarraitzeko..."
            ;;

        4)
            echo "Programa amaitzen..."
            exit 0
            ;;

        *)
            echo "Errorea: 1etik 5era arteko aukera bat hautatu"
            read -p "Sakatu ENTER jarraitzeko..."
            ;;
    esac
done
```

* **`read -p "Texto" var`**

