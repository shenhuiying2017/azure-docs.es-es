---
title: Descripción del control de versiones de las plantillas de dispositivo para las aplicaciones de Azure IoT Central | Microsoft Docs
description: Iteración sobre las plantillas de dispositivo mediante la creación de nuevas versiones y sin afectar a los dispositivos conectados en vivo
services: iot-central
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/19/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 3a399b6b54f8da9c48b4bdfe7e98a527262bb174
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201098"
---
# <a name="create-a-new-device-template-version"></a>Creación de una nueva versión de plantilla de dispositivo

Microsoft Azure IoT Central permite el desarrollo rápido de aplicaciones de IoT. Puede iterar rápidamente por los diseños de plantilla de dispositivo agregando, editando o eliminando las medidas, las configuraciones o las propiedades. Algunos de estos cambios podrían ser intrusivos para los dispositivos conectados actualmente. Azure IoT Central identifica estos cambios importantes y proporciona una manera de implementar de forma segura estas actualizaciones en los dispositivos.

Una plantilla de dispositivo tiene un número de versión al crearlo. De forma predeterminada, el número de versión es 1.0.0. Si edita una plantilla de dispositivo, y si ese cambio puede afectar a los dispositivos conectados en vivo, Azure IoT Central le pedirá que cree una nueva versión de la plantilla de dispositivo.

> [!NOTE]
> Para más información sobre cómo crear una plantilla de dispositivo, consulte [Set up a device template](howto-set-up-template.md) (Configurar una plantilla de dispositivo).

## <a name="changes-that-prompt-a-version-change"></a>Cambios que provocan un cambio de versión

Por lo general, los cambios en la configuración o las propiedades de la plantilla de dispositivo provocan un cambio de versión.

> [!NOTE]
> Los cambios realizados en la plantilla de dispositivo no solicitan la creación de una nueva versión cuando no hay ningún dispositivo conectado o hay un dispositivo conectado como máximo.

En la lista siguiente se describen las acciones del usuario que podrían requerir una nueva versión:

* Propiedades (obligatorias)
    * Incorporación o eliminación de una propiedad obligatoria
    * Cambio del nombre de campo de una propiedad, el nombre de campo que usan los dispositivos para enviar mensajes.
*  Propiedades (opcionales)
    * Eliminación de una propiedad opcional
    * Cambio del nombre de campo de una propiedad, el nombre de campo que usan los dispositivos para enviar mensajes.
    * Cambio de una propiedad opcional para una propiedad obligatoria
*  Settings
    * Incorporación o eliminación de una configuración
    * Cambio del nombre de campo de una configuración, el nombre de campo que usan los dispositivos para enviar y recibir mensajes.

## <a name="what-happens-on-version-change"></a>¿Qué ocurre al cambiar de versión?

¿Qué ocurre con las reglas y los paneles de dispositivo cuando se produce un cambio de versión?

Las **reglas** podrían contener condiciones que dependen de propiedades. Si ha quitado una o varias de estas propiedades, estas reglas podrían interrumpirse en la nueva versión de la plantilla de dispositivo. Puede ir a estas reglas específicas y actualizar las condiciones para corregir las reglas. Las reglas para la versión anterior deben funcionar sin ningún impacto.

Los **paneles de dispositivo** puede contener varios tipos de iconos. Algunos de los iconos pueden contener configuraciones y propiedades. Cuando se quita una propiedad o configuración usada en un icono, este queda interrumpido total o parcialmente. Puede ir al icono y corregir el problema quitando dicho icono o actualizando el contenido del mismo.

## <a name="migrate-a-device-across-device-template-versions"></a>Migración de un dispositivo a través de versiones de plantillas de dispositivo

Puede crear varias versiones de la plantilla de dispositivo. Con el tiempo, tendrá varios dispositivos conectados mediante estas plantillas de dispositivo. Puede migrar los dispositivos de una versión de la plantilla de dispositivo a otra. Los pasos siguientes describen cómo migrar un dispositivo:

1. Vaya a la página **Explorador**.
1. Seleccione el dispositivo que necesita migrar a otra versión.
1. Elija **Migrate Device** (Migrar dispositivo).
1. Seleccione el número de versión a la que desea migrar el dispositivo y elija **Migrar**.

![Migración de un dispositivo](media\howto-version-devicetemplate\pick-version.png)

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar las versiones de plantilla de un dispositivo en la aplicación de Azure IoT Central, este es el paso siguiente sugerido:

> [!div class="nextstepaction"]
> [Creación de reglas de telemetría](howto-create-telemetry-rules.md)