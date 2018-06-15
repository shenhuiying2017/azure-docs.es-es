---
title: Creación y administración de reglas de telemetría en una aplicación de Azure IoT Central | Microsoft Docs
description: Las reglas de telemetría de Azure IoT Central le permiten supervisar los dispositivos casi en tiempo real e invocar automáticamente acciones, como el envío de correo electrónico, cuando la regla se desencadena.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: caca4e9db898b3766995fde8c5eebd4767abd85b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629820"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Creación de una regla de telemetría y configuración de las notificaciones en la aplicación de Azure IoT Central

Puede usar Microsoft Azure IoT Central para supervisar de forma remota los dispositivos conectados. Las reglas de Azure IoT Central le permiten supervisar los dispositivos casi en tiempo real e invocar automáticamente acciones, como el envío de correo electrónico, cuando la regla se desencadena. En unos pocos clics, puede definir la condición para supervisar los datos del dispositivo y configurar la acción que se va a invocar. En este artículo se explican con detalle las reglas de telemetría.

Azure IoT Central usa la [medida de telemetría](howto-set-up-template.md) para capturar los datos del dispositivo. Cada tipo de medida tiene atributos claves que definen la medida. Puede crear reglas para supervisar cada tipo de medida de dispositivos y generar alertas cuando la regla se desencadena. Una regla de telemetría se desencadena cuando la telemetría del dispositivo seleccionado supera un umbral especificado.

## <a name="create-a-telemetry-rule"></a>Creación de una regla de telemetría

En esta sección se muestra cómo crear una regla de telemetría. En este ejemplo se utiliza un dispositivo de aire acondicionado conectado que envía telemetría de temperatura y humedad. La regla supervisa la temperatura notificada por el dispositivo y envía un correo electrónico cada vez que sube de 80 grados.

1. Navegue a la página de detalles del dispositivo para el dispositivo al que va a agregar la regla.

1. Si aún no ha creado ninguna regla, consulte la siguiente pantalla:

    ![No hay ninguna regla todavía](media\howto-create-telemetry-rules\image1.png)

1. En la pestaña **Reglas**, elija **+Nueva regla** para ver los tipos de reglas que puede crear.

    ![Tipos de regla](media\howto-create-telemetry-rules\image2.png)

1. Elija el icono **Telemetría** para abrir el formulario para crear la regla.

    ![Regla de telemetría](media\howto-create-telemetry-rules\image3.png)

1. Elija un nombre que le ayude a identificar la regla en esta plantilla de dispositivo.

1. Para habilitar inmediatamente la regla para todos los dispositivos creados a partir esta plantilla, cambie el valor de **Habilitar regla**.

### <a name="configure-the-rule-condition"></a>Configuración de la condición de regla

En esta sección se muestra cómo agregar una condición para supervisar la telemetría de temperatura.

1. Seleccione **+** junto a **Condición**.

1. En la lista desplegable, elija el tipo de telemetría **Temperatura**. A continuación, seleccione el operador y proporcione un valor umbral. Puede agregar varias condiciones de telemetría. Cuando se especifican varias condiciones, deben cumplirse todas ellas para que la regla se desencadene.

    ![Adición de condición de telemetría](media\howto-create-telemetry-rules\image4.png)

    > [!NOTE]
    > Seleccione al menos una medida de telemetría cuando defina una condición de regla de telemetría.

### <a name="configure-the-action"></a>Configuración de la acción

En esta sección se muestra cómo especificar lo que hace la regla cuando se cumple la condición mediante la adición de una acción.

1. Seleccione **+** junto a **Acciones**. Aquí puede ver la lista de acciones disponibles. Durante la versión preliminar pública, **Correo electrónico** es la única acción admitida.

    ![Adición de acción](media\howto-create-telemetry-rules\image5.png)

1. Elija la acción **Correo electrónico**, escriba una dirección de correo electrónico válida en el campo **Para** y proporcione una nota que aparecerá en el cuerpo del correo electrónico cuando la regla se desencadene.

    > [!NOTE]
    > Solo se envían mensajes de correo electrónico a los usuarios que se han agregado a la aplicación y han iniciado sesión al menos una vez. Obtenga más información sobre la [administración de usuarios](howto-administer.md) en Azure IoT Central.

   ![Configuración de acción](media\howto-create-telemetry-rules\image6.png)

1. Para guardar la regla, elija **Guardar**. La regla está activa en unos minutos e inicia la supervisión de telemetría que se envía a la aplicación. Si coincide con la condición especificada en la regla, la regla desencadena la acción de correo electrónico configurada.

## <a name="parameterize-the-rule"></a>Parametrización de la regla

Las reglas pueden derivar ciertos valores de las **propiedades del dispositivo**  como parámetros. El uso de parámetros es útil en aquellos escenarios en los que los umbrales de telemetría varían para diferentes dispositivos. Cuando cree la regla, elija una propiedad de dispositivo que especifique el umbral, como **umbral ideal máximo**, en lugar de proporcionar un valor absoluto, como 80 grados. Cuando la regla se ejecuta, hace coincidir la telemetría del dispositivo con el valor proporcionado en la propiedad del dispositivo.

El uso de parámetros es una forma eficaz de reducir el número de reglas que va a administrar la plantilla de dispositivo.

Las acciones también pueden configurarse mediante la **Propiedad de dispositivo** como un parámetro. Si una dirección de correo electrónico se almacena como una propiedad de dispositivo, se puede utilizar cuando se define la dirección **Para**.

## <a name="delete-a-rule"></a>Eliminar una regla

Si ya no necesita una regla, elimínela; para ello, abra la regla y seleccione **Eliminar**. Al eliminar la regla, se quita de la plantilla de dispositivos y todos los dispositivos asociados.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Habilitación o deshabilitación de una regla para una plantilla de dispositivo

Navegue hasta el dispositivo y elija la regla que desea habilitar o deshabilitar. El hecho de activar o desactivar el botón **Enable rule for all devices of this template** (Habilitar regla para todos los dispositivos de esta plantilla) en la regla habilita o deshabilita la regla para todos los dispositivos asociados a la plantilla de dispositivo.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Habilitación o deshabilitación de una regla para un dispositivo

Navegue hasta el dispositivo y elija la regla que desea habilitar o deshabilitar. Active o desactive el botón **Enable rule for this device** (Habilitar regla para este dispositivo) para habilitar o deshabilitar la regla para ese dispositivo.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a editar reglas en una aplicación de Azure IoT Central, le sugerimos el paso siguiente:

> [!div class="nextstepaction"]
> [How to manage your devices](howto-manage-devices.md) (Administración de los dispositivos).