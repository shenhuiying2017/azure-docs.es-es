---
title: Creación y administración de reglas de evento en una aplicación de Azure IoT Central | Microsoft Docs
description: Las reglas de eventos de Azure IoT Central le permiten supervisar los dispositivos casi en tiempo real e invocar automáticamente acciones, como el envío de correo electrónico, cuando la regla se desencadena.
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 932b1906b767ee7676f46ffd7242ad3d478d41c2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201598"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Creación de una regla de evento y configuración de las notificaciones en la aplicación de Azure IoT Central

Puede usar Microsoft Azure IoT Central para supervisar de forma remota los dispositivos conectados. Las reglas de Azure IoT Central le permiten supervisar los dispositivos casi en tiempo real e invocar automáticamente acciones, como el envío de correo electrónico, cuando la regla se desencadena. En unos pocos clics, puede definir la condición para supervisar los datos del dispositivo y configurar la acción que se va a invocar. En este artículo se explican con detalle las reglas de supervisión de eventos.

Azure IoT Central usa la [medida de eventos](howto-set-up-template.md) para capturar los datos del dispositivo. Cada tipo de medida tiene atributos claves que definen la medida. Puede crear reglas para supervisar cada tipo de medida de dispositivos y generar alertas cuando la regla se desencadena. Una regla de evento se desencadena cuando se notifica el evento de dispositivo seleccionado por el dispositivo.

## <a name="create-an-event-rule"></a>Creación de una regla de eventos

En esta sección se muestra cómo crear una regla de eventos. Se utiliza para ello utiliza un dispositivo expendedor con refrigeración que notifica un evento de error del motor del ventilador. La regla supervisa el evento notificado por el dispositivo y envía un correo electrónico cada vez que se notifica el evento.

1. Navegue a la página de detalles del dispositivo para el dispositivo al que va a agregar la regla.

1. Si aún no ha creado ninguna regla, consulte la siguiente pantalla:

    ![No hay ninguna regla todavía](media\howto-create-event-rules\image1.png)

1. En la pestaña **Reglas**, elija **Nueva regla** para ver los tipos de reglas que puede crear.

    ![Tipos de regla](media\howto-create-event-rules\image2.png)

1. Haga clic en **Evento** para abrir el formulario de creación de la regla.

    ![Regla de eventos](media\howto-create-event-rules\image3.png)

1. Elija un nombre que le ayude a identificar la regla en esta plantilla de dispositivo.

1. Para habilitar inmediatamente la regla para todos los dispositivos creados a partir esta plantilla, cambie el valor de **Habilitar regla**.

### <a name="configure-the-rule-condition"></a>Configuración de la condición de regla

En esta sección se muestra cómo agregar una condición para supervisar la medición del eventos de error del motor del ventilador.

1. Seleccione **+** junto a **Condición**.

1. Elija la medición de eventos en la lista desplegable que desea supervisar. En este ejemplo, se ha seleccionado el evento **Fan Motor Error** (Error del motor del ventilador).

1. Opcionalmente, también puede proporcionar un valor en caso de que desee supervisar un valor específico del evento sobre el que informa el dispositivo. Por ejemplo, si el dispositivo informa sobre el mismo evento con diferentes códigos de error, el hecho de proporcionar el código de error como un valor en la condición de la regla garantizará que la regla se desencadene solo cuando el dispositivo envíe ese valor específico como la carga del evento. Si lo deja en blanco, la regla se desencadenará cada vez que el dispositivo envíe el evento, independientemente del valor de este.

    ![Adición de condición de evento](media\howto-create-event-rules\image4.png)

    > [!NOTE]
    > Debe seleccionar al menos una medición de eventos al definir una condición de regla de evento.

### <a name="configure-the-action"></a>Configuración de la acción

En esta sección se muestra cómo especificar lo que hace la regla cuando se cumple la condición mediante la adición de una acción.

1. Seleccione **+** junto a **Acciones**. Aquí puede ver la lista de acciones disponibles. Durante la versión preliminar pública, **Correo electrónico** es la única acción admitida.

    ![Adición de acción](media\howto-create-event-rules\image5.png)

1. Elija la acción **Correo electrónico**, escriba una dirección de correo electrónico válida en el campo **Para** y proporcione una nota que aparecerá en el cuerpo del correo electrónico cuando la regla se desencadene.

    > [!NOTE]
    > Solo se envían mensajes de correo electrónico a los usuarios que se han agregado a la aplicación y han iniciado sesión al menos una vez. Obtenga más información sobre la [administración de usuarios](howto-administer.md) en Azure IoT Central.

   ![Configuración de acción](media\howto-create-event-rules\image6.png)

1. Para guardar la regla, elija **Guardar**. La regla está activa en unos minutos e inicia la supervisión de los eventos que se envían a la aplicación. Si coincide con la condición especificada en la regla, la regla desencadena la acción de correo electrónico configurada.

## <a name="parameterize-the-rule"></a>Parametrización de la regla

Las acciones también pueden configurarse mediante la **Propiedad de dispositivo** como un parámetro. Si una dirección de correo electrónico se almacena como una propiedad de dispositivo, se puede utilizar cuando se define la dirección **Para**.

## <a name="delete-a-rule"></a>Eliminar una regla

Si ya no necesita una regla, elimínela; para ello, abra la regla y seleccione **Eliminar**. Al eliminar la regla, se quita de la plantilla de dispositivos y todos los dispositivos asociados.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Habilitación o deshabilitación de una regla para una plantilla de dispositivo

Navegue hasta el dispositivo y elija la regla que desea habilitar o deshabilitar. El hecho de activar o desactivar el botón **Enable rule for all devices of this template** (Habilitar regla para todos los dispositivos de esta plantilla) en la regla habilita o deshabilita la regla para todos los dispositivos asociados a la plantilla de dispositivo.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Habilitación o deshabilitación de una regla para un dispositivo

Navegue hasta el dispositivo y elija la regla que desea habilitar o deshabilitar. Active o desactive el botón **Enable rule for this device** (Habilitar regla para este dispositivo) para habilitar o deshabilitar la regla para ese dispositivo.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a crear reglas en una aplicación de Azure IoT Central, le sugerimos el paso siguiente:

> [!div class="nextstepaction"]
> [How to manage your devices](howto-manage-devices.md) (Administración de los dispositivos).