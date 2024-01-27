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

ORIGIN | Tipo | Código | Formato | Detalle | Denominación
--- | --- | --- | --- | --- | --- 
DISPLAY | DATE | 17 | 17 14 01 E8 07 07 15 24 00 | Comando para poner la fecha en la placa base | 
DISPLAY |  | 18 | 18 15 00 |  | 	
MB | | 19 | 19 00 00 03 00 00 00 00 00 0A |  | 	
DISPLAY | PORTERO | 4F | 4F 03 FF - ABRIR PORTAL, 4F 01 01 - HABLAR, 4F 07 00 - VER VIDEOPORTERO |  | 	
DISPLAY | PERSIANA | 7D (SE DESDOBLA) | 7D 5D 02 | OpeningNum=02 | Petición de estado de persiana
MB | PERSIANA | 7E (SE DESDOBLA) | 7D 5E 02 06 00 | OpeningNum=02, % DE APERTURA=60%, NO SUBE NI BAJA (0x01 Subiendo, 0x02 Bajando) | Reporte de estado de persiana
DISPLAY | PERSIANA | 7F | 7F | 00 02 00 02 04 00 | OpeningMode=0, OpeningType=2, OpeningSensorFlag=0,OpeningNum=02, % DE APERTURA=40%, NO SUBE NI BAJA (0x01 Subiendo, 0x02 Bajando)	 | Comando para modificar posición de persiana
DISPLAY | ENCHUFE | 95 | 95 00 | PlugNum = 0 | Petición de estado de enchufe
MB | ENCHUFE | 96 | 96 00 01 | PlugNum=0, PlugStat=1 | Reporte de estado de enchufe
DISPLAY | ENCHUFE | 97 | 97 00 01 00 00 01 | IconID=0, PlugType=1, PLugmode=0, PlugNum=0, PlugStat=1 | Comando de actuación en enchufe
DISPLAY |  | C0	| C0  |  |  		
MB |  | C1 | C1 A5	(A5, 04, F4)  |  | 	
DISPLAY | CLIMA | C2 | C2 |  | Petición de información sobre zona de clima
MB | CLIMA | C3 | C3 00 01 0F 23 05 2D 02 | ClimaNum = 0, ClimaGeneralMode = 1, ClimaSetPointMin=0F,  ClimaSetPointMax=23, ClimaTaMin=05, ClimaTaMax=2D, ClimaHysteresis=2 | Reporte de zona de clima
DISPLAY | CLIMA | C8 | C8 00 | ClimaUnitNum=0 | Petición de estado de zona de clima
MB | CLIMA | C9 | C9 00 15 19 | ClimaUnitNum=0, Temperature=21ºC, Setpoint=25ºC, Clima=OFF (bit7=0)	 | Reporte de estado de zona de clima
DISPLAY | CLIMA | CA | CA 00 00 03 19	00, 00, |  ClimaUnitNum=0, ClimaUnitMode=[0,1,3], ClimaUnitSetPoint=25ºC	 | 
DISPLAY | ???? | CB | CB | ????	 | 
MB | ???? | CC | CC C9 C4 | ???? | 	
