# vivimat
Análisis de los sistemas Vivimat III
# Introducción
Desde 2018 la empresa Dinitel ha cerrado y ha dejado los sistemas propietarios que distribuian completamente  desasistidos.
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
### Placa principal a módulo:
#0 | #1 | #2 | #3 | #4 | #5 | #6 
--- | --- | --- | --- |--- |--- |--- |--- 
[0x7E] | [Longitud] | [Tipo] | [Dirección de destino] | [N bytes indicados en el campo longitud...] | [Chequeo de trama con la suma de todos los bytes] | [0x7E] 
--- | --- | --- | --- |--- |--- |--- 
[0x7E] | [0x01] | [0x08] | [0x01] | [0x02] | [0x0C] | [0x7E]
