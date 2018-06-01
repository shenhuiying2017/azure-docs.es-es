---
title: Administración de dispositivos en una aplicación de Azure IoT Central | Microsoft Docs
description: Como operador, aprenda a administrar dispositivos en la aplicación de Azure IoT Central.
services: iot-central
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 75472d701160e7cfd331d01efcdc1a19ae20fb2d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303586"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Administración de dispositivos en la aplicación de Azure IoT Central

En este artículo se describe cómo administrar dispositivos en su aplicación de Microsoft Azure IoT Central como operador. Como operador, puede hacer lo siguiente:

- Usar la página **Explorer** para ver, agregar y eliminar los dispositivos conectados a la aplicación de Azure IoT Central.
- Mantener un inventario actualizado de los dispositivos.
- Mantener actualizados los metadatos del dispositivo cambiando los valores almacenados en las propiedades del dispositivo.
- Controlar el comportamiento de los dispositivos mediante la actualización de una configuración en un dispositivo específico desde la página **Configuración**.

## <a name="view-your-devices"></a>Visualización de los dispositivos

Para ver un dispositivo determinado:

1. Elija **Explorer** en el menú de navegación de la izquierda. Aquí verá una lista de las [plantillas de dispositivo](howto-set-up-template.md).

1. Elija una **Plantilla de dispositivo** en el panel izquierdo.

1. En el panel derecho, verá una lista de dispositivos creados a partir de esa plantilla de dispositivo. Elija un dispositivo determinado para ver la página **Detalles del dispositivo** para dicho dispositivo:

    [![Página Detalles del dispositivo](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>Agregar un dispositivo

Para agregar un dispositivo real a una aplicación de Azure IoT Central:

1. Elija **Explorer** en el menú de navegación de la izquierda.

1. Elija la plantilla de dispositivo desde la que va a crear un dispositivo.

1. Elija + **Nuevo**.

1. Elija **Real** o **Simulado**. Un dispositivo real hace referencia a un dispositivo físico que se conecta a la aplicación de Azure IoT Central. Un dispositivo simulado tiene datos de ejemplo generados por Azure IoT Central, que los pone a su disposición. En este ejemplo se utiliza un dispositivo real. Elija **Real** para navegar hasta la página **Detalles del dispositivo** para el dispositivo nuevo.


## <a name="bulk-import-devices"></a>Importación en bloque de dispositivos

Para conectar un gran número de dispositivos a la aplicación, Azure IoT Central ofrece importar dispositivos en bloque a través de un archivo CSV. 

Requisitos del archivo CSV:
1. El archivo CSV debe tener una única columna que contenga los identificadores de dispositivo.

1. El archivo no debe tener ningún encabezado.


Para registrar dispositivos en la aplicación en bloque:

1. Elija **Explorer** en el menú de navegación de la izquierda.

1. En el panel izquierdo, elija la plantilla de dispositivo para la que desea crear dispositivos en bloque.

1. Elija **Nuevo** y seleccione **Importación en bloque**.

    [![Acción de importación en bloque](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Seleccione el archivo CSV que tiene la lista de identificadores de dispositivo que desea importar.

1. La importación de dispositivos se inicia una vez que se ha cargado el archivo. Puede realizar un seguimiento del estado de la importación en la parte superior de la cuadrícula de dispositivo.

1. Una vez finalizada la importación, se muestra un mensaje de confirmación en la cuadrícula de dispositivos.

    [![Importación en bloque correcta](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Si se produce un error en la operación de importación de dispositivos, aparecerá un mensaje de error en la cuadrícula de dispositivos. Se genera un archivo de registro que captura todos los errores, que se puede descargar haciendo clic en el mensaje de error.



## <a name="delete-a-device"></a>Eliminar un dispositivo

Para eliminar ya sea un dispositivo real o simulado de la aplicación de Azure IoT Central:

1. Elija **Explorer** en el menú de navegación.

1. Elija la plantilla de dispositivo del dispositivo que desea eliminar.

1. Active la casilla situada junto al dispositivo que desea eliminar.

1. Elija **Eliminar**.

## <a name="change-a-device-setting"></a>Cambio de la configuración de un dispositivo

La configuración controla el comportamiento de un dispositivo. En otras palabras, le permite proporcionar entradas para el dispositivo. Puede ver y actualizar la configuración del dispositivo en la página **Detalles del dispositivo**.

1. Elija **Explorer** en el menú de navegación.

1. Elija la plantilla de dispositivo del dispositivo cuya configuración desea modificar.

1. Elija la pestaña **Configuración** . Aquí verá toda la configuración que tiene el dispositivo y sus valores actuales. Para cada valor, puede ver si el dispositivo se está sincronizando todavía.

1. Modifique la configuración a los valores deseados. Puede modificar varias opciones de configuración a la vez y actualizarlas todas al mismo tiempo.

1. Seleccione **Actualizar**. Los valores se envían al dispositivo. Cuando el dispositivo confirma el cambio de configuración, el estado de la configuración se cambia de nuevo a **synced** (sincronizado).

## <a name="change-a-property"></a>Cambio de una propiedad

Las propiedades son los metadatos de dispositivo asociados al dispositivo, como la ciudad y el número de serie. Puede ver y actualizar las propiedades en la página **Detalles del dispositivo**.

1. Elija **Explorer** en el menú de navegación.

1. Elija la plantilla de dispositivo del dispositivo cuyas propiedades desea modificar.

1. Elija la pestaña **Propiedades**, donde verá todas las propiedades.

1. Modifique las propiedades a los valores deseados. Puede modificar varias propiedades a la vez y actualizarlas todas al mismo tiempo.

1. Seleccione **Actualizar**.

> [!NOTE]
> No se puede cambiar el valor de las _propiedades del dispositivo_. Las propiedades del dispositivo se establecen con el dispositivo y son de solo lectura en la aplicación de Azure IoT Central.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a administrar dispositivos en la aplicación de Azure IoT Central, le sugerimos el paso siguiente:

> [!div class="nextstepaction"]
> [How to use device sets](howto-use-device-sets.md) (Uso de conjuntos de dispositivos)

<!-- Next how-tos in the sequence -->
