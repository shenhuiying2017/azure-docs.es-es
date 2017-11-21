---
title: "Introducción a las soluciones preconfiguradas | Microsoft Docs"
description: "Siga este tutorial para aprender a implementar una solución preconfigurada del conjunto de IoT de Azure."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 12eb858f0be4220419300019497070a081e58249
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="get-started-with-the-preconfigured-solutions"></a>Introducción a las soluciones preconfiguradas

Las [soluciones preconfiguradas][lnk-preconfigured-solutions] del Conjunto de aplicaciones de IoT de Azure combinan varios servicios de IoT de Azure para ofrecer soluciones integrales que implementan escenarios empresariales de IoT comunes. La solución preconfigurada de *supervisión remota* permite conectar y supervisar los dispositivos. Puede utilizar la solución para analizar la transmisión de datos de los dispositivos y mejorar los resultados de negocio realizando procesos para responder automáticamente a esa transmisión de datos.

Este tutorial muestra cómo aprovisionar la solución preconfigurada de supervisión remota. También le guía por las características básicas de la solución preconfigurada. Puede acceder a muchas de estas características desde el *panel* de soluciones que se implementa como parte de la solución preconfigurada:

![Panel de la solución preconfigurada de supervisión remota][img-dashboard]

Para completar este tutorial, deberá tener una suscripción activa de Azure.

> [!NOTE]
> En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte la [evaluación gratuita de Azure][lnk_free_trial].

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

## <a name="scenario-overview"></a>Información general de escenario

Al implementar la solución preconfigurada de supervisión remota, esta se rellena previamente con los recursos que le permiten recorrer un escenario común de supervisión remota. En este escenario, varios dispositivos conectados a la solución informan sobre valores de temperatura inesperados. Las secciones siguientes le indicarán cómo:

* Identificar los dispositivos que envían los valores de temperatura inesperados.
* Configurar estos dispositivos para enviar telemetría más detallada.
* Corregir el problema actualizando el firmware en estos dispositivos.
* Compruebe que la acción ha solucionado el problema.

Una característica clave de este escenario es que se pueden realizar todas estas acciones de forma remota desde el panel de la solución. No es necesario el acceso físico a los dispositivos.

## <a name="view-the-solution-dashboard"></a>Visualización del panel de soluciones

El panel de la solución permite administrar la solución implementada. Por ejemplo, puede ver la telemetría, agregar dispositivos y configurar las reglas.

1. Cuando se haya completado el aprovisionamiento y el icono de la solución preconfigurada indique **Listo**, seleccione **Iniciar** para abrir el portal de la solución de supervisión remota en una nueva pestaña.

    ![Iniciar la solución preconfigurada][img-launch-solution]

1. De forma predeterminada, el portal de la solución muestra el *panel*. Puede navegar a otras áreas del portal de solución mediante el menú en el lado izquierdo de la página.

    ![Panel de la solución preconfigurada de supervisión remota][img-menu]

El panel muestra la siguiente información:

* Un mapa que muestra la ubicación de cada dispositivo conectado a la solución. La primera vez que se ejecuta la solución hay 25 dispositivos simulados. Los dispositivos simulados se implementan como WebJobs de Azure y la solución utiliza la API de Mapas de Bing para trazar la información en el mapa. Consulte el artículo sobre [preguntas más frecuentes][lnk-faq] para obtener información sobre cómo hacer que el mapa se vuelva dinámico.
* Un panel **Historial de telemetría** que traza los datos de telemetría de temperatura y humedad de un dispositivo seleccionado prácticamente en tiempo real y muestra los datos agregados, como la humedad máxima, mínima y media.
* Un panel **Historial de alarmas** que muestra los últimos eventos de alarma cuando un valor de telemetría ha superado un umbral. Puede definir sus propias alarmas, además de los ejemplos que crea la solución preconfigurada.
* Un panel **Trabajos** que muestra información acerca de los trabajos programados. En la página **Trabajos de administración** puede programar sus propios trabajos.

## <a name="view-alarms"></a>Vista de alarmas

El panel de historial de alarma muestra que hay cinco dispositivos que están informando sobre unos valores de telemetría más altos de lo esperado.

![TAREA Historial de alarmas en el panel de la solución][img-alarms]

