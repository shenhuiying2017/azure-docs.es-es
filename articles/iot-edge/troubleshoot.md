---
title: "Solución de problemas en Azure IoT Edge | Microsoft Docs"
description: "Resolución de problemas habituales y adquisición de conocimientos sobre solución de problemas para Azure IoT Edge"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 7b9f9f8295aac0920ae4726289c535aae12c4482
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemas habituales y soluciones para Azure IoT Edge

Si experimenta problemas al ejecutar Azure IoT Edge en el entorno, use este artículo como guía para solucionarlos. 

## <a name="standard-diagnostic-steps"></a>Pasos de diagnóstico estándar 

Si detecta un problema, consiga más información sobre el estado del dispositivo de IoT Edge mediante la revisión de los registros de contenedor y de los mensajes que se envían hacia el dispositivo y desde este. Use los comandos y las herramientas de esta sección para recopilar información. 

* Examine los registros de los contenedores de Docker para detectar problemas. Empiece por los contenedores implementados y, a continuación, examine los contenedores que componen el entorno de ejecución de IoT Edge: agente de Edge y centro de Edge. Los registros del agente de Edge normalmente proporcionan información sobre el ciclo de vida de cada contenedor. Los registros del centro de Edge proporcionan información sobre mensajería y enrutamiento. 

   ```cmd
   docker logs <container name>
   ```

* Vea los mensajes que se envían mediante el centro de Edge y recopile información sobre las actualizaciones de las propiedades del dispositivo con los registros detallados procedentes de los contenedores del entorno de ejecución. Puede que desee agregar la opción "--auto-cert-gen-force-no-passwords" si está siguiendo los artículos de la guía de inicio rápido.

   ```cmd
   iotedgectl setup --connection-string "{device connection string}" --runtime-log-level debug
   ```

* Si experimenta problemas de conectividad, inspeccione las variables del entorno del dispositivo perimetral, como la cadena de conexión del dispositivo:

   ```cmd
   docker exec edgeAgent printenv
   ```

También puede comprobar los mensajes que se envían entre los dispositivos de IoT Hub e IoT Edge. Vea estos mensajes mediante la extensión de [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) para Visual Studio Code. Para más instrucciones, consulte [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/) (Herramienta práctica para desarrollar con Azure IoT).

Después de investigar los registros y mensajes para obtener información, también puede intentar reiniciar el entorno de ejecución de Azure IoT Edge:

   ```cmd
   iotedgectl restart
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>El agente de Edge se detiene después de aproximadamente un minuto

El agente de Edge se inicia y se ejecuta correctamente durante un minuto aproximadamente y, a continuación, se detiene. Los registros indican que el agente de Edge está intentando conectarse a IoT Hub a través de AMQP y aproximadamente 30 segundos después intenta conectarse mediante AMQP con el protocolo Websocket. Cuando se produce un error, se cierra el agente de Edge. 

Registros de ejemplo de Edge:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Causa principal
Una configuración de redes en la red del host está impidiendo que el agente de Edge alcance la red. El agente intentará conectarse en primer lugar a través de AMQP (puerto 5671). Si se produce un error, lo intentará mediante los protocolos websocket (puerto 443).

El entorno de ejecución de IoT Edge configura una red para cada uno de los módulos en los que se comunica. En Linux, esta red es una red de puente. En Windows, se usa NAT. Este problema es más frecuente en los dispositivos de Windows que usan contenedores de Windows que usan la red NAT. 

### <a name="resolution"></a>Resolución
Asegúrese de que hay una ruta a Internet para las direcciones IP asignadas a esta red de puente o NAT. A veces, una configuración de VPN en el host invalida la red de IoT Edge. 

## <a name="edge-hub-fails-to-start"></a>No se puede iniciar el centro de Edge

Se produce un error al iniciar el centro de Edge y se imprime el siguiente mensaje en los registros: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Causa principal
Algún otro proceso del equipo host se ha enlazado con el puerto 443. El centro de Edge asigna los puertos 5671 y 443 para su uso en escenarios de puerta de enlace. Se producirá un error en la asignación de puertos si hay algún otro proceso enlazado con este puerto. 

### <a name="resolution"></a>Resolución
Busque y detenga el proceso que está utilizando el puerto 443. Este proceso suele ser un servidor web.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>El agente de Edge no puede acceder a la imagen de un módulo (403)
No se puede ejecutar un contenedor y los registros del agente de Edge muestran un error 403. 

### <a name="root-cause"></a>Causa principal
El agente de Edge no tiene permisos para acceder a la imagen de un módulo. 

### <a name="resolution"></a>Resolución
Intente ejecutar el comando `iotedgectl login` de nuevo.

## <a name="iotedgectl-cant-find-docker"></a>iotedgectl no puede encontrar Docker
iotedgectl no puede ejecutar el programa de instalación o el comando de inicio y se imprime el mensaje siguiente en los registros:
```output
File "/usr/local/lib/python2.7/dist-packages/edgectl/host/dockerclient.py", line 98, in get_os_type
  info = self._client.info()
File "/usr/local/lib/python2.7/dist-packages/docker/client.py", line 174, in info
  return self.api.info(*args, **kwargs)
File "/usr/local/lib/python2.7/dist-packages/docker/api/daemon.py", line 88, in info
  return self._result(self._get(self._url("/info")), True)
```

### <a name="root-cause"></a>Causa principal
iotedgectl no puede encontrar Docker, que es un requisito previo.

### <a name="resolution"></a>Resolución
Instale Docker, asegúrese de que está ejecutando e inténtelo de nuevo.

## <a name="next-steps"></a>Pasos siguientes
¿Cree que encontró un error en la plataforma de IoT Edge? Use la opción [Enviar un problema](https://github.com/Azure/iot-edge/issues) para que podamos seguir mejorando. 
