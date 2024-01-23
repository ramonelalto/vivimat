# Introducción
La placa principal sondea los dispositivos de forma secuencial. Les envía una serie de datos de estado, entre ellos la fecha y hora.
Los módulos pueden responder con un OK o enviar un comando para realizar una actuación, con lo que la placa principal contestaría OK, o pedir una información con lo que la placa principal responderá con ellos.
Denominaremos comandos a ambos tipos de respuesta a la ronda de contacto. Por ejemplo:
## Ronda con OK de respuesta:
Ronda de consulta de la placa principal(MB) al dispositivo (DISPLAY) tipo 1 con dirección 2. 

MB to DISPLAY: MARK(7E) - LONGITUD(L:17 H:00) - TYPE(01) - ADDRESS(02) - DATOS[FF 80 00 00 00 01 01 DA 07 05 02 13 34 00 00 03 00 0A 00 00 00 00 00] - SUM(D7) - MARK(7E)
  
  DISPLAY to MB:MARK(7E) - LONGITUD(L:01 H:00) -  TYPE(00) - ADDRESS(F0) - DATOS[00] - SUM(F1) - MARK(7E)

## Ronda con Petición de respuesta:
Ronda de consulta de la placa principal(MB) al dispositivo (DISPLAY) tipo 1 con dirección 2. 

MB to DISPLAY: MARK(7E) - LONGITUD(L:17 H:00) - TYPE(01) - ADDRESS(02) - DATOS[FF 80 00 00 00 01 01 DA 07 05 02 13 34 00 00 03 00 0A 00 00 00 00 00] - SUM(D7) - MARK(7E)

    Se hace la ronda de consulta
  DISPLAY to MB:MARK(7E) - LONGITUD(L:01 H:00) -  TYPE(00) - ADDRESS(F0) - DATOS[C2] - SUM(B3) - MARK(7E)
    
    Se pide configuración del clima (Comando 0xC2)
  MB to DISPLAY: MARK(7E) - LONGITUD(L:08 H:00) - TYPE(01) - ADDRESS(02) - DATOS[C3 00 01 0F 23 05 2D 02] - SUM(35) - MARK(7E)

    
    Se responde (Comando 0xC3) indicando:
  * ClimaNum = 0
  * ClimaGeneralMode = 1
  * ClimaSetPointMin = 15ºC
  * ClimaSetPointMax = 35ºC
  * ClimaTaMin = 5ºC
  * ClimaTaMax = 45ºC
  * ClimaHysteresis = 2ºC