> [!NOTE]
> Estas alarmas las genera una regla que se incluye en la solución preconfigurada. Esta regla genera una alerta cuando el valor de temperatura enviado por un dispositivo supera 60. Puede definir sus propias reglas y acciones eligiendo [Reglas](#add-a-rule) y [Acciones](#add-an-action) en el menú que aparece a la izquierda.

## <a name="view-devices"></a>Vista de dispositivos

La lista de *dispositivos* muestra todos los dispositivos registrados de la solución. En la lista de dispositivos puede ver y editar los metadatos del dispositivo, agregar o quitar dispositivos e invocar métodos en los dispositivos. Puede filtrar y ordenar la lista de dispositivos en la lista de dispositivos. También puede personalizar las columnas mostradas en la lista de dispositivos.

1. Elija **Dispositivos** para mostrar la lista de dispositivos para esta solución.

   ![Vista de la lista de dispositivos en el portal de la solución][img-devicelist]

1. La lista de dispositivos muestra inicialmente 25 dispositivos simulados creados por el proceso de aprovisionamiento. pero se pueden agregar más dispositivos simulados y físicos a la solución.

1. Seleccione un dispositivo en la lista de dispositivos para ver sus detalles.

   ![Vista de los detalles de dispositivos en el portal de la solución][img-devicedetails]

El panel **Detalles del dispositivo** contiene seis secciones:

* Una colección de vínculos que permiten personalizar el icono del dispositivo, deshabilitar el dispositivo, agregar una regla, invocar un método o enviar un comando. Para ver una comparación de comandos (mensajes de dispositivo a la nube) y métodos (métodos directos), consulte [Guía de comunicación de nube a dispositivo][lnk-c2d-guidance].
* La sección **Device Twin - Tags** (Dispositivo gemelo - Etiquetas) permite editar los valores de las etiquetas del dispositivo. Los valores de etiquetas se pueden mostrar en la lista de dispositivos y se pueden usar para filtrar la lista de dispositivos.
* La sección **Device Twin - Desired Properties** (Dispositivo gemelo - Propiedades deseadas) permite establecer los valores de las propiedades que se van a enviar al dispositivo.
* La sección **Device Twin - Reported Properties** (Dispositivo gemelo - Propiedades notificadas) muestra los valores de las propiedades enviados desde el dispositivo.
* La sección **Device Properties** (Propiedades del dispositivo) muestra la información del registro de identidades, como el id. de dispositivo y las claves de autenticación.
* La sección **Recent Jobs** (Trabajos recientes) muestra información acerca de todos los trabajos cuyo destino reciente ha sido este dispositivo.

## <a name="filter-the-device-list"></a>Filtro de la lista de dispositivos

Puede utilizar un filtro para mostrar solo aquellos dispositivos estén enviando valores de temperatura inesperados. La solución preconfigurada de supervisión remota incluye el filtro **Unhealthy devices** (Dispositivos con un estado incorrecto) para mostrar los dispositivos con un valor de temperatura media superior a 60. También puede [crear sus propios filtros](#add-a-filter).

1. Elija **Abrir el filtro guardado** para mostrar una lista de los filtros disponibles. A continuación, elija **Unhealthy devices** (Dispositivos con un estado incorrecto) para aplicar el filtro:

    ![Presentación de la lista de filtros][img-unhealthy-filter]

1. La lista de dispositivos muestra ahora solo los dispositivos con un valor de la temperatura media superior a 60.

    ![Vista de la lista de dispositivos filtrados que muestra los dispositivos con un estado incorrecto][img-filtered-unhealthy-list]

## <a name="update-desired-properties"></a>Actualizar las propiedades deseadas

Ha identificado un conjunto de dispositivos que pueden necesitar corrección. Sin embargo, ha decidido que la frecuencia de datos de 15 segundos no es suficiente para un diagnóstico claro del problema. Cambiar la frecuencia de telemetría a cinco segundos le proporciona más puntos de datos que le permitirán diagnosticar mejor el problema. Puede insertar este cambio de configuración desde el portal de la solución a los dispositivos remotos. Puede realizar el cambio una vez, evaluar el impacto y actuar dependiendo de los resultados.

Siga estos pasos para ejecutar un trabajo que cambia la propiedad deseada de **TelemetryInterval** para los dispositivos afectados. Cuando los dispositivos reciben el nuevo valor de la propiedad **TelemetryInterval**, cambian su configuración para enviar telemetría cada cinco segundos en lugar de cada 15 segundos:

1. Mientras se muestra la lista de dispositivos con un estado incorrecto en la lista de dispositivos, elija **Programador de trabajos** y a continuación **Editar dispositivo gemelo**.

1. Llame al trabajo **Cambiar el intervalo de telemetría**.

1. Cambie el valor del nombre de **Propiedad deseada** **desired.Config.TelemetryInterval** a cinco segundos.

1. Seleccione **Programación**.

    ![Cambio de la propiedad TelemetryInterval a cinco segundos][img-change-interval]

1. Puede supervisar el estado del trabajo en la página **Trabajos de administración** en el portal.

> [!NOTE]
> Si quiere cambiar un valor de propiedad deseada para un dispositivo individual, utilice la sección **Propiedades deseadas** del panel **Detalles del dispositivo** en lugar de ejecutar un trabajo.

Este trabajo establece el valor de la propiedad deseada **TelemetryInterval** en el dispositivo gemelo para todos los dispositivos seleccionados por el filtro. Los dispositivos recuperan este valor desde el dispositivo gemelo y actualizan su comportamiento. Cuando un dispositivo recupera y procesa una propiedad deseada de un dispositivo gemelo, establece la propiedad de valor notificado correspondiente.

## <a name="invoke-methods"></a>Invocación de métodos

Mientras se ejecuta el trabajo, se observa en la lista de dispositivos con un estado incorrecto que todos estos ellos tienen versiones antiguas de firmware (inferiores a la versión 1.6).

![Visualización de la versión de firmware notificado para los dispositivos con un estado incorrecto][img-old-firmware]

La versión de firmware puede ser la causa principal de los valores de temperatura inesperados, porque se sabe que otros dispositivos cuyo estado es correcto se actualizaron recientemente a la versión 2.0. Puede usar el filtro integrado **Old firmware devices**(Dispositivos de firmware antiguos) para identificar los dispositivos con versiones antiguas de firmware. Desde el portal, puede, a continuación, actualizar de forma remota todos los dispositivos sigan ejecutando versiones de firmware antiguo:

1. Elija **Abrir el filtro guardado** para mostrar una lista de los filtros disponibles. A continuación, elija **Old firmware devices** (Dispositivos de firmware antiguos) para aplicar el filtro:

    ![Presentación de la lista de filtros][img-old-filter]

1. La lista de dispositivos muestra ahora solo los dispositivos que tienen versiones de firmware antiguas. Esta lista incluye los cinco dispositivos identificados por el filtro **Unhealthy devices** (Dispositivos con un estado incorrecto) y tres dispositivos adicionales:

    ![Vista de la lista de dispositivos filtrados que muestra los dispositivos con una versión antigua][img-filtered-old-list]

1. Elija **Programador de trabajos** y, a continuación, **Invocar método**.

1. Establezca el **Nombre del trabajo** a **Actualización de firmware a la versión 2.0**.

1. Elija **InitiateFirmwareUpdate** como **Método**.

1. Establezca el parámetro**FwPackageUri** como **https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin**.

1. Seleccione **Programación**. De forma predeterminada, el trabajo se ejecuta ahora.

    ![Creación de un trabajo para actualizar el firmware de los dispositivos seleccionados][img-method-update]

> [!NOTE]
> Si desea invocar un método en un dispositivo individual, elija **Métodos** en el panel **Detalles del dispositivo** en lugar de ejecutar un trabajo.

Este trabajo invoca el método directo **InitiateFirmwareUpdate** en todos los dispositivos seleccionados por el filtro. Los dispositivos responden inmediatamente a IoT Hub y, a continuación, inician el proceso de actualización de firmware de forma asincrónica. Los dispositivos proporcionan información de estado sobre el proceso de actualización de firmware a través de los valores de propiedad notificados, tal y como se muestra en las siguientes capturas de pantalla. Elija el icono **Actualizar** para actualizar la información en las listas de dispositivo y de trabajo:

![Lista de trabajos que muestra la lista de actualizaciones de firmware ejecutándose][img-update-1]
![Lista de dispositivos que muestra el estado de actualización de firmware][img-update-2]
![Lista de trabajos que muestra la lista de actualizaciones de firmware completada][img-update-3]

> [!NOTE]
> En un entorno de producción, puede programar trabajos para que se ejecuten durante una ventana de mantenimiento designada.

## <a name="scenario-review"></a>Revisión del escenario

En este escenario, ha identificado un problema potencial con algunos de los dispositivos remotos mediante el historial de alarma en el panel y un filtro. A continuación, ha usado el filtro y un trabajo para configurar de forma remota los dispositivos para proporcionar más información que ayude a diagnosticar el problema. Por último, ha utilizado un filtro y un trabajo para programar el mantenimiento en los dispositivos afectados. Si vuelve al panel, puede comprobar que ya no hay alarmas procedentes de dispositivos de la solución. Puede utilizar un filtro para comprobar que el firmware está actualizado en todos los dispositivos de la solución y que ya no hay dispositivos con un estado incorrecto:

![Filtro que muestra que todos los dispositivos tienen firmware actualizado][img-updated]

![Filtro que muestra que todos los dispositivos tiene un estado correcto][img-healthy]

## <a name="other-features"></a>Otras características

Las secciones siguientes describen algunas características adicionales de la solución preconfigurada de supervisión remota que no se han descrito como parte del escenario anterior.

### <a name="customize-columns"></a>Personalización de columnas

Para personalizar la información que se muestra en la lista de dispositivos, haga clic en **Editor de columnas**. Puede agregar y quitar las columnas que muestran los valores de propiedad y etiqueta notificados. Las columnas también se pueden reordenar y cambiar de nombre:

   ![Editor de columna en la lista de dispositivos][img-columneditor]

### <a name="customize-the-device-icon"></a>Personalización del icono del dispositivo

El icono del dispositivo que se muestra en la lista de dispositivos del panel **Detalles del dispositivo** se puede personalizar como se indica a continuación:

1. Haga clic en el icono del lápiz para abrir el panel **Modificar imagen** de un dispositivo:

   ![Abrir el editor de imágenes del dispositivo][img-startimageedit]

1. Cargue una nueva imagen o utilice una de las imágenes existentes y seleccione **Guardar**:

   ![Modificar editor de imágenes del dispositivo][img-imageedit]

1. La imagen que seleccionó se muestra ahora en la columna **Icono** del dispositivo.

> [!NOTE]
> La imagen se almacena en Blob Storage. Una etiqueta del dispositivo gemelo contiene un vínculo a la imagen en Blob Storage.

### <a name="add-a-device"></a>Agregar un dispositivo

Al implementar la solución preconfigurada, se aprovisionan automáticamente los 25 dispositivos de ejemplo que se pueden ver en la lista de dispositivos. Estos dispositivos son *dispositivos simulados* que se ejecutan en un WebJob de Azure. Los dispositivos simulados facilitan la experimentación de la solución preconfigurada sin necesidad de implementar dispositivos físicos reales. Si quiere conectar un dispositivo real a la solución, consulte el tutorial [Conectar el dispositivo a la solución preconfigurada de supervisión remota (Windows)][lnk-connect-rm].

Los pasos siguientes explican cómo agregar un nuevo dispositivo simulado a la solución:

1. Vuelva a navegar hasta la lista de dispositivos.

1. Haga clic en **+ Agregar un dispositivo** en la esquina inferior izquierda.

   ![Agregar un dispositivo a la solución preconfigurada][img-adddevice]

1. Haga clic en **Añadir nuevo** en el icono **Dispositivo simulado**.

   ![Establecer nuevos detalles de los dispositivos en el panel][img-addnew]

   Si elige crear un **dispositivo personalizado**, además de crear un dispositivo simulado nuevo, también puede agregar un dispositivo físico. Para más información acerca de cómo conectar dispositivos físicos a la solución, consulte el tutorial para la [conexión del dispositivo a la solución preconfigurada de supervisión remota del conjunto de aplicaciones de IoT][lnk-connect-rm].

1. Seleccione **Let me define my own Device ID** (Permitirme definir mi propio identificador de dispositivo) y escriba un nombre de identificador de dispositivo único como **mydevice_01**.

1. Seleccione **Create**.

   ![Guardar un dispositivo nuevo][img-definedevice]

1. En el paso 3 de **Add a simulated device** (Agregar un dispositivo simulado), seleccione **Listo** para volver a la lista de dispositivos.

1. Puede ver su dispositivo **En ejecución** en la lista de dispositivos.

    ![Ver un dispositivo nuevo en la lista de dispositivos][img-runningnew]

1. También puede ver la telemetría simulada del dispositivo nuevo en el panel:

    ![Ver telemetría desde el nuevo dispositivo][img-runningnew-2]

### <a name="disable-and-delete-a-device"></a>Deshabilitar y eliminar un dispositivo

Puede deshabilitar un dispositivo y, después, quitarlo:

![Deshabilitar y quitar un dispositivo][img-disable]

### <a name="add-a-rule"></a>Agregar una regla

No hay reglas para el nuevo dispositivo que acaba de agregar. En esta sección, agregará una regla que desencadenará una alarma cuando la temperatura notificada por el nuevo dispositivo supere los 47 grados. Antes de comenzar, observe que el historial de telemetría del nuevo dispositivo en el panel muestra que la temperatura de dispositivo nunca supera los 45 grados.

1. Vuelva a navegar hasta la lista de dispositivos.

1. Para agregar una regla al dispositivo, seleccione el dispositivo nuevo en la **Lista de dispositivos** y haga clic en **Agregar regla**.

1. Cree una regla que utilice **Temperature** como campo de datos y **AlarmTemp** como resultado cuando la temperatura supere los 47 grados:

    ![Agregar una regla de dispositivo][img-adddevicerule]

1. Para guardar los cambios, haga clic en **Guardar y ver reglas**.

1. Haga clic en **Comandos** en el panel de detalles del dispositivo nuevo.

    ![Agregar una regla de dispositivo][img-adddevicerule2]

1. Seleccione **ChangeSetPointTemp** en la lista de comandos y establezca **SetPointTemp** en 45. A continuación, elija **Enviar comando**:

    ![Agregar una regla de dispositivo][img-adddevicerule3]

1. Vuelva al panel. Al poco tiempo, verá una entrada nueva en el panel **Historial de alarmas** cuando la temperatura que notifique el nuevo dispositivo supere el umbral de 47 grados:

    ![Agregar una regla de dispositivo][img-adddevicerule4]

1. Todas las reglas se pueden revisar y editar en la página **Reglas** del panel:

    ![Enumerar reglas de dispositivo][img-rules]

1. Todas las acciones que se pueden realizar en respuesta a una regla se pueden revisar y editar en la página **Acciones** del panel:

    ![Enumerar acciones de dispositivo][img-actions]

> [!NOTE]
> Es posible definir las acciones que pueden enviar un mensaje de correo electrónico o un SMS en respuesta a una regla, o bien integrarse en un sistema de línea de negocio a través de una [aplicación lógica][lnk-logic-apps]. Para más información, consulte el tutorial sobre [conexión de una aplicación lógica a la solución preconfigurada de supervisión remota del conjunto de aplicaciones de IoT de Azure][lnk-logicapptutorial].

### <a name="manage-filters"></a>Administración de filtros

En la lista de dispositivos, puede crear, guardar y volver a cargar filtros para mostrar una lista personalizada de los dispositivos conectados al centro. Para crear un filtro:

1. Seleccione el icono de edición de filtro situado encima de la lista de dispositivos:

    ![Abra el editor de filtros][img-editfiltericon]

1. En el **editor de filtros**, agregue los campos, operadores y valores necesarios para filtrar la lista de dispositivos. Para refinar el filtro puede agregar varias cláusulas. Haga clic en **Filtro** para aplicar el filtro:

    ![Crear un filtro][img-filtereditor]

1. En este ejemplo, la lista se filtra por fabricante y número de modelo:

    ![Lista filtrada][img-filterelist]

1. Para guardar el filtro con un nombre personalizado, haga clic en el icono **Guardar como**:

    ![Agregar un filtro][img-savefilter]

1. Para volver a aplicar un filtro que se ha guardado previamente, haga clic en el icono **Abrir el filtro guardado**:

    ![Abrir un filtro][img-openfilter]

Se pueden crear filtros basados en el Id. de dispositivo, estado del dispositivo, propiedades deseadas, propiedades notificadas y etiquetas. Agregue sus propias etiquetas personalizadas a un dispositivo en la sección **Etiquetas** del panel **Detalles del dispositivo** o ejecute un trabajo para actualizar las etiquetas en varios dispositivos.

> [!NOTE]
> En el **editor de filtros**, se puede usar la **vista avanzada** para editar el texto de consulta directamente.

### <a name="commands"></a>Comandos:

En el panel **Detalles del dispositivo**, se pueden enviar comandos al dispositivo. La primera vez que se inicia un dispositivo, este envía a la solución información acerca de los comandos que admite. Para obtener una explicación de las diferencias entre los comandos y los métodos, consulte [Opciones de nube a dispositivo de Azure IoT Hub][lnk-c2d-guidance].

1. Haga clic en **Comandos** en el panel **Detalles del dispositivo** del dispositivo seleccionado:

   ![Comandos de los dispositivos en el panel][img-devicecommands]

1. Seleccione **PingDevice** en la lista de comandos.

1. Elija **Enviar comando**.

1. Puede ver el estado del comando en el historial de comandos.

   ![Estado de los comandos en el panel][img-pingcommand]

La solución hace un seguimiento del estado de cada comando que envía. Inicialmente, el resultado es **Pendiente**. Cuando el dispositivo informa de que ha ejecutado el comando, el resultado se establece en **Correcto**.

## <a name="behind-the-scenes"></a>Entre bambalinas

Al implementar una solución preconfigurada, el proceso de implementación crea varios recursos en la suscripción de Azure seleccionada. Dichos recursos se pueden ver en Azure [Portal][lnk-portal]. El proceso de implementación crea un **grupo de recursos** cuyo nombre se basa en el nombre que eligió para la solución preconfigurada:

![Solución preconfigurada en el portal de Azure][img-portal]

Para ver la configuración de cada recurso, selecciónelo en la lista de recursos del grupo de recursos.

También puede ver el código fuente de la solución preconfigurada. El código fuente de la solución preconfigurada de supervisión remota está en el repositorio de GitHub [azure-iot-remote-monitoring][lnk-rmgithub]:

* La carpeta **DeviceAdministration** contiene el código fuente del panel.
* La carpeta **Simulator** contiene el código fuente del dispositivo simulado.
* La carpeta **EventProcessor** contiene el código fuente del proceso de back-end que controla los datos de telemetría entrantes.

Cuando haya terminado, puede eliminar la solución preconfigurada de la suscripción de Azure en el sitio [azureiotsuite.com][lnk-azureiotsuite]. Este sitio le permite eliminar fácilmente todos los recursos que se aprovisionaron cuando creó la solución preconfigurada.

> [!NOTE]
> Para asegurarse de que elimina todo lo relacionado con la solución preconfigurada, elimínelo en el sitio [azureiotsuite.com][lnk-azureiotsuite] y no elimine simplemente el grupo de recursos en el portal.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha implementado una solución preconfigurada de trabajo, puede continuar su introducción al Conjunto de aplicaciones de IoT con la lectura de los artículos siguientes:

* [Tutorial de la solución preconfigurada de supervisión remota][lnk-rm-walkthrough]
* [Conectar el dispositivo a la solución preconfigurada de supervisión remota][lnk-connect-rm]
* [Permisos en el sitio azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-v1-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-v1-getstarted-preconfigured-solutions/dashboard.png
[img-menu]: media/iot-suite-v1-getstarted-preconfigured-solutions/menu.png
[img-devicelist]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicelist.png
[img-alarms]: media/iot-suite-v1-getstarted-preconfigured-solutions/alarms.png
[img-devicedetails]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-v1-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-v1-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-v1-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-v1-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-v1-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-v1-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-v1-getstarted-preconfigured-solutions/rules.png
[img-adddevicerule]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-v1-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-v1-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-v1-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-v1-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-v1-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-v1-getstarted-preconfigured-solutions/imagedit2.png
[img-editfiltericon]: media/iot-suite-v1-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-v1-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-v1-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-v1-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-v1-getstarted-preconfigured-solutions/openfilter.png
[img-unhealthy-filter]: media/iot-suite-v1-getstarted-preconfigured-solutions/unhealthyfilter.png
[img-filtered-unhealthy-list]: media/iot-suite-v1-getstarted-preconfigured-solutions/unhealthylist.png
[img-change-interval]: media/iot-suite-v1-getstarted-preconfigured-solutions/changeinterval.png
[img-old-firmware]: media/iot-suite-v1-getstarted-preconfigured-solutions/noticeold.png
[img-old-filter]: media/iot-suite-v1-getstarted-preconfigured-solutions/oldfilter.png
[img-filtered-old-list]: media/iot-suite-v1-getstarted-preconfigured-solutions/oldlist.png
[img-method-update]: media/iot-suite-v1-getstarted-preconfigured-solutions/methodupdate.png
[img-update-1]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate1.png
[img-update-2]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate2.png
[img-update-3]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate3.png
[img-updated]: media/iot-suite-v1-getstarted-preconfigured-solutions/updated.png
[img-healthy]: media/iot-suite-v1-getstarted-preconfigured-solutions/healthy.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-logicapptutorial]: iot-suite-v1-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-v1-connecting-devices.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-faq]: iot-suite-v1-faq.md