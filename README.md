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
El bus es de especificación eléctrica RS485. Tiene dos señales A-B junto con una señal de 12V y GND.
Transmite tramas a una velocidad de 19200 baudios.
Utiliza un protocolo derivado del HDLC. Utiliza marcas de inicio y fin de trama: "0x7E"
El formato es indicandose los bytes como [xx]:
[0x7E] [Longitud] [Tipo] [Dirección de destino] [N bytes indicados en el campo longitud...] [Chequeo de trama con la suma de todos los bytes][0x7E]
#0 | #1 | #2 | #3 | #4 | #5 | #6 | #7 
--- | --- | --- | --- |--- |--- |--- |--- 
[0x7E] | [Longitud] | [Tipo] | [Dirección de destino] | [N bytes indicados en el campo longitud...] | [...] | [Chequeo de trama con la suma de todos los bytes] | [0x7E]
