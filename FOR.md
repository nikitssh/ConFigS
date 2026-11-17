# Esplicacion detallada de FOR funcciones

## La estructura

La estructura de FOR es asi

```bash
for (( inicio ; condición ; incremento ))
do
done
```

Despues de do va la condicion que se va a repetir todo el rato

## Funcciones

```bash
#!/bin/bash

for (( i = 0 ; i <= 5 ; i++ ))
do
    echo "$i"
done
```

Lo que hace aqui es, el valor principal es un 0, y mientras el 0 es menor a 5, va a imprimir el numero, cuando llegue a 5, se va a parrar el bucle, el i++ hace que se sume 1 siempre.

* **`"$@"`**

El simbolo "$@" representa todos los parametros que se habian metido. Tambien podemos usar el $*, pero es menos seguro por si el parametro esta compuestro por dos palabras.

```bash
#!/bin/bash

for i in "$@"
do
    echo "$i"
done
```

* **`*.h`**

La funccion *.h buscara todos los archivos que terminen con .h y lueg el head "$i" nos va a enseñar las primeras lineas del documento, por defecto si no ponemos nada son 10, si queremos mas lineas seria head -n 20 "$i".

```bash
#!/bin/bash

for i in *.h
do
    echo "======== $i fitxategia ========="
    head "$i"
done
```

* **`a b c d e`**

Para imprimir una serie de caracters o palabras, podemos escribirlas directamente despues de in sin comillas, si queremos una palabra compuesta, entonces si deveriamos usar comillas "".

```bash
#!/bin/bash

# Lehenengo FOR: letrak banan-banan erakusten ditugu
echo "Lehenengo begizta:"
for i in a b d e f
do
    echo "$i"
done
```

* **`tail`**

Saca las ultimas lineas.

```bash
#!/bin/bash

for i in "$@"           #Recore todos los parametros metidos
do
    if test -f "$i"     #Comprobamos si el archivo existe.
    then
        echo "=============================="
        echo "$i fitxategiaren azken 10 lerroak"
        echo "=============================="
        tail -n 10 "$i" #Saca las ultimas 10 lineas de archivo
    else
        echo "Errorea: $i fitxategia ez da existitzen"
    fi
done
```

* **Bucle**

```bash
#!/bin/bash

zenbakia=$1

for (( i=1; i<=zenbakia; i++ ))
do
    # Zenbakia bikoitia den egiaztatzen dugu
    if test $((i % 2)) -eq 0
    then
        echo "$i zenbaki bikoitia da"
    else
        # Bestela, bakoitia dela esaten dugu
        echo "$i zenbaki bakoitia da"
    fi
done
```

En este ejemplo lo que ocure es, cuando llegue el i al numero que le hemos indicado, va a parar la conficion.

* **`for do for do`**


```bash
#!/bin/bash

for (( i=1; i<=9; i++ ))
do
    for (( j=1; j<=9; j++ ))
    do
        emaitza=$((i * j))
        echo -n "$emaitza "
    done
    echo
done
```

Aqui lo que hacemos es, primero recoremos un numero, y luego entramos con el al segundo bucle para recorer el siguiente numero y miltiplicarlos.
