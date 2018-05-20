---
title: Recepción de un correo electrónico al abrir la puerta con el servicio SendGrid y Azure Functions | Microsoft Docs
description: Controle el sensor magnético para detectar cuándo se abre una puerta y utilice Azure Functions para enviar una notificación por correo electrónico.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 4eb13a588f0ffd1377caf3ce9bac4886f052a22f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="door-monitor"></a>Supervisión de puertas          

MXChip IoT DevKit tiene integrado un sensor magnético. En este proyecto, vamos a detectar la presencia o ausencia de un fuerte campo magnético cercano; en este caso, uno procedente de un pequeño imán permanente.

## <a name="what-you-learn"></a>Conocimientos que adquirirá

En este proyecto, aprenderá a:
- Utilizar el sensor magnético de MXChip IoT DevKit para detectar el movimiento de un imán cercano.
- Utilizar el servicio SendGrid para enviar notificaciones a una dirección de correo electrónico.

> [!NOTE]
> Para realizar este proyecto:
> - Debe instalar un imán en el borde de la puerta.
> - Debe instalar DevKit en la jamba de la puerta junto al imán. Al abrir o cerrar la puerta, se activará el sensor, lo que hará que reciba por correo electrónico una notificación del evento.

## <a name="what-you-need"></a>Lo que necesita

Siga la [Guía de introducción]({{"/docs/get-started/" | absolute_url }}) para:

* Conexión de DevKit con Wi-Fi
* Preparación del entorno de desarrollo

Una suscripción de Azure activa. Si no dispone de una, puede registrarse mediante uno de estos métodos:

