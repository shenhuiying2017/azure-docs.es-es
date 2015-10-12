<properties
	pageTitle="Directrices del conjunto de IoT de Microsoft Azure de personalización de soluciones preconfiguradas | Microsoft Azure"
	description="Esto le proporcionará directrices sobre la personalización de soluciones preconfiguradas del conjunto de IoT de Azure."
	services=""
	documentationCenter=".net"
	authors="stevehob"
	manager="kevinmil"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/22/2015"
     ms.author="stevehob"/>

# Personalización de soluciones preconfiguradas
Las soluciones preconfiguradas proporcionadas con el conjunto de IoT de Azure permiten a los clientes ver los servicios en el conjunto en funcionamiento para ofrecer una solución completa. Desde este punto de partida, hay varios lugares donde puede realizarse la personalización y ampliación de la solución a escenarios específicos. Las secciones siguientes describen estos puntos de personalización comunes.

## Búsqueda del código fuente
El código fuente de su solución preconfigurada está disponible en Github en los repositorios siguientes:

- Supervisión remota: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)

Este origen se proporciona para mostrar un patrón para implementar la funcionalidad básica de la supervisión remota con el conjunto de IoT de Azure.

## Cambio de las reglas previamente configuradas
La solución de supervisión remota incluye dos trabajos de [análisis de secuencias de Azure](http://azure.microsoft.com/documentation/services/stream-analytics) para implementar la lógica de alarmas y telemetría que se muestran en el panel.

El primer trabajo selecciona todos los datos de la secuencia entrante de la telemetría y crea dos resultados diferentes. El trabajo se denominará [nombre de la solución]-Telemetry.

- El primer resultado toma todos los datos mediante `SELECT *` y almacena los resultados en el blob. El almacenamiento de blobs es donde el panel lee los valores sin formato para crear sus gráficos.
- El segundo resultado realiza los cálculos `AVG()`, `MIN()` y `MAX()` en una ventana deslizante de 5 minutos. Estos datos se muestran en los discos del panel.

Mediante la interfaz de usuario de análisis de secuencias, podrá editar directamente estos trabajos y modificar la lógica o agregar lógica específica para su escenario.

El segundo trabajo funciona en los valores de dispositivo a umbral creados en la página de reglas de la solución. Este trabajo consume como datos de referencia el valor de umbral establecido para cada dispositivo. El trabajo compara el valor de umbral para ver si es mayor (`>`) que el valor real. Este trabajo se puede modificar, por ejemplo, para cambiar el operador de comparación.

***Tenga en cuenta que el panel de supervisión remoto depende de datos específicos, por lo que la modificación de los trabajos puede causar posibles errores en el panel.***

## Adición de reglas propias
Además de los trabajos de análisis de secuencias de Azure preconfigurados modificados, puede usar el Portal de Azure para agregar nuevos trabajos o nuevas consultas a los trabajos existentes.

## Personalización de dispositivos
Una de las actividades de extensión más comunes es trabajar con dispositivos específicos de su escenario. Existen varios métodos para trabajar con dispositivos. Entre estos se incluye la modificación de un dispositivo simulado para que coincida con su escenario, o bien el uso del SDK de dispositivo de IoT de Azure para conectar el dispositivo físico a la solución.

Consulte el documento siguiente para obtener directrices paso a paso para agregar dispositivos a la solución preconfigurada de supervisión remota [conexión de dispositivos al conjunto de IoT](iot-suite-connecting-devices.md).

### Creación de dispositivo simulado propio
El código fuente de la solución de supervisión remota (mencionado anteriormente) incluye un simulador. NET. Este simulador es el que se aprovisiona como parte de la solución y se puede modificar para enviar distintos metadatos, telemetría o responder a distintos comandos.

Además, hemos proporcionado una [muestra de SDK de C](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring) que está diseñada para funcionar con la solución preconfigurada de supervisión remota.

### Creación y uso de dispositivos propios (físicos)
Los [SDK de IoT de Azure](https://github.com/Azure/azure-iot-sdks) proporcionan bibliotecas que permiten conectar distintos tipos de dispositivos (lenguajes y sistemas operativos) a soluciones de IoT.


Para obtener más información sobre los dispositivos de IoT, consulte el [sitio para desarrolladores de IoT de Azure](http://azure.microsoft.com/develop/iot) para obtener documentación y vínculos.

<!---HONumber=Oct15_HO1-->