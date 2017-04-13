---
title: "Conexión de Arduino a Azure IoT: Lección 2: Herramientas de Azure (macOS) | Microsoft Docs"
description: "Instale Python la interfaz de la línea de comandos de Azure (CLI de Azure) en Mac OS."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: CLI de Azure, servicio en la nube de IoT, Arduino en la nube
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: 9b719293-01d2-4a2d-9c49-476e67f4816d
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: ad8ee9d524dcb096ac7db4bb23ec16d56518742b
ms.lasthandoff: 01/24/2017


---
# <a name="get-azure-tools-macos-1010"></a>Obtención de las herramientas de Azure (macOS 10.10)

> [!div class="op_single_selector"]
> * [Windows 7 o posterior][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>Lo que hará

Instalación de la interfaz de la línea de comandos de Azure (CLI de Azure) Si tiene problemas, busque soluciones en [esta página](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md) para la placa Adafruit Feather M0 WiFi Arduino.

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá lo siguiente:
* Cómo instalar la CLI de Azure.
* Cómo agregar un subgrupo de IoT de la CLI de Azure.

## <a name="what-you-need"></a>Lo que necesita
* Un Mac con conexión a Internet.
* Una suscripción de Azure activa. Si no tiene ninguna cuenta de Azure, puede crear una [cuenta de evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/) en solo unos minutos.

## <a name="install-python"></a>Instalación de Python
Aunque macOS incluye Python 2.7 de forma predetermina, se recomienda instalar Python mediante Homebrew. Consulte [Instalación de Python en Mac OS](http://docs.python-guide.org/en/latest/starting/install/osx/).

Instale Python y pip ejecutando el comando siguiente:

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>Instalación de la CLI de Azure
La CLI de Azure proporciona una experiencia de línea de comandos multiplataforma en Azure. De este modo, puede trabajar directamente desde la línea de comandos para aprovisionar y administrar los recursos.

Para instalar la CLI de Azure más recientr, siga estos pasos:

1. Ejecute los siguientes comandos en una ventana de terminal. Este proceso puede tardar cinco minutos en instalar la CLI de Azure.

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
2. Compruebe la instalación ejecutando el comando siguiente:

   ```bash
   az iot -h
   ```

Si la instalación se realiza correctamente, verá el siguiente resultado.

![Resultado que indica una instalación correcta][output]

## <a name="summary"></a>Resumen
Ha instalado la CLI de Azure. La siguiente tarea consiste en crear la identidad de Azure IoT Hub y de dispositivos mediante la CLI de Azure.

## <a name="next-steps"></a>Pasos siguientes
[Creación de la instancia de IoT Hub y registro de Raspberry Pi][create-your-iot-hub-and-register-your-arduino-board]


<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-mac.md
[output]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson2/az_iot_help_osx.png
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
