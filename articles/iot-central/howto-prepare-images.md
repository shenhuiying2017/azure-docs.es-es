---
title: Carga de imágenes a una aplicación de Azure IoT Central | Microsoft Docs
description: Como generador, aprenda a preparar y cargar imágenes para su aplicación de Azure IoT Central.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: a43f2dd780604235ada1d8e3ae8a20563042fbaa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200238"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Preparación y carga de imágenes a una aplicación de Azure IoT Central

En este artículo describe cómo puede, en tanto que generador, adaptar la aplicación Microsoft Azure IoT Central mediante la carga de imágenes personalizadas. Por ejemplo, puede personalizar un panel de dispositivo con una imagen del dispositivo.

## <a name="before-you-begin"></a>Antes de empezar

Necesitará lo siguiente para completar los pasos de este artículo:

1. Una aplicación de Azure IoT Central. Para más información, consulte [Create your Azure IoT Central Application](howto-create-application.md) (Creación de una aplicación de Azure IoT Central).
1. Una herramienta para ajustar la escala y el tamaño de los archivos de imágenes.

## <a name="choose-where-to-use-custom-images"></a>Elija dónde quiere utilizar imágenes personalizadas.

Puede agregar imágenes personalizadas a las siguientes ubicaciones en una aplicación de Azure IoT Central:

* La página **Application Manager** (Administrador de aplicaciones)

    ![Imagen en la página del administrador de aplicaciones](media/howto-prepare-images/applicationmanager.png)

* La página principal

    ![Imagen en la página principal](media/howto-prepare-images/homepage.png)

* Una plantilla de dispositivo

    ![Imagen en una plantilla de dispositivo](media/howto-prepare-images/devicetemplate.png)

* Un icono en un panel de dispositivo

    ![Imagen en un icono de dispositivo](media/howto-prepare-images/devicetile.png)

* Un icono en el panel de un conjunto de dispositivos

    ![Imagen en un icono de conjunto de dispositivos](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Preparación de las imágenes

En las cuatro ubicaciones, puede usar imágenes PNG, GIF o JPEG.

En la tabla siguiente se resumen los tamaños de imagen que puede usar:

| Ubicación | Tamaños |
| -------- | ------ |
| **Administrador de aplicaciones** | 268x160 px |
| Plantilla de dispositivo | 64x64 px |
| Página principal e iconos del panel | El icono de tamaño más pequeño es 200 x 200 px, los iconos más grandes pueden ser múltiplos cuadrados o rectangulares de los iconos pequeños. Por ejemplo, 200 x 400 px, 400 x 200 px o 400 x 400 px. |

Para obtener la mejor visualización en la aplicación, debe crear imágenes que coincidan con las dimensiones que se muestran en la tabla anterior.

## <a name="upload-the-images"></a>Carga de las imágenes

En las secciones siguientes se describe cómo cargar las imágenes que se utilizan en las diferentes ubicaciones:

### <a name="application-manager"></a>Administrador de aplicaciones

Para cargar una imagen que se usará en el **Administrador de aplicaciones**, navegue hasta la página **Configuración de la aplicación** en la sección **Administración**. Debe ser un administrador para completar esta tarea:

![Carga de imagen en la aplicación](media/howto-prepare-images/uploadapplicationmanager.png)

Haga clic en la imagen de carga y, a continuación, elija el archivo para cargarlo desde el equipo local.

### <a name="home-page"></a>Página de inicio

Para cargar una imagen que se usará en la página principal, desplácese hasta la **página principal** y active el modo de diseño. Debe ser un generador para completar esta tarea:

![Carga de imagen de la página principal](media/howto-prepare-images/uploadhomepage.png)

Haga clic en la imagen de carga y, a continuación, elija el archivo para cargarlo desde el equipo local.

Después de que se cargue la imagen, puede cambiar su tamaño mientras esté activado el modo de diseño.

### <a name="device-template"></a>Plantilla de dispositivo

Para cargar una imagen que se usará en una plantilla de dispositivo, vaya a **Device Explorer**, elija la plantilla de dispositivo y luego un dispositivo y active el modo de diseño. Debe ser un generador para completar esta tarea:

![Carga de la imagen de plantilla de dispositivo](media/howto-prepare-images/uploaddevicetemplate.png)

Haga clic en la imagen de carga y, a continuación, elija el archivo para cargarlo desde el equipo local.

### <a name="device-dashboard"></a>Panel del dispositivo

Para cargar una imagen que se usará en un panel de dispositivo, vaya a **Device Explorer** y elija la plantilla de dispositivo y luego un dispositivo. A continuación, elija la página **Panel** y active el modo de diseño. Debe ser un generador para completar esta tarea:

![Carga de imagen del panel de dispositivo](media/howto-prepare-images/uploaddevicedashboard.png)

Haga clic en la imagen de carga y, a continuación, elija el archivo para cargarlo desde el equipo local.

Después de que se cargue la imagen, puede cambiar su tamaño y recolocarla mientras esté activado el **modo de diseño**.

### <a name="device-set-dashboard"></a>Panel de conjunto de dispositivos

Para cargar una imagen que se usará en un panel de conjunto de dispositivos, vaya a los **Conjuntos de dispositivos** y elija el conjunto de dispositivos, y luego un dispositivo. A continuación, elija la página **Panel** y active el **modo de diseño**:

![Carga de imagen del panel de conjunto de dispositivos](media/howto-prepare-images/uploaddevicesetdashboard.png)

Haga clic en la imagen de carga y, a continuación, elija el archivo para cargarlo desde el equipo local.

Después de que se cargue la imagen, puede cambiar su tamaño y recolocarla mientras esté activado el modo de diseño.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a preparar y cargar imágenes en la aplicación de Azure IoT Central, le sugerimos que siga con lo siguiente:

> [!div class="nextstepaction"]
> [Manage devices in your Azure IoT Central application](howto-manage-devices.md) (Administración de dispositivos en la aplicación de Azure IoT Central)