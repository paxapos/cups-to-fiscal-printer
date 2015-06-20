Drivers de impresión de impresoras fiscales utilizando CUPS.
 - PARA DESARROLLADORES -

Por el momento sólo funciona con impresoras Hasar, pero si me ayudan un poco podremos lograr que ande en todas


= ¿Como imprimir desde mi programa?

Para que funcione hay que generar un listado de comandos fiscales en tu programa, por ejemplo:

9X 
(para imprimir un comprobante X en una Hasar)



luego se envia a imprimir. Para ello lo podes hacer, por ejemplo, creando un archivo en la carpeta temporal. Luego ejecutas;
 
   lp -d impresora_fiscal archivo_a_imprimir.txt



= Conceptos básicos de funcionamiento y configuración del driver.

El driver funciona como un backend de CUPS (https://community.kde.org/Printing/Developer_Tools#.272dir.27_CUPS_backend_script_.28Bash_code.29). Lo que hace es simplemente, agarrar el texto que le llega, y enviarlo a imprimir con el ejecutable spooler de Hasar.

De esta forma será simple crear nuevos drivers para soportar distintas marcas y modelos. Solo es cuestion de reescribir este backend, y ejecutar el "spooler" correcto.




= Instalación

1) Deberás copiar el ejecutable (el spooler de Hasar) correcto según la arquitectura que estes usando dentro de /usr/bin
2) Deberás copiar el backend dentro de /usr/lib/cups/backend
3) Reiniciar CUPS: sudo service cups restart
4) Crear la nueva impresora seleccionando el backend ("fiscal:/ttyUSB0", "fiscal:/ttyS0") e introduciendo el dispositivo serial donde esta conectado la impresora.
5) Configurar el PPD como "RAW" para evitar que CUPS modifique el archivo de comandos enviado. Tiene que llegar al "backend" sin ningún tipo de modificaciones porque la impresorá no funcionará y hasta puede llegar a quedar bloqueada