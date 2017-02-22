---
title: "Conexión de Intel Edison (C) a Azure IoT: Lección 2: Herramientas de Azure (Windows) | Microsoft Docs"
description: "Instale Python y la interfaz de la línea de comandos de Azure (CLI de Azure) en Windows 7 y versiones posteriores."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: CLI de Azure, servicio en la nube de IoT, Arduino en la nube
ms.assetid: 1035760e-cdd1-4d99-8003-067e98b29762
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: c31847b0a4b630f999f64c139f6b9b44623d847e


---
# <a name="get-azure-tools-windows-7-and-later"></a>Obtención de las herramientas de Azure (Windows 7 o posterior)
> [!div class="op_single_selector"]
> * [Windows 7 y versiones posteriores][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>Lo que hará
Instale Python y la interfaz de la línea de comandos de Azure (CLI de Azure). Si tiene problemas, busque soluciones en [esta página][troubleshooting].

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá lo siguiente:
* Cómo instalar Python.
* Cómo instalar la CLI de Azure.

## <a name="what-you-need"></a>Lo que necesita
* Un equipo Windows con conexión a Internet.
* Una suscripción de Azure activa. Si no tiene ninguna cuenta de Azure, cree una [cuenta de evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/) en solo unos minutos.

## <a name="install-python"></a>Instalación de Python
[Instale Python](https://www.python.org/downloads/) en el equipo Windows. Puede instalar Python 2.7, 3.4 o 3.5. Este tutorial se basa en Python 2.7. Si ya ha instalado Python, vaya a la sección siguiente e instale la CLI de Azure.

También debe agregar la ruta de acceso de las carpetas donde se instalan python.exe y pip.exe a la variable de entorno `PATH` del sistema. De forma predeterminada, se instala python.exe en `C:\Python27` y pip.exe en `C:\Python27\Scripts`.

## <a name="install-the-azure-cli"></a>Instalación de la CLI de Azure
La CLI de Azure proporciona una experiencia de línea de comandos multiplataforma en Azure. De este modo, puede trabajar directamente desde la línea de comandos para aprovisionar y administrar los recursos.

Para instalar la CLI de Azure, siga estos pasos:

1. Abra una ventana de símbolo del sistema como administrador.
2. Ejecute los comandos siguientes:

   ```cmd
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
3. Compruebe la instalación ejecutando el comando siguiente:

   ```cmd
   az iot -h
   ```

Si la instalación se realiza correctamente, verá el siguiente resultado.

![Resultado que indica una instalación correcta](media/iot-hub-intel-edison-lessons/lesson2/az_iot_help_win.png)

## <a name="summary"></a>Resumen
Ha instalado la CLI de Azure. La siguiente tarea consiste en crear la identidad de Azure IoT Hub y de dispositivos mediante la CLI de Azure.

## <a name="next-steps"></a>Pasos siguientes
[Creación de una instancia de IoT Hub y registro de Intel Edison][create-your-iot-hub-and-register-intel-edison]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[create-your-iot-hub-and-register-intel-edison]: iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md
[windows]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-mac.md



<!--HONumber=Jan17_HO4-->


