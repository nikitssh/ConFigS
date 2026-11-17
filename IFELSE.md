# Esplicacion detallada de if else y funcciones

## La estructura

La estructura de IF es asi

```bash
if [[ $variable ]] ; then
    COSA QUE HACER
else
    OTRA COSA QUE HACER
fi
```

Tambien podemos cambiar esta estructura, como no poner las ; despues de la condicion pero en este caso el then tendria que ir abajo luego, tambien podemos poner en vez de doble [[]], poner solo [], pero tendria que ir dentro de unas "" para prevenir los fallos.

```bash
if [ "$variable" ]  #Variable entre "" para que el resultado no de error
then                #El then lo pasamos abajo ya que no esta el ;
    COSA QUE HACER
else
    OTRA COSA QUE HACER
fi
```

*Diferencia entre [[]] y (()).*

*Para las compropaciones o funcciones, se usan [[]] o [], pero para unas operaciones numericas o condiciones numericas usamos (()), en este caso las variables no necesitan llevar el simbolo $, el bash lo intempreta correctamente si ello.*

```bash
#!/bin/bash

echo -n "sartu balore bat: "
read var                                        

if (( var < 10 ))                       #Sin $ porque esta dentro (())
then
    echo $var "10 baino txikiagoa da"   #Con $ porque no esta dentro de (())
else
    echo $var "10 edo 10 baino handiagoa da"    
fi
```

Para continuar añadiendo condiciones a un IF

```bash
if [ $variable ]
then                    
    COSA QUE HACER
elif
    OTRA COSA QUE HACER
else
    OTRA COSA QUE HACER
fi
```

Estructura IF dentro de IF

```bash
if [[ $variable ]]
then
    COSA QUE HACER
    
    if [[$variable]]
    then
        COSA QUE HACER
    else
        OTRA COSA QUE HACER
    fi
else
    OTRA COSA QUE HACER
fi
```

## Funcciones

* **`-e -d`**

La funccion -e comprueba si un archivo indicado existe en tu directorio que le vas a indicar. En cambio la -d te va a encontrar una carpeta.

```bash
#!/bin/bash

if [[ -e readme.txt ]] ; then                       #Nos busca el archivo readme.txt en el directori actual
    echo 'The file "readme.txt" exists.'            #Nos imprime esto si el archivo existe
else
    echo 'The file "readme.txt" does not exist.'    #Si el archivo no existe nos imprimiria esto
fi
```

* **`$?`**

La funccion $? guarda la salida de ultimo comando ejecutado, su salida natural es un 0 cuando un comando se ejecuto correctamente, si no es asi, dara algo distincto al 1.

```bash
#!/bin/bash

echo -n "Zein fitxategi borratu nahi duzu? "
read fitxategia                     #Guarda el nombre de nuesto documento

rm "$fitxategia"                        

if [[ $? -eq 0 ]] ; then            #Si la salida es igual (-eq) a 0, esta correcto
    echo "fitxategia $fitxategia ondo borratu da"
else
    echo "ekintzak errorea eman du" #Si la salida del rm fue distinca a 0, se ejecutara esta respuesta
fi
```

*La funccion **`-n`** se usa para puedas escribir algo despues de echo sin que salte a la siguiente linea, es decir, escribir seguido en vez de abajo*

*La funccion read es la que hace que el script se pare para esperar que metas algun texto para luego meter ese texto en una variable que es este caso es $fitxategia.*

*La funccion **`-eq`** compara los numeros, en el caso que queramos comparar dos textos, usarioamos otra funccion que seria **`==`***

*En este caso el la variable fichategia en el rm la ponemos entre "", es decir "$fitxategia", es para prevenir erores, si nuestro archivo .txt tiene espacios como **`mi archivo.txt`**, en estos casos como pueden tener espacion las variables se ponen entre "".*

Otro ejemplo mas de **`$?`**

```bash
#!/bin/bash

erabiltzailea=$1

grep "^$erabiltzailea:" /etc/passwd > /dev/null #Busca el user:

if [[ $? -eq 0 ]]
then
    echo "Erabiltzailea existitzen da"
else
    echo "Erabiltzailea ez da existitzen"
fi
```

Aqui hacemos lo mismo de antes, compararmos si la salida ha sido correcta, le grep busca el usuario indicado en /etc/passwd que es donde se encuentran los nombres de usuarios, como podemos observar, antes de $erabiltzailea: tenemos un ^, es decir, el grep buscara exactamente el "usuario:".

* **`==`**

La funccion == significa si el contenido x es igual a y, en dos palabras, comparamos dos valores si son iguales.

```bash
#!/bin/bash

echo -n "Sartu kolore bat: "
read kolorea

if [[ $kolorea == "gorria" ]]   #Mira si el color indicado es igual al rojo
then
    echo "Kolorea gorria da"
else
    echo "Kolorea ez da gorria" #Si el color no es rojo
fi
```

Otro ejemplo mas

```bash
#!/bin/bash

a=$1
b=$2

if (( a % b == 0 ))
then
    echo "$a eta $b zatigarriak dira"
else
    echo "$a eta $b ez dira zatigarriak"
fi
```

En este caso, ponemos dos numeros y los dividimos, si la division sale exacta con un numero entero, es zatigarriak.

* **`&&`**

Los simbolos && se usan para que la funccion que este, cumple dos o mas requisitos

```bash
#!/bin/bash

echo -n "Sartu erabiltzailea: "
read erabiltzailea

echo -n "Sartu pasahitza: "
read -s pasahitza                                           
echo

if [[ $erabiltzailea == "admin" && $pasahitza == "1234" ]]  #Las dos condiciones tienen que cumplirse
then
    echo "Sarrera zuzena"
else
    echo "Erabiltzailea edo pasahitza okerra da"
fi
```

*La funccion -s en read hace que el texto que escribamos, no aparezca en linea, es decir, es invisible.*

* **`<>`**

En el caso de que queremos comparar algun numero si es menor o mayor al otro, usamos < o >, por ejemplo el numero que nosotos queramos se lo guardamos en una variable dentro de read.

```bash
#!/bin/bash

echo -n "sartu balore bat: "
read var                                        

if (( var < 10 ))                               
then
    echo $var "10 baino txikiagoa da"           #Si el numero indicado en menor que el 10
else
    echo $var "10 edo 10 baino handiagoa da"    #Si no era menor sacamos esta salida
fi
```

* **`||`**

Este simbolo significa que tiene que cumplir una o la otra condicion, es decir **`condicion o condicion`**.

```bash
#!/bin/bash

echo -n "Sartu zure nota: "
read nota

if (( nota < 4 || nota > 10 ))  #Si el numero es menor a 4 y mayor a 10, que imprime lo siguiente.
then
    echo "Nota ez da zuzena"    
elif (( nota < 5 ))
then
    echo "Gutxiegi"
elif (( nota < 7 ))
then
    echo "Nahikoa"
else
    echo "Bikain"
fi
```

* **`<= >=`**

La funccion **`<=`** o **`>=`**, significa que el Y es igual o mayor/menor que X.

```bash
!/bin/bash

a=$1
b=$2
c=$3

if (( a >= b && a >= c ))
then
    handiena=$a
elif (( b >= a && b >= c ))
then
    handiena=$b
else
    handiena=$c
fi

echo "Handiena: $handiena"
```
