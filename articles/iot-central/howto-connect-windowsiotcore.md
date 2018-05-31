---
title: Conexión de un dispositivo Windows IoT Core a una aplicación de Azure IoT Central | Microsoft Docs
description: Como desarrollador de dispositivos, aprenda a conectar un dispositivo MXChip IoT DevKit a una aplicación de Azure IoT Central.
services: iot-central
author: miriamb
ms.author: mriamb
ms.date: 04/09/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: peterpr
ms.openlocfilehash: c38231f97eeb1c4511702bf3e788f72918cab045
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200411"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Conexión de un dispositivo Windows IoT Core a una aplicación de Azure IoT Central

En este artículo se describe cómo conectar, en tanto que desarrollador de dispositivos, un dispositivo Windows IoT Core a una aplicación de Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de empezar

Necesitará lo siguiente para completar los pasos de este artículo:

1. Una aplicación de Azure IoT Central creada a partir de la plantilla de aplicación **Ejemplo Devkits**. Para más información, consulte [Create your Azure IoT Central Application](howto-create-application.md) (Creación de una aplicación de Azure IoT Central).
2. Un dispositivo en el que se ejecuta el sistema operativo Windows 10 IoT Core. En este tutorial, usaremos un dispositivo Raspberry Pi.

Una aplicación creada a partir de la plantilla de aplicación **Ejemplo Devkits** incluye una plantilla de dispositivo **Windows IoT Core** con las siguientes características:

### <a name="telemetry-measurements"></a>Medidas de telemetría

| Nombre del campo     | Unidades  | Mínima | Máxima | Posiciones decimales |
| -------------- | ------ | ------- | ------- | -------------- |
| humedad       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Settings

Valores numéricos

| Nombre para mostrar | Nombre del campo | Unidades | Posiciones decimales | Mínima | Máxima | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Fan Speed (Velocidad del ventilador)    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |


### <a name="properties"></a>Properties (Propiedades)

| Escriba            | Nombre para mostrar | Nombre del campo | Tipo de datos |
| --------------- | ------------ | ---------- | --------- |
| Propiedad de dispositivo | Die number (Número de chip)   | dieNumber  | número    |
| Texto            | Ubicación     | location   | N/D       |

## <a name="add-a-real-device"></a>Adición de un dispositivo real

En su aplicación de Azure IoT Central, agregue un dispositivo real de la plantilla de dispositivo **Windows IoT Core** y tome nota de la cadena de conexión del dispositivo. Para obtener más información, consulte [Add a real device to your Azure IoT Central application](tutorial-add-device.md) (Adición de un dispositivo real a su aplicación de Azure IoT Central).

### <a name="prepare-the-windows-iot-core-device"></a>Preparación del dispositivo Windows IoT Core

Para configurar un dispositivo de Windows IoT Core, siga la guía paso a paso en [Set up a Windows IoT Core device] (Configuración de un dispositivo Windows IoT Core) (https://github.com/Microsoft/microsoft-iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device).

### <a name="add-a-real-device"></a>Adición de un dispositivo real

En su aplicación de Azure IoT Central, agregue un dispositivo real de la plantilla de dispositivo **Windows IoT Core** y tome nota de la cadena de conexión del dispositivo. Para obtener más información, consulte [Add a real device to your Azure IoT Central application](tutorial-add-device.md) (Adición de un dispositivo real a su aplicación de Azure IoT Central).

## <a name="prepare-the-windows-10-iot-core-device"></a>Preparación del dispositivo Windows 10 IoT Core

### <a name="what-youll-need"></a>Qué necesita

Para configurar un dispositivo físico Windows 10 IoT Core, debe tener primero un dispositivo que ejecute Windows 10 IoT Core. Obtenga información acerca de cómo configurar un dispositivo Windows 10 IoT Core [aquí](https://developer.microsoft.com/en-us/windows/iot/getstarted/prototype/setupdevice).

También necesitará una aplicación cliente que pueda comunicarse con Azure IoT Central. Puede crear su propia aplicación personalizada con el SDK de Azure e implementarla en el dispositivo mediante Visual Studio, o puede descargar un [ejemplo pregenerado](https://developer.microsoft.com/en-us/windows/iot/samples) y simplemente implementarlo y ejecutarlo en el dispositivo. 

### <a name="deploying-the-sample-client-application"></a>Implementación de la aplicación cliente de ejemplo

Para implementar la aplicación cliente del paso anterior en el dispositivo Windows 10 IoT con el fin de prepararlo:

**Asegúrese de que la cadena de conexión se almacena en el dispositivo para la aplicación cliente que se va a utilizar**
* En el escritorio, guarde la cadena de conexión en un archivo de texto denominado connection.string.iothub.
* Copie el archivo de texto en la carpeta de documentos del dispositivo: `[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

Una vez hecho esto, debe abrir el [Portal de dispositivos Windows](https://docs.microsoft.com/en-us/windows/iot-core/manage-your-device/deviceportal); para ello, escriba http://[dirección-IP-dispositivo]:8080 en cualquier explorador.

Desde ahí, como se muestra en el siguiente GIF, haga lo siguiente:
1. Expanda el nodo "Aplicaciones" de la izquierda.
2. Haga clic en "Quick-run samples" (Ejecutar ejemplos rápidamente).
3. Haga clic en "Azure IoT Hub Client" (Cliente de Azure IoT Hub).
4. Haga clic en "Deploy and run" (Implementar y ejecutar).

![GIF del cliente de Azure IoT Hub en el Portal de dispositivos de Windows](./media/howto-connect-windowsiotcore/iothubapp.gif)

Si el proceso se completa correctamente, la aplicación se iniciará en el dispositivo con el aspecto siguiente:

![Captura de pantalla de la aplicación cliente de Azure IoT Hub](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

En Azure IoT Central, puede ver cómo el código que se ejecuta en Raspberry Pi interactúa con la aplicación:

* En la página **Measurements** (Medidas) para el dispositivo real, puede ver la telemetría.
* En la página **Properties** (Propiedades), puede ver el valor de la propiedad Die Number (Número de chip) notificado.
* En la página **Settings** (Configuración), puede cambiar distintos parámetros en el instalador de Raspberry Pi, como el voltaje y la velocidad del ventilador.

## <a name="download-the-source-code"></a>Descarga del código fuente

Si desea explorar y modificar el código fuente de la aplicación cliente, puede descargarlo desde GitHub [aquí](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients). Si tiene previsto modificar el código, debe seguir estas instrucciones en [este](https://github.com/Microsoft/Windows-iotcore-samples) archivo Léame para su sistema operativo de escritorio.

> [!NOTE]
> Si **git** no está instalado en el entorno de desarrollo, puede descargarlo desde [https://git-scm.com/download](https://git-scm.com/download).
