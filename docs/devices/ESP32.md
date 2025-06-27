# ESP32 

Pruebas para la conexión del microcontrolador ESP32 al sistema de laboratorios remotos. Para la conexión del ESP32 se han realizado un programa base sobre el cual se asentarán los equipos remotos conectados al sistema. 

El programa garantiza las siguientes especificaciones:

1. **Periodo de muestreo determinista**. Necesario para las aplicaciones de control y supervisión.
2. **Comunicaciones en tiempo real**.
3. **Máquina de estados** que permita varios modos de funcionamiento. Stand by, en uso, conectado, no-conectado, etc.

La programación del dispositivo se va a realizar mediante el framework de Arduino y el IDE platform IO. Nos permitirá incluir módulos de Espressif para la gestión de procesos, nucleos, etc.


## Periodo de muestreo determinista

 



## Comunicaciones en tiempo real mediante WIFI 

Para garantizar la conexión en tiempo real entre el ESP32 y el backend se propone una comunicación por websockets. El ESP32 se configura como un cliente websocket y el backend como un servidor websockets. 






