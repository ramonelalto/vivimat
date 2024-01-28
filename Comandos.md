# Introducción
La placa principal sondea los dispositivos de forma secuencial. Les envía una serie de datos de estado, entre ellos la fecha y hora.
Los módulos pueden responder con un OK o enviar un comando para realizar una actuación, con lo que la placa principal contestaría OK, o pedir una información con lo que la placa principal responderá con ellos.
Denominaremos peticiones, comandos y respuestas a la respuesta a la ronda de contacto.
## Formato de la ronda de sondeo:
MB to DISPLAY: 
7E 17 00 01 02 FF 80 00 00 00 01 01 DA 07 05 02 13 34 00 00 03 00 0A 00 00 00 00 00 D7 7E

MARK | LONGITUD(L:17 H:00) | TYPE | ADDRESS | DATOS | SUM | MARK
--- | --- | --- | --- | --- | --- | --- 
7E | 0017 | 01 | 02 | FF 80 00 00 00 01 01 DA 07 05 02 13 34 00 00 03 00 0A 00 00 00 00 00 | D7 | 7E

Significado de los datos:

#Dato | #Significado 
 --- | ---
FF | Código de ronda 
80 |
00 |
00 |
00 |
01 | Día: 1
01 | Mes: enero
DA | Año (Low)
07 | Año (High) 2010 (0x07DA)
05 | Día de la semana: Viernes
02 | Hora: 2
13 | Minuto: 19
34 | Segundo: 52
00 | 
00 | 
03 | Status: Bit0= 1 siempre?, Bit1= 0 Armado alarma, 1 Desarmado alarma, Bit2= 0 Armado o no armado alarma, 1 : Armando durante el timer de la alarma
00 | Memoria alarma: 0 No se ha armado nunca la alarma, 1 Armado perimetral, 2 Armado Parcial y 3 Armado Total
0A |
00 |
00 |
00 |
00 |
00 |


## Ronda con OK de respuesta:
Ronda de consulta de la placa principal(MB) al dispositivo (DISPLAY) tipo 1 con dirección 2. 

MB to DISPLAY: 
7E 17 00 01 02 FF 80 00 00 00 01 01 DA 07 05 02 13 34 00 00 03 00 0A 00 00 00 00 00 D7 7E

MARK | LONGITUD(L:17 H:00) | TYPE | ADDRESS | DATOS | SUM | MARK
--- | --- | --- | --- | --- | --- | --- 
7E | 0017 | 01 | 02 | FF 80 00 00 00 01 01 DA 07 05 02 13 34 00 00 03 00 0A 00 00 00 00 00 | D7 | 7E
  
DISPLAY to MB:
7E 01 00 00 F0 00 F1 7E  
MARK | LONGITUD(L:01 H:00) | TYPE | ADDRESS | DATOS | SUM | MARK
--- | --- | --- | --- | --- | --- | ---
7E | 0001 | 00 | F0 | 00 | F1 | 7E

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

## Ronda con Comando de respuesta:
Ronda de consulta de la placa principal(MB) al dispositivo (DISPLAY) tipo 1 con dirección 2. 

MB to DISPLAY: 
7E 17 00 01 02 FF 80 00 00 00 01 01 DA 07 05 02 13 34 00 00 03 00 0A 00 00 00 00 00 D7 7E
  
    Se hace la ronda de consulta
MARK | LONGITUD(L:17 H:00) | TYPE | ADDRESS | DATOS | SUM | MARK
--- | --- | --- | --- | --- | --- | --- 
7E | 0017 | 01 | 02 | FF 80 00 00 00 01 01 DA 07 05 02 13 34 00 00 03 00 0A 00 00 00 00 00 | D7 | 7E

DISPLAY to MB:
7E 01 00 00 F0 00 F1 7E  
  
    Se envía el comando para modificar posición de persiana
MARK | LONGITUD(L:07 H:00) | TYPE | ADDRESS | DATOS | SUM | MARK
--- | --- | --- | --- | --- | --- | ---
7E | 0007 | 00 | F0 | 7F 00 02 00 02 04 00 | 7D 5E desdoble de 7E = suma de bytes | 7E
  
  OpeningMode=0, OpeningType=2, OpeningSensorFlag=0,OpeningNum=02, % DE APERTURA=40%, NO SUBE NI BAJA (0x01 Subiendo, 0x02 Bajando)	

MB to DISPLAY:
7E 01 00 01 02 00 04 7E  
  
    Se envía el OK al comando desde la placa principal
MARK | LONGITUD(L:01 H:00) | TYPE | ADDRESS | DATOS | SUM | MARK
--- | --- | --- | --- | --- | --- | ---
7E | 0001 | 01 | 02 | 00 | 04 | 7E  

# Peticiones, comandos y respuestas
Cuando la pantalla quiere decir a la placa base algo espera su turno de consulta a la misma. Ahí puede contestar: 
  * OK, código 00
  * Petición. Esperando una respuesta de información, por ejemplo porque está visualizando la pantalla de una persiana y quiere saber su estado.
  * Comando. Por ejemplo, quiere mandar que una persiana suba.

A continuación el inventario de peticiones, comandos y respuestas, tanto de la placa principal como del módulo APP o pantalla.
Se intentan agrupar por tipo pero están ordenados por el código.
Source | Tipo | Código | Formato | Detalle | Denominación
--- | --- | --- | --- | --- | --- 
MB |  | 00 | 00 | OK a petición o ronda de consulta sin respuesta de información |  OK habital a la ronda o respuesta a un comando ejecutado correctamente	
DISPLAY | DATE | 17 | 17 14 01 E8 07 07 15 24 00 | Día=20 (0x14), Mes=1 (0x01), Año=2024 (0x07E8), Día de la semana = Domingo (0x07), Hora=21 (0x15), Minuto=36 (0x24), Segundo=0 (0x00) | Comando para poner la fecha en la placa base 
DISPLAY |  | 18 | 18 15 00 |  | 	
MB | | 19 | 19 00 00 03 00 00 00 00 00 0A |  | 	
DISPLAY | PORTERO | 4F | 4F 03 FF - ABRIR PORTAL, 4F 01 01 - HABLAR, 4F 07 00 - VER VIDEOPORTERO |  | 	
DISPLAY | PERSIANA | 7D (SE DESDOBLA) | 7D 5D 02 | OpeningNum=02 | Petición de estado de persiana
MB | PERSIANA | 7E (SE DESDOBLA) | 7D 5E 02 06 00 | OpeningNum=02, % DE APERTURA=60%, NO SUBE NI BAJA (0x01 Subiendo, 0x02 Bajando) | Reporte de estado de persiana
DISPLAY | PERSIANA | 7F | 7F 00 02 00 02 04 00 | OpeningMode=0, OpeningType=2, OpeningSensorFlag=0,OpeningNum=02, % DE APERTURA=40%, NO SUBE NI BAJA (0x01 Subiendo, 0x02 Bajando)	 | Comando para modificar posición de persiana
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