* Puede activar una [cuenta de Microsoft Azure de prueba de 30 días gratis](https://azure.microsoft.com/free/).
* Puede reclamar su [crédito de Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) si tiene una suscripción a MSDN o Visual Studio.

## <a name="deploy-sendgrid-service-in-azure"></a>Implementación del servicio SendGrid en Azure

[SendGrid](https://sendgrid.com/) es una plataforma de entrega de correo electrónico basada en la nube. Este servicio es el que se utilizará para enviar notificaciones por correo electrónico.

> [!NOTE]
> Si ya tiene implementado el servicio SendGrid, puede continuar directamente en el paso [Implementación de IoT Hub en Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Implementación de SendGrid

Para aprovisionar los servicios de Azure, utilice el botón **Deploy to Azure** (Implementar en Azure). Este botón le permite implementar de forma rápida y sencilla los proyectos de código abierto en Microsoft Azure.

Haga clic en el botón **Deploy to Azure** (Implementar en Azure) que aparece a continuación. 

[![Implementación en Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Aparecerá la página siguiente.

> [!NOTE]
> De lo contrario, deberá iniciar sesión primero en la cuenta de Azure.

Complete el formulario de registro:

  * **Grupo de recursos**: cree un grupo de recursos para hospedar el servicio SendGrid o utilice uno existente. Consulte [Uso de grupos de recursos para administrar los recursos de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Nombre**: nombre del servicio SendGrid. Elija un nombre único que sea diferente al de otros servicios que pudiera tener.

  * **Contraseña**: el servicio requiere una contraseña, aunque en este proyecto no se utilizará.

  * **Correo electrónico**: el servicio SendGrid enviará un mensaje de prueba a esta dirección de correo electrónico.

  > [!NOTE]
  > Active la opción **Pin to dashboard** (Anclar al panel) para que resulte más fácil encontrar esta aplicación en el futuro.
 
![Implementación de SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

### <a name="sendgrid-api-key-creation"></a>Creación de claves de API de SendGrid

Una vez que la implementación se haya realizado correctamente, selecciónela y haga clic en el botón **Administrar**. Se abrirá automáticamente la página de SendGrid, donde tendrá que verificar su dirección de correo electrónico.

![Administrar SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

En la página de SendGrid, haga clic en **Settings** (Configuración) > **API Keys** (Claves de API) > **Create API Key** (Crear clave de API). Especifique el nombre en **API Key Name** (Nombre de clave de API) y haga clic en **Create & View** (Crear y ver).

![Creación de la primera API de SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

![Creación de la segunda API de SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

La clave de API solamente aparecerá una vez. No olvide copiarla y asegúrese de guardarla en un lugar seguro, ya que tendrá que utilizarla en el paso siguiente.

## <a name="deploy-iot-hub-in-azure"></a>Implementación de IoT Hub en Azure

Con el procedimiento siguiente, aprovisionará otros servicios IoT de Azure e implementará Azure Functions para este proyecto.

Haga clic en el botón **Deploy to Azure** (Implementar en Azure) que aparece a continuación. 

[![Implementación en Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Aparecerá la página siguiente.

> [!NOTE]
> De lo contrario, deberá iniciar sesión primero en la cuenta de Azure.

Complete el formulario de registro:

  * **Grupo de recursos**: cree un grupo de recursos para hospedar el servicio SendGrid o utilice uno existente. Consulte [Uso de grupos de recursos para administrar los recursos de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Iot Hub Name** (Nombre del IoT Hub): nombre del centro de IoT. Elija un nombre único que sea diferente al de otros servicios que pudiera tener.

  * **Iot Hub Sku** (SKU de IoT Hub): el nivel F1 es gratis (solo se admite uno por suscripción). Puede ver más información sobre los precios en [nivel de precios y de escala](https://azure.microsoft.com/pricing/details/iot-hub/).

  * **From Email** (Correo electrónico de origen): debería tratarse de la misma dirección de correo electrónico que utilizó al configurar el servicio SendGrid.

  > [!NOTE]
  > Active la opción **Pin to dashboard** (Anclar al panel) para que resulte más fácil encontrar esta aplicación en el futuro.
 
![Implementación de IoT Hub](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

## <a name="build-and-upload-the-code"></a>Creación y carga del código

### <a name="start-vs-code"></a>Inicio de VS Code

- Asegúrese de que el DevKit **no** está conectado al equipo.
- Inicie VS Code.
- Conecte el DevKit al equipo.

> [!NOTE]
> Es posible que, al iniciar VS Code, aparezca un mensaje de error en el que se informa de que no se puede encontrar el IDE de Arduino o un paquete de la placa relacionado. Si aparece este error, cierre VS Code e inicie de nuevo el IDE de Arduino. VS Code debería encontrar la ruta del IDE de Arduino correctamente.

### <a name="open-arduino-examples-folder"></a>Apertura de la carpeta de ejemplos de Arduino

Expanda la sección de la izquierda **ARDUINO EXAMPLES** (EJEMPLOS DE ARDUINO), vaya a **Examples for MXCHIP AZ3166 > AzureIoT** (Ejemplos de MXCHIP AZ3166 > AzureIoT) y seleccione **DoorMonitor**. De este modo, se abrirá una nueva ventana VS Code con una carpeta de proyecto.

![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

> [!NOTE]
> También puede abrir el ejemplo desde la paleta de comandos. Utilice `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir la paleta de comandos, escriba **Arduino** y, a continuación, busque y seleccione **Arduino: ejemplos** .

### <a name="provision-azure-services"></a>Aprovisionamiento de los servicios de Azure

En la ventana de la solución, ejecute la tarea de aprovisionamiento de la nube:
- Escriba `Ctrl+P` (macOS: `Cmd+P`).
- Escriba `task cloud-provision` en el cuadro de texto proporcionado.

En el terminal de VS Code, una línea de comandos interactiva le guiará durante el proceso de aprovisionamiento de los servicios de Azure correspondientes. Cuando aparezca la lista, seleccione los mismos elementos que aprovisionó anteriormente en [Implementación de IoT Hub en Azure](#deploy-iot-hub-in-azure).

![Aprovisionamiento de la nube](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Si la página se bloquea en el estado de carga al intentar iniciar sesión en Azure, consulte las [preguntas más frecuentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) para resolver el problema. 

### <a name="build-and-upload-the-device-code"></a>Creación y carga del código del dispositivo

#### <a name="windows"></a>Windows

1. Use `Ctrl+P` para ejecutar `task device-upload`.
2. El terminal le pide que especifique el modo de configuración. Para ello, mantenga presionado el botón A, y presione y suelte el botón de restablecimiento. En la pantalla, se muestra el número de identificación de DevKit y la palabra *Configuration* (Configuración).

Este procedimiento establece la cadena de conexión que se obtuvo en el paso [Aprovisionamiento de los servicios de Azure](#provision-azure-services).

A continuación, VS Code comenzará a verificar y a cargar el boceto de Arduino en DevKit:

![carga-dispositivo](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

El DevKit se reinicia y comienza a ejecutar el código.

> [!NOTE]
> En ocasiones, puede aparecer un mensaje de error similar al siguiente "Error: AZ3166: paquete desconocido". Este error se produce cuando el índice del paquete de la placa no se actualiza correctamente. Para resolverlo, consulte las [preguntas más frecuentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

#### <a name="macos"></a>macOS

1. Ponga el DevKit en modo de configuración: mantenga presionado el botón A y presione y suelte el botón de restablecimiento. La pantalla muestra "Configuration" (Configuración).
2. Use `Cmd+P` para ejecutar `task device-upload`.

Este procedimiento establece la cadena de conexión que se obtuvo en el paso [Aprovisionamiento de los servicios de Azure](#provision-azure-services).

A continuación, VS Code comenzará a verificar y a cargar el boceto de Arduino en DevKit:

![carga-dispositivo](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

El DevKit se reinicia y comienza a ejecutar el código.

> [!NOTE]
> En ocasiones, puede aparecer un mensaje de error similar al siguiente "Error: AZ3166: paquete desconocido". Este error se produce cuando el índice del paquete de la placa no se actualiza correctamente. Para resolverlo, consulte las [preguntas más frecuentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Prueba del proyecto

El programa se inicializa por primera vez cuando DevKit está en presencia de un campo magnético estable.

Una vez que se ha inicializado, aparece `Door closed` en la pantalla. Cuando se produce un cambio en el campo magnético, el estado pasa a `Door opened`. Cada vez que la puerta cambie de estado, recibirá una notificación por correo electrónico (puede tardar hasta cinco minutos en llegar).

![El imán está cerca del sensor: puerta cerrada](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "El imán está cerca del sensor: puerta cerrada")

![El imán se separa del sensor: puerta abierta](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "El imán se separa del sensor: puerta abierta")

## <a name="problems-and-feedback"></a>Problemas y comentarios

Si tiene algún problema, consulte las [preguntas más frecuentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o póngase en contacto con nosotros a través de los siguientes canales:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Pasos siguientes

Ha aprendido a conectar un dispositivo de DevKit al acelerador de la solución de supervisión remota de Azure IoT y a utilizar el servicio SendGrid para enviar un correo electrónico. Estos son los siguientes pasos que le sugerimos:

* [Información general sobre el acelerador de la solución de supervisión remota de Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Conectar un dispositivo MXChip IoT DevKit a una aplicación de Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
