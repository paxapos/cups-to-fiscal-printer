# Drivers CUPS para impresoras fiscales Hasar
 
Por el momento sólo funciona con impresoras Hasar, pero si me ayudan un poco podremos lograr que ande en todas las marcas y modelos!


# ¿Como imprimir desde mi programa o desde consola?
Simple: 
```sh
lp -d fiscal_printer_name archivo_de_comandos_fiscales.txt
```
Donde:
* **lp** es el comando de impresión (también se puede usar lpr), pero me gusta mas lp porque podemos hacer algo como:
```sh
lp -h 10.20.100.102 -d fiscal_printer_name archivo_de_comandos_fiscales.txt
```
Y nos enviará a imprimir al HOST 10.20.100.102 siempre y cuando tenga el puerto 512 y 631 abierto si es que estoy detras de un router o firewall.

* **-d fiscal_printer_name** es el nombre de la impresora configurada en CUPS
* **archivo_de_comandos_fiscales.txt** es el archivo con el listado de comandos fiscales generado por tu programa.
Por ejemplo con el contenido: "9X" (para imprimir un comprobante X en una Hasar)


# Aspectos técnicos del driver

El driver funciona como un backend de CUPS y es necesario leerse la documentación para saber que es un backend, un PPD, un filter, y como funcionan y se crean drivers en CUPS:  
* https://community.kde.org/Printing/Developer_Tools#.272dir.27_CUPS_backend_script_.28Bash_code.29).
* https://en.opensuse.org/SDB:Using_Your_Own_Backends_to_Print_with_CUPS

Explicando brevemente como funciona, hace lo siguiente:
CUPS, recibe un "archivo" y lo combierte en JOB. Este Job pasa por distintos filtros hasta, finalmente ejecutar el backend que es el encargado de la comunicación final con la impresora.
Lo que yo hice fue hacer que, el backend llame al ejecutable "spooler" que es un ejecutable que nos da la empresa Hasar para enviar a imprimir los archivos de comandos enviados. Y ahi esta todo. Es muy simple, por asi decirlo.
Es importante que CUPS no procese filtros, y que el archivo llege tal como fue enviado al backend. Esto quiere decir que, al configurar la impresora fiscal en CUPS es necesario indicarle que la marca y modelo (selecciona el PPD y determina el filtro a utilizar) sea el formato "RAW".

# Instalación

1. Deberás copiar el ejecutable (el archivo "spooler" de Hasar) correcto según la arquitectura que estes usando dentro de /usr/bin
2. Deberás copiar el archivo "backend/fiscal" dentro de /usr/lib/cups/backend
3. Deberás copiar las udev rules para que la impresoras conectadas con USB (incluso el conector manhatan) sea leidas automaticamente (copiar lo que esta dentro de la carpeta udev en /etc/udev/rules.d)
4. Reiniciar CUPS: sudo service cups restart
5. Desde CUPS, crear la nueva impresora seleccionando el URI "fiscal:/" que es lo que hará activar el backend indicado y poder realizar la impresión (Por ejemplo: "fiscal:/dev/ttyUSB0", "fiscal:/dev/ttyS0") e introduciendo el path al dispositivo serial donde esta conectada la impresora.
6. Seleccionar el PPD como "RAW" para evitar que CUPS modifique el archivo de comandos enviado. Tiene que llegar al "backend" sin ningún tipo de modificaciones porque la impresorá no funcionará y hasta puede llegar a quedar bloqueada
7. Hacer que el dispositivo serial tenga permiso de escritora para todos. Esto es algo que debo modificar urgentemente. Se supone que colocando al usuario lp dentro del grupo dialout se solucionaria, pero en mi caso no funciono y para lograr imprimir a el dispositivo ttyUSB0 tuve que colocarle permiso de escritura para todos (chmod 666)


# ¿Que viene después?
* primero espero que varios colaboradores se animen a ayudar y mejorar el proyecto.
* Hacer que se pueda enviar un JSON a CUPS. y este convierta automáticamente al formato de la marca y modelo de la impresora detectada y configurada. (ver proyecto fiscalberry en mi página web) personal)[http://alevilar.com/blog/fiscalberry-el-proyecto-para-programadores-de-impresoras-fiscales-argentina]
De esta forma los programadores no tendremos que encargarnos de crear esos archivos de comandos "horribles" que nos genera problemas con la codificación ASCII y el puerto SERIAL y una impresora fiscal con muchas trabas y manuales complicadísimos.
* Compatibilidad con impresoras Epson, Samsung, Ncr y Bematech. 
* Creación de PPD para facilitar la configuración
* Creación de reglas udev rules para detectar automaticamente la marca y el modelo




 [www.alevilar.com](www.alevilar.com "Página web personal de Ale Vilar")


