---
title: Traductor IoT DevKit con Azure Functions y Cognitive Services | Microsoft Docs
description: Utilice el micrófono de IoT DevKit para recibir un mensaje de voz y Azure Cognitive Services para procesarlo en texto traducido al inglés.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hube
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: 20e5a5f4fb381dedc42d698464819a6098c3579b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="use-iot-devkit-az3166-with-azure-function-and-cognitive-services-to-make-a-language-translator"></a>Uso de IoT DevKit AZ3166 con Azure Functions y Cognitive Services para crear un traductor de idiomas

En este artículo, aprenderá a convertir IoT DevKit en un traductor de idiomas mediante [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Registra la voz y la traduce a texto en inglés que se muestra en la pantalla de DevKit.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) es una placa integral compatible de Arduino con periféricos y sensores varios. Se puede desarrollar para ella con la [extensión de Visual Studio Code para Arduino](https://aka.ms/arduino). Incluye un [catálogo de proyectos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) cada vez mayor que sirve de guía para crear prototipos de soluciones de Internet de las cosas (IoT) que aprovechen los servicios de Microsoft Azure.

## <a name="what-you-need"></a>Lo que necesita

Siga la [Guía de introducción](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Conexión de DevKit con Wi-Fi
* Preparación del entorno de desarrollo

Una suscripción de Azure activa. Si no tiene una, puede registrarse a través de uno de estos dos métodos:

* Active una [cuenta de Microsoft Azure de prueba de 30 días gratis](https://azure.microsoft.com/free/)
* Reclame su [crédito de Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) si es suscriptor de MSDN o Visual Studio

## <a name="step-1-open-the-project-folder"></a>Paso 1. Apertura de la carpeta del proyecto

### <a name="a-start-vs-code"></a>A. Inicio de VS Code

- Asegúrese de que el DevKit no está conectado al equipo.
- Inicio de VS Code
- Conecte el DevKit al equipo.

### <a name="b-open-the-arduino-examples-folder"></a>B. Apertura de la carpeta de ejemplos de Arduino

Expanda la sección de la izquierda **EJEMPLOS DE ARDUINO > Ejemplos para MXCHIP AZ3166 > AzureIoT** y seleccione **DevKitTranslator**. Se abre una nueva ventana de VS Code que contiene la carpeta de proyecto DEVKITTRANSLATOR.

![Ejemplos de IoT DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

> [!NOTE]
> También puede abrir el ejemplo desde la paleta de comandos. Utilice `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir la paleta de comandos, escriba **Arduino** y, a continuación, busque y seleccione **Arduino: ejemplos** .

## <a name="step-2-provision-azure-services"></a>Paso 2. Aprovisionamiento de los servicios de Azure

En la ventana de la solución, escriba `Ctrl+P` (macOS: `Cmd+P`) e introduzca `task cloud-provision`.

En el terminal de VS Code, una línea de comandos interactiva le guiará para aprovisionar todos los servicios de Azure necesarios:

![Tarea de aprovisionamiento en la nube](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="step-3-deploy-azure-functions"></a>Paso 3. Implementación de Azure Functions

Use `Ctrl+P` (macOS: `Cmd+P`) para ejecutar `task cloud-deploy` para implementar el código de Azure Functions. Normalmente, este proceso tarda entre 2 y 5 minutos en completarse:

![Tarea de implementación en la nube](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Después de que Azure Functions se implemente correctamente, rellene el archivo azure_config.h con el nombre de la aplicación de función. Puede ir a [Azure Portal](https://portal.azure.com/) para encontrarlo:

![Búsqueda del nombre de la aplicación de Azure Functions](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Si Azure Functions no funciona correctamente, consulte la sección de [preguntas más frecuentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) para resolver el problema.

## <a name="step-4-build-and-upload-the-device-code"></a>Paso 4 Creación y carga del código del dispositivo

1. Use `Ctrl+P` (macOS: `Cmd+P`) para ejecutar `task config-device-connection`.

2. El terminal le preguntará si desea usar la cadena de conexión que se recupera en el paso `task cloud-provision`. También puede introducir su propia cadena de conexión de dispositivo, seleccionado **Create New...** (Crear nuevo...).

3. El terminal le pide que especifique el modo de configuración. Para ello, mantenga presionado el botón A, y presione y suelte el botón de restablecimiento. La pantalla muestra el identificador de DevKit y "Configuration" (Configuración).
  ![Comprobación y carga del boceto de Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. Después de que `task config-device-connection` termine, haga clic en `F1` para cargar los comandos VS Code y seleccione `Arduino: Upload`, a continuación, VS Code inicia la comprobación y la carga del boceto de Arduino: ![Comprobación y carga del boceto de Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

El DevKit se reinicia y comienza a ejecutar el código.

## <a name="test-the-project"></a>Prueba del proyecto

Después de la inicialización de la aplicación, siga las instrucciones en la pantalla de DevKit. El idioma de origen predeterminado es el chino.

Para seleccionar otro idioma para traducir:

1. Presione el botón A para acceder al modo de configuración.
2. Presione el botón B para desplazarse por todos los idiomas origen admitidos.
3. Presione el botón A para confirmar la elección de idioma de origen.
4. Mantenga pulsado el botón B mientras habla y suéltelo para iniciar la traducción.
5. El texto traducido al inglés se mostrará en la pantalla.

![Selección de idioma](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Resultado de la traducción](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

En la pantalla de resultados de traducción, puede:

- Presionar los botones A y B para desplazarse y seleccionar el idioma de origen.
- Presionar el botón B para hablar y soltarlo para enviar la voz y obtener el texto traducido.

## <a name="how-it-works"></a>Cómo funciona

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

El boceto de Arduino graba su voz y, a continuación, envía una solicitud HTTP para activar Azure Functions. Azure Functions llama a la API del traductor de voz de la instancia de Cognitive Services para realizar la traducción. Cuando Azure Functions recibe el texto de traducción, envía un mensaje C2D al dispositivo. A continuación, la traducción se mostrará en la pantalla.

## <a name="change-device-id"></a>Cambio del identificador del dispositivo

El identificador de dispositivo predeterminado registrado en Azure IoT Hub es **AZ3166**. Si desea modificarlo, siga [estas instrucciones](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problemas y comentarios

Si tiene algún problema, acuda a [P+F](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), o también puede ponerse en contacto con nosotros a través de los siguientes canales:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Pasos siguientes

Ahora puede convertir a IoT DevKit en un traductor con Azure Functions y Cognitive Services. En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Uso de una tarea de Visual Studio Code para automatizar aprovisionamientos en la nube
> * Configuración de la cadena de conexión del dispositivo IoT de Azure
> * Implementación de una instancia de Azure Functions
> * Prueba de la traducción de mensajes de voz

Consulte los otros tutoriales para obtener información sobre:

> [!div class="nextstepaction"]
> [Conexión de IoT DevKit AZ3166 al acelerador de la solución de supervisión remota de Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
