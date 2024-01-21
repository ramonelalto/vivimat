# Vivimat de Dinitel
Análisis de los sistemas Vivimat III
# Introducción
Desde 2018 la empresa Dinitel ha cerrado y ha dejado los sistemas propietarios que distribuían completamente  desasistidos.
Las empresas de domótica que acuden ante la llamada de asistencia de los usuarios solo recomiendan su sustitución.
Existe un montón de dispositivos en venta en segunda mano por retirada de las instalaciones.
En este proyecto se va a estudiar el sistema para intentar reutilizar los sistemas existentes integrándolos en otros y para poder crear un ecosistema de sustitución a partir de desarrollos DIY.
# Documentación
En la carpeta de documentación se han subido todas las referencias al sistema que se han podido encontrar por internet para que no se pierdan.
También se han subido diversas fotos de las distintas placas para poder realizar el estudio.
Con los sistemas se van a hacer capturas de los distintos buses para que todos los que quieran puedan colaborar en el análisis de las tramas de los mismos.
# Código fuente (src)
Se realizarán librerías para comunicar con los distintos buses desde desarrollos basados en Arduino (Uno, Mega, ESP8266 y ESP32)
# Protocolo BUS 2 - Accesorios
Este bus se conecta a los módulos de control de persianas (Tipo MOD-0106-N) que se les indica su dirección mediante switches (ver fotos en documentación)  
* El bus es de especificación eléctrica RS485.
* Tiene dos señales A-B junto con una señal de 12V y GND.
* Transmite tramas a una velocidad de 19200 baudios.
* Utiliza un protocolo derivado del HDLC:
 * Utiliza marcas de inicio y fin de trama: "0x7E".
 * Utiliza la sustitución de la aparición de 0x7E por [0x7D][0x5E] y la aparición de 0x7D por [0x7D][0x5D].

El formato es indicandose los bytes como [xx]:  
  
#0 | #1 | #2 | #3 | #4 | #5 | #6 | #7 
--- | --- | --- | --- |--- |--- |--- |--- 
[0x7E] | [Longitud] | [Tipo] | [Dirección de destino] | [N bytes indicados en el campo longitud...] | [...] | [Chequeo de trama con la suma de todos los bytes] | [0x7E]  

* Inicio de trama = 0x7E
* Longitud - Es el número de bytes a continuación del campo "Dirección de destino" y hasta antes del "Chequeo de trama con la suma de todos los bytes".
* Tipo - Es el tipo de dispositivo, por ejemplo: 0x08 para las persianas.
* Dirección de destino - Es el valor indicado en los switches de los dispositivos.
* N bytes indicados en el campo longitud... - Esta es la información propiamente dicha. Se especifican ejemplos a continuación.
* Chequeo de trama con la suma de todos los bytes - Es la simple suma de todos los bytes desde "Longitud" hasta el fin de los datos.
* Fin de trama = 0x7E

  # Ejemplos de comunicación con módulo de persianas.
## Consulta periódica a módulo de persiana.
Se realiza constantemente la consulta uno a uno de todos los módulos del bus.  
El módulo contesta con su estado. Ejemplo:  
### Consulta de la placa principal a módulo de persiana de dirección 1:
#0 | #1 | #2 | #3 | #4 | #5 | #6 
--- | --- | --- | --- | --- | --- | ---  
[0x7E] | [Longitud] | [Tipo] | [Dirección de destino] | [N bytes indicados en el campo longitud...] | [Chequeo de trama con la suma de todos los bytes] | [0x7E]
[0x7E] | [0x01] | [0x08] | [0x01] | [0x02] | [0x0C] | [0x7E]
  
  El dato es 0x02 que indica la consulta del estado.
### Respuesta del módulo de persiana de dirección 1 a placa principal:
#0 | #1 | #2 | #3 | #4 | #5 | #6 | #7 | #8
--- | --- | --- | --- | --- | --- | --- | --- | --- 
[0x7E] | [Longitud] | [Tipo] | [Dirección de destino] | [Dato1] | [Dato2] | [Dato3] | [Chequeo de trama con la suma de todos los bytes] | [0x7E]
[0x7E] | [0x03] | [0x00] | [0xF0] | [0x03] | [0x0A] | [0x01] | [0x01] | [0x7E]
* Dato 1 - 0x03 indica respuesta a la consulta de estado
* Dato 2 - 0x0A es el valor de estado de posición de la persiana siendo 0x00 abajo y 0x0A arriba ( se indica el valor entre los valores: 0,2,4,6,8,10)
* Dato 3 - 0x01 es el valor de estado de movimiento, esto es, si sube (0x01) baja (0x02) o está parada (0x00).
## Comando a módulo de persiana.
Se realiza cuando desde una de las pantallas, un temporizador, la APP del móvil o el web server se actúa sobre la persiana indicando una posición de destino.  
El módulo contesta con un ´OK´. Ejemplo:  
### Comando de placa principal a módulo de persiana de dirección 1:
#0 | #1 | #2 | #3 | #4 | #5 | #6 | #7 | #8 | #9 
--- | --- | --- | --- | --- | --- | --- | --- | --- | ---  
[0x7E] | [Longitud] | [Tipo] | [Dirección de destino] | [Dato1] | [Dato2] | [Dato3] | [Dato4] | [Chequeo de trama con la suma de todos los bytes] | [0x7E]
[0x7E] | [0x04] | [0x08] | [0x01] | [0x04] | [0x00] | [0x0A] | [0x11] | [0x2C] | [0x7E]
  
* Dato 1 - 0x04 indica comando a persiana
* Dato 2 - 0x00 ?????? Siempre 0
* Dato 3 - 0x0A es el valor de posición de la persiana deseado siendo 0x00 abajo y 0x0A arriba ( se indica el valor entre los valores: 0,2,4,6,8,10)
* Dato 4 - 0x11 es el valor del tiempo de recorrido de la persiana (0x11 = 17 segundos). Se configura en la placa principal por el instalador (Vivimat project al que no tengo acceso y desearía ;-).
### Respuesta del módulo de persiana de dirección 1 a placa principal:
#0 | #1 | #2 | #3 | #4 | #5 | #6 
--- | --- | --- | --- | --- | --- | --- 
[0x7E] | [Longitud] | [Tipo] | [Dirección de destino] | [Dato1] | [Chequeo de trama con la suma de todos los bytes] | [0x7E]
[0x7E] | [0x01] | [0x00] | [0xF0] | [0x00] | [0xF1] | [0x7E]
* Dato 1 - 0x00 indica respuesta al comando: OK.
