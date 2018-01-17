---
title: "Implementación de Modbus en Azure IoT Edge | Microsoft Docs"
description: "Permitir dispositivos que usan Modbus TCP para comunicarse con Azure IoT Hub mediante la creación de un dispositivo de puerta de enlace con IoT Edge"
services: iot-Edge
documentationcenter: 
author: kgremban
manager: timlt
editor: chrisgmsft
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: kgremban
ms.custom: 
ms.openlocfilehash: e239bde48c3da0d899e3c78bdd39f520c4128b95
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway---preview"></a>Conexión de dispositivos que usan Modbus TCP a través de una puerta de enlace con IoT Edge: versión preliminar

Si desea conectar dispositivos de IoT que usan los protocolos Modbus TCP o RTU con Azure IoT Hub, use un dispositivo de IoT Edge como puerta de enlace. Este dispositivo de puerta de enlace permite leer los datos de los dispositivos de Modbus y, posteriormente, comunica esos datos a la nube mediante un protocolo compatible. 

![Los dispositivos de Modbus se conectan con IoT Hub mediante una puerta de enlace perimetral](./media/deploy-modbus-gateway/diagram.png)

En este artículo se explica cómo crear su propia imagen de contenedor para un módulo Modbus (o puede usar un ejemplo creado previamente), y cómo implementarlo después en el dispositivo de IoT Edge que actuará como puerta de enlace. 

En este artículo se da por supuesto que va a usar el protocolo Modbus TCP. Para más información sobre cómo configurar el módulo para que admita Modbus RTU, consulte el proyecto [Módulo Modbus de Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) en Github. 

## <a name="prerequisites"></a>Requisitos previos
* Un dispositivo de Azure IoT Edge. Para ver un tutorial sobre cómo configurar uno, consulte [Implementación de Azure IoT Edge en un dispositivo simulado en Windows](tutorial-simulate-device-windows.md) o [Linux](tutorial-simulate-device-linux.md). 
* La cadena de conexión de clave principal para el dispositivo de IoT Edge.
* Un dispositivo de Modbus físico o simulado que admita Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Preparación de un contenedor Modbus

Si desea probar la funcionalidad de puerta de enlace de Modbus, Microsoft tiene un módulo de ejemplo que puede usar. Para usar el módulo de ejemplo, vaya a la sección [Ejecución de la solución](#run-the-solution) y escriba lo siguiente como URI de imagen: 

```URL
microsoft/azureiotedge-modbus-tcp:1.0-preview
```

Si desea crear su propio módulo y personalizarlo para su entorno, hay un proyecto de código abierto llamado [Módulo Modbus de Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) en Github. Siga las instrucciones de ese proyecto para crear su propia imagen de contenedor. Si crea su propia imagen de contenedor, consulte [Desarrollar e implementar un módulo IoT Edge C#](tutorial-csharp-module.md) para obtener instrucciones sobre cómo publicar imágenes de contenedor en un registro y cómo implementar un módulo personalizado en el dispositivo. 


## <a name="run-the-solution"></a>Ejecución de la solución
1. En [Azure Portal](https://portal.azure.com/), vaya hasta la instancia de IoT Hub.
2. Vaya a **IoT Edge (versión preliminar)** y seleccione el dispositivo de IoT Edge.
3. Seleccione **Set modules** (Establecer módulos).
4. Agregue el módulo Modbus:
   1. Seleccione **Add IoT Edge module** (Agregar módulo de IoT Edge).
   2. En el campo **Nombre**, escriba "modbus".
   3. En el campo **Imagen**, escriba el URI de la imagen del contenedor de ejemplo: `microsoft/azureiotedge-modbus-tcp:1.0-preview`.
   4. Marque la casilla **Habilitar** para actualizar las propiedades deseadas del módulo gemelo.
   5. Copie el siguiente JSON en el cuadro de texto. Cambie el valor de **SlaveConnection** a la dirección IPv4 del dispositivo Modbus.

      ```JSON
      {  
        "properties.desired":{  
          "PublishInterval":"2000",
          "SlaveConfigs":{  
            "Slave01":{  
              "SlaveConnection":"<IPV4 address>",
              "HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{  
                "Op01":{  
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"400001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. Seleccione **Guardar**.
5. Vuelva al paso **Agregar módulos** y seleccione **Siguiente**.
7. En el paso **Specify Routes** (Especificar rutas), copie el siguiente archivo JSON en el cuadro de texto. Esta ruta envía todos los mensajes recopilados por el módulo Modbus a IoT Hub. En esta ruta, ''modbusOutput'' es el punto de conexión que usa el módulo Modbus para los datos de salida y "upstream" es un destino especial que indica a Edge Hub que envíe mensajes a IoT Hub. 
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. Seleccione **Siguiente**. 
9. En el paso **Revisar plantilla**, seleccione **Enviar**. 
10. Vuelva a la página de detalles del dispositivo y seleccione **Actualizar**. Debería ver que el nuevo módulo **modbus** se ejecuta junto con el runtime de IoT Edge.

## <a name="view-data"></a>Visualización de datos
Vea los datos que entran a través del módulo modbus:
```cmd/sh
docker logs -f modbus
```

También puede ver la telemetría que envía el dispositivo mediante la [herramienta del explorador IoT Hub](https://github.com/azure/iothub-explorer). 

## <a name="next-steps"></a>pasos siguientes

- Para más información acerca de cómo los dispositivos de IoT Edge pueden actuar como puertas de enlace, consulte [Creación de un dispositivo de IoT Edge que actúa como una puerta de enlace transparente](how-to-create-transparent-gateway.md)
- Para más información sobre cómo funcionan los módulos de IoT Edge, consulte [Información de los módulos de Azure IoT Edge](iot-edge-modules.md)