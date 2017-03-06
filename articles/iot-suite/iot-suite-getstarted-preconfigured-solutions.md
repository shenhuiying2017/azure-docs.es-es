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
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 9e1bcba086a9f70c689a5d7d7713a8ecdc764492
ms.openlocfilehash: 8248e0a02cb0775a87f0c8130e53b98f8bcfe581
ms.lasthandoff: 02/27/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Tutorial: Introducción a las soluciones preconfiguradas
## <a name="introduction"></a>Introducción
Las [soluciones preconfiguradas][lnk-preconfigured-solutions] del Conjunto de aplicaciones de IoT de Azure combinan varios servicios de IoT de Azure para ofrecer soluciones integrales que implementan escenarios empresariales de IoT comunes. La solución preconfigurada de *supervisión remota* permite conectar y supervisar los dispositivos. Puede utilizar la solución para analizar la transmisión de datos de los dispositivos y mejorar los resultados de negocio realizando procesos para responder automáticamente a esa transmisión de datos.

Este tutorial muestra cómo aprovisionar la solución preconfigurada de supervisión remota. También le guía por las características básicas de la solución preconfigurada. Puede acceder a muchas de estas características desde el panel de soluciones que se implementa como parte de la solución preconfigurada:

![Panel de la solución preconfigurada de supervisión remota][img-dashboard]

Para completar este tutorial, deberá tener una suscripción activa de Azure.

> [!NOTE]
> En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte la [evaluación gratuita de Azure][lnk_free_trial].
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>Visualización del panel de soluciones
El panel de la solución permite administrar la solución implementada. Por ejemplo, puede ver la telemetría, agregar dispositivos y configurar las reglas.

1. Cuando se complete el aprovisionamiento y el icono de la solución preconfigurada indique **Listo**, haga clic en **Iniciar** para abrir el portal de la solución de supervisión remota en una nueva pestaña.
   
   ![Iniciar la solución preconfigurada][img-launch-solution]
2. De forma predeterminada, el portal de la solución muestra el *panel de la solución*. Puede seleccionar otras vistas mediante el menú izquierdo.
   
   ![Panel de la solución preconfigurada de supervisión remota][img-dashboard]

El panel muestra la siguiente información:

* El mapa muestra la ubicación de cada dispositivo conectado a la solución. La primera vez que se ejecuta la solución hay cuatro dispositivos simulados. Los dispositivos simulados se implementan como WebJobs de Azure y la solución utiliza la API de Mapas de Bing para trazar la información en el mapa.
* El panel **Historial de telemetría** traza los datos de telemetría de temperatura y humedad de un dispositivo seleccionado prácticamente en tiempo real y muestra los datos agregados, como la humedad máxima, mínima y media.
* El panel **Historial de alarmas** muestra los últimos eventos de alarma cuando un valor de telemetría ha superado un umbral. Puede definir sus propias alarmas, además de los ejemplos que crea la solución preconfigurada.
* El panel **Trabajos** muestra información acerca de los trabajos programados. En la página **Trabajos de administración** puede programar sus propios trabajos.

## <a name="view-the-device-list"></a>Visualización de la lista de dispositivos
La *lista de dispositivos* muestra todos los dispositivos registrados de la solución. En la lista de dispositivos puede ver y editar los metadatos del dispositivo, agregar o quitar dispositivos e invocar métodos en los dispositivos.

1. Haga clic en **Dispositivos** en el menú izquierdo para mostrar la lista de dispositivos de la solución.
   
   ![Lista de dispositivos en el panel][img-devicelist]
2. La lista de dispositivos muestra inicialmente cuatro dispositivos simulados creados por el proceso de aprovisionamiento, pero se pueden agregar más dispositivos simulados y físicos a la solución.
3. Para personalizar la información que se muestra en la lista de dispositivos, haga clic en **Editor de columnas**. Puede agregar y quitar las columnas que muestran los valores de propiedad y etiqueta notificados. Las columnas también se pueden reordenar y cambiar de nombre:
   
   ![Editor de columnas del panel][img-columneditor]
4. Para ver los detalles de un dispositivo, haga clic en él en la lista de dispositivos.
   
   ![Detalles de los dispositivos en el panel][img-devicedetails]

El panel **Detalles del dispositivo** contiene seis secciones:

* Una colección de vínculos que permiten personalizar el icono del dispositivo, deshabilitar el dispositivo, agregar una regla, invocar un método o enviar un comando. Para ver una comparación de comandos (mensajes de dispositivo a la nube) y métodos (métodos directos), consulte [Guía de comunicación de nube a dispositivo][lnk-c2d-guidance].
* La sección **Device Twin - Tags** (Dispositivo gemelo - Etiquetas) permite editar los valores de las etiquetas del dispositivo. Los valores de etiquetas se pueden mostrar en la lista de dispositivos y se pueden usar para filtrar la lista de dispositivos.
* La sección **Device Twin - Desired Properties** (Dispositivo gemelo - Propiedades deseadas) permite establecer los valores de las propiedades que se van a enviar al dispositivo.
* La sección **Device Twin - Reported Properties** (Dispositivo gemelo - Propiedades notificadas) muestra los valores de las propiedades enviados desde el dispositivo.
* La sección **Device Properties** (Propiedades del dispositivo) muestra la información del registro de identidades, como el id. de dispositivo y las claves de autenticación.
* La sección **Recent Jobs** (Trabajos recientes) muestra información acerca de todos los trabajos cuyo destino reciente ha sido este dispositivo.

## <a name="customize-the-device-icon"></a>Personalización del icono del dispositivo

El icono del dispositivo que se muestra en la lista de dispositivos del panel **Detalles del dispositivo** se puede personalizar como se indica a continuación:

1. Haga clic en el icono del lápiz para abrir el panel **Modificar imagen** de un dispositivo:
   
   ![Abrir el editor de imágenes del dispositivo][img-startimageedit]
2. Cargue una nueva imagen o utilice una de las imágenes existentes y, después, haga clic en **Guardar**:
   
   ![Modificar editor de imágenes del dispositivo][img-imageedit]
3. La imagen que seleccionó se muestra ahora en la columna **Icono** del dispositivo.

> [!NOTE]
> La imagen se almacena en Blob Storage. Una etiqueta del dispositivo gemelo contiene un vínculo a la imagen en Blob Storage.
> 
> 

## <a name="invoke-a-method-on-a-device"></a>Invocación a un método en un dispositivo
En el panel **Detalles del dispositivo** se pueden invocar métodos en el dispositivo. La primera vez que se inicia un dispositivo, este envía a la solución información acerca de los métodos que admite.

1. Haga clic en **Métodos** en el panel **Detalles del dispositivo** del dispositivo seleccionado:
   
   ![Métodos de dispositivo en el panel][img-devicemethods]
2. Seleccione **Reiniciar** en la lista de métodos.
3. Haga clic en **Invocar método**.
4. El estado de la invocación del método se puede ver en el historial del método.
   
   ![Estado del método en el panel][img-pingmethod]

La solución hace un seguimiento del estado de cada método al que invoca. Cuando el dispositivo completa el método, se ve una nueva entrada en la tabla del historial del método.

Algunos métodos inician trabajos asincrónicos en el dispositivo. Por ejemplo, el método **InitiateFirmwareUpdate** inicia una tarea asincrónica para realizar la actualización. El dispositivo utiliza propiedades notificadas para informar acerca del estado de la actualización de firmware a medida que progresa.

## <a name="send-a-command-to-a-device"></a>Envío de un comando a un dispositivo
En el panel **Detalles del dispositivo**, se pueden enviar comandos al dispositivo. La primera vez que se inicia un dispositivo, este envía a la solución información acerca de los comandos que admite.

1. Haga clic en **Comandos** en el panel **Detalles del dispositivo** del dispositivo seleccionado:
   
   ![Comandos de los dispositivos en el panel][img-devicecommands]
2. Seleccione **PingDevice** en la lista de comandos.
3. Haga clic en **Enviar comando**.
4. Puede ver el estado del comando en el historial de comandos.
   
   ![Estado de los comandos en el panel][img-pingcommand]

La solución hace un seguimiento del estado de cada comando que envía. Inicialmente, el resultado es **Pendiente**. Cuando el dispositivo informa de que ha ejecutado el comando, el resultado se establece en **Correcto**.

## <a name="add-a-new-simulated-device"></a>Adición de un nuevo dispositivo simulado
Al implementar la solución preconfigurada, se aprovisionan automáticamente los cuatro dispositivos de ejemplo que se pueden ver en la lista de dispositivos. Estos dispositivos son *dispositivos simulados* que se ejecutan en un WebJob de Azure. Los dispositivos simulados facilitan la experimentación de la solución preconfigurada sin necesidad de implementar dispositivos físicos reales. Si quiere conectar un dispositivo real a la solución, consulte el tutorial [Conectar el dispositivo a la solución preconfigurada de supervisión remota (Windows)][lnk-connect-rm].

Los pasos siguientes explican cómo agregar un nuevo dispositivo simulado a la solución:

1. Vuelva a navegar hasta la lista de dispositivos.
2. Haga clic en **+ Agregar un dispositivo** en la esquina inferior izquierda.
   
   ![Agregar un dispositivo a la solución preconfigurada][img-adddevice]
3. Haga clic en **Agregar nuevo** en el icono **Simulated Device** (Dispositivo simulado).
   
   ![Establecer nuevos detalles de los dispositivos en el panel][img-addnew]
   
   Si elige crear un **dispositivo personalizado**, además de crear un dispositivo simulado nuevo, también puede agregar un dispositivo físico. Para más información acerca de cómo conectar dispositivos físicos a la solución, consulte el tutorial para la [conexión del dispositivo a la solución preconfigurada de supervisión remota del conjunto de aplicaciones de IoT][lnk-connect-rm].
4. Seleccione **Let me define my own Device ID** (Permitirme definir mi propio identificador de dispositivo) y escriba un nombre de identificador de dispositivo único como **mydevice_01**.
5. Haga clic en **Crear**.
   
   ![Guardar un dispositivo nuevo][img-definedevice]
6. En el paso 3 de **Add a simulated device** (Agregar un dispositivo simulado), haga clic en **Listo** para volver a la lista de dispositivos.
7. Puede ver su dispositivo **En ejecución** en la lista de dispositivos.
   
    ![Ver un dispositivo nuevo en la lista de dispositivos][img-runningnew]
8. También puede ver la telemetría simulada del dispositivo nuevo en el panel:
   
    ![Ver telemetría desde el nuevo dispositivo][img-runningnew-2]

## <a name="device-properties"></a>Propiedades del dispositivo
La solución preconfigurada de supervisión remota usa [dispositivos gemelos][lnk-device-twin] para sincronizar los metadatos de dispositivo entre los dispositivos y el back-end de la solución. Un dispositivo gemelo es un documento JSON almacenado en IoT Hub que almacena los valores de las propiedades de un dispositivo individual. Los dispositivos envían de forma regular metadatos al back-end de la solución como *propiedades notificadas* que se almacenan en el dispositivo gemelo. El back-end de la solución puede establecer las *propiedades deseadas* en el dispositivo gemelo para enviar las actualizaciones de los metadatos a los dispositivos. Las propiedades notificadas muestran los valores de metadatos más recientes enviados por el dispositivo. Para más información, consulte [Registro de identidades de dispositivo y DocumentDB][lnk-devicemetadata].

> [!NOTE]
> La solución también utiliza una base de datos de DocumentDB para almacenar datos específicos del dispositivo relacionados con comandos y métodos.
> 
> 

1. Vuelva a navegar hasta la lista de dispositivos.
2. Seleccione el nuevo dispositivo en la **lista de dispositivos** y haga clic en **Editar** para editar la sección **Device Twin - Desired Properties** (Dispositivo gemelo - Propiedades deseadas):
   
   ![Editar propiedades deseadas de dispositivo][img-editdevice]
3. En **Nombre de la propiedad deseada**, seleccione **Latitud** y establezca el valor en **47,639521**. Luego, haga clic en **Save Changes to Device Registry** (Guardar cambios en registro de dispositivo):
   
    ![Editar propiedad deseada de dispositivo][img-editdevice2]
4. En el panel **Detalles del dispositivo**, el valor de la nueva latitud se muestra como una propiedad deseada, mientras que el valor de latitud anterior se muestra como una propiedad notificada:
   
    ![Ver propiedad notificada][img-editdevice3]
5. Actualmente, los dispositivos simulados de la solución preconfigurada solo procesan las propiedades deseadas **Desired.Config.TemperatureMeanValue** y **Desired.Config.TelemetryInterval**. Un dispositivo real debe leer todas las propiedades deseadas de IoT Hub, realizar los cambios necesarios en su configuración y notificar los nuevos valores al centro como propiedades notificadas.

En el panel **Detalles del dispositivo**, también se puede editar la sección **Device Twin - Tags** (Dispositivo gemelo - Etiquetas) de la misma manera que se edita **Device Twin - Desired Properties** (Dispositivo gemelo - Propiedades deseadas). Sin embargo, a diferencia de las propiedades deseadas, las etiquetas no se sincronizan con el dispositivo. Las etiquetas solo está presente en el dispositivo gemelo en IoT Hub. Las etiquetas son útiles para la creación de filtros personalizados en la lista de dispositivos.

## <a name="sort-the-device-list"></a>Ordenar la lista de dispositivos

Para ordenar la lista de dispositivos, haga clic al lado del encabezado de columna. Con el primer clic la clasificación se realiza en orden ascendente, mientras que con el segundo, se realiza en orden descendente:

![Ordenar lista de dispositivos][img-sortdevices]

## <a name="filter-the-device-list"></a>Filtro de la lista de dispositivos

En la lista de dispositivos, puede crear, guardar y volver a cargar filtros para mostrar una lista personalizada de los dispositivos conectados al centro. Para crear un filtro:

1. Haga clic en el icono de edición de filtro situado encima de la lista de dispositivos:
   
   ![Abra el editor de filtros][img-editfiltericon]
2. En el **editor de filtros**, agregue los campos, operadores y valores necesarios para filtrar la lista de dispositivos. Para refinar el filtro puede agregar varias cláusulas. Haga clic en **Filtro** para aplicar el filtro:
   
   ![Crear un filtro][img-filtereditor]
3. En este ejemplo, la lista se filtra por fabricante y número de modelo:
   
   ![Lista filtrada][img-filterelist]
4. Para guardar el filtro con un nombre personalizado, haga clic en el icono **Guardar como**:
   
   ![Agregar un filtro][img-savefilter]
5. Para volver a aplicar un filtro que se ha guardado previamente, haga clic en el **Abrir el filtro guardado**:
   
   ![Abrir un filtro][img-openfilter]

Se pueden crear filtros basados en el Id. de dispositivo, estado del dispositivo, propiedades deseadas, propiedades notificadas y etiquetas.

> [!NOTE]
> En el **editor de filtros**, se puede usar la **vista avanzada** para editar el texto de consulta directamente.
> 
> 

## <a name="add-a-rule-for-the-new-device"></a>Adición de una regla para el nuevo dispositivo
No hay reglas para el nuevo dispositivo que acaba de agregar. En esta sección, agregará una regla que desencadenará una alarma cuando la temperatura notificada por el nuevo dispositivo supere los 47 grados. Antes de comenzar, observe que el historial de telemetría del nuevo dispositivo en el panel muestra que la temperatura de dispositivo nunca supera los 45 grados.

1. Vuelva a navegar hasta la lista de dispositivos.
2. Para agregar una regla al dispositivo, seleccione el dispositivo nuevo en la **lista de dispositivos** y haga clic en **Agregar regla**.
3. Cree una regla que utilice **Temperature** como campo de datos y **AlarmTemp** como resultado cuando la temperatura supere los 47 grados:
   
    ![Agregar una regla de dispositivo][img-adddevicerule]
4. Para guardar los cambios, haga clic en **Guardar y ver reglas**.
5. Haga clic en **Comandos** en el panel de detalles del dispositivo nuevo.
   
   ![Agregar una regla de dispositivo][img-adddevicerule2]
6. Seleccione **ChangeSetPointTemp** en la lista de comandos y establezca **SetPointTemp** en 45. Después, haga clic en **Enviar comando**:
   
   ![Agregar una regla de dispositivo][img-adddevicerule3]
7. Vuelva a navegar al panel de la solución. Al poco tiempo, verá una entrada nueva en el panel **Historial de alarmas** cuando la temperatura que notifique el nuevo dispositivo supere el umbral de 47 grados:
   
   ![Agregar una regla de dispositivo][img-adddevicerule4]
8. Todas las reglas se pueden revisar y editar en la página **Reglas** del panel:
   
    ![Enumerar reglas de dispositivo][img-rules]
9. Todas las acciones que se pueden realizar en respuesta a una regla se pueden revisar y editar en la página **Acciones** del panel:
   
    ![Enumerar acciones de dispositivo][img-actions]

> [!NOTE]
> Es posible definir las acciones que pueden enviar un mensaje de correo electrónico o un SMS en respuesta a una regla, o bien integrarse en un sistema de línea de negocio a través de una [aplicación lógica][lnk-logic-apps]. Para más información, consulte el tutorial sobre [conexión de una aplicación lógica a la solución preconfigurada de supervisión remota del conjunto de aplicaciones de IoT de Azure][lnk-logicapptutorial].
> 
> 

## <a name="disable-and-remove-devices"></a>Deshabilitación y eliminación de dispositivos
Puede deshabilitar un dispositivo y, después, quitarlo:

![Deshabilitar y quitar un dispositivo][img-disable]

## <a name="run-jobs"></a>Ejecución de trabajos
Para realizar operaciones masivas en los dispositivos se pueden programar trabajos. Cada trabajo se crea para una lista de dispositivos. Dicha lista puede contener todos los dispositivos o puede ser una lista filtrada que se creó mediante las [herramientas de filtro](#filter-the-device-list) en la lista de dispositivos. Un trabajo puede invocar un método en cada dispositivo de la lista o puede actualizar el dispositivo gemelo para todos los dispositivos de la lista.

### <a name="create-a-job-to-invoke-a-method"></a>Creación de un trabajo para invocar un método

Los pasos siguientes muestran cómo crear un trabajo que invoca el método de actualización del firmware en todos los dispositivos de una lista. El método solo se invoca en los dispositivos compatibles con el método:

1. Use las herramientas de filtro en la lista de dispositivos para crear una lista de dispositivos que reciban la actualización del firmware:
   
   ![Abra el editor de filtros][img-editfiltericon]
2. En la lista filtrada, haga clic en **Programador de trabajos**:
   
   ![Abra al programador de trabajos][img-clickjobscheduler]
3. En el panel **Programar trabajo**, haga clic en **Invocar método**.
4. En la página **Invocar método**, escriba los detalles del método que se va a invocar y, después, haga clic en **Programación**:
   
   ![Configurar trabajo de método][img-invokemethodjob]

El método **InitiateFirmwareUpdate** inicia una tarea de forma asincrónica en el dispositivo y vuelve inmediatamente. Después, el proceso de actualización de firmware utiliza propiedades notificadas para informar acerca del proceso de actualización mientras se ejecuta.

### <a name="create-a-job-to-edit-the-device-twin"></a>Creación de un trabajo para editar al dispositivo gemelo

Los pasos siguientes muestran cómo crear un trabajo que edita el dispositivo gemelo en todos los dispositivos de una lista:

1. Use las herramientas de filtro en la lista de dispositivos para crear una lista de dispositivos que reciban las ediciones de dispositivos gemelos:
   
   ![Abra el editor de filtros][img-editfiltericon]
2. En la lista filtrada, haga clic en **Programador de trabajos**:
   
   ![Abra al programador de trabajos][img-clickjobscheduler]
3. En el panel **Programar trabajo**, haga clic en **Editar dispositivo gemelo**.
4. En la página **Editar dispositivo gemelo**, escriba los detalles de las **propiedades deseadas** y las **etiquetas** que se van a editar y, después, haga clic en **Programación**:
   
   ![Configurar trabajo de método][img-edittwinjob]

### <a name="monitor-the-job"></a>Supervisión del trabajo
En la página **Trabajos de administración** se puede supervisar el estado de los trabajos que se programan. El panel **Detalles del trabajo** muestra información acerca del trabajo seleccionado:
   
   ![Ver estado del trabajo][img-jobstatus]

La información acerca de los trabajos también se puede ver en el **panel**:
   
   ![Ver trabajos en el panel][img-jobdashboard]


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
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha implementado una solución preconfigurada de trabajo, puede continuar su introducción al Conjunto de aplicaciones de IoT con la lectura de los artículos siguientes:

* [Tutorial de la solución preconfigurada de supervisión remota][lnk-rm-walkthrough]
* [Conectar el dispositivo a la solución preconfigurada de supervisión remota][lnk-connect-rm]
* [Permisos en el sitio azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-devicemethods]: media/iot-suite-getstarted-preconfigured-solutions/devicemethods.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-pingmethod]: media/iot-suite-getstarted-preconfigured-solutions/pingmethod.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit2.png
[img-sortdevices]: media/iot-suite-getstarted-preconfigured-solutions/sortdevices.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-clickjobscheduler]: media/iot-suite-getstarted-preconfigured-solutions/clickscheduler.png
[img-invokemethodjob]: media/iot-suite-getstarted-preconfigured-solutions/invokemethodjob.png
[img-edittwinjob]: media/iot-suite-getstarted-preconfigured-solutions/edittwinjob.png
[img-jobstatus]: media/iot-suite-getstarted-preconfigured-solutions/jobstatus.png
[img-jobdashboard]: media/iot-suite-getstarted-preconfigured-solutions/jobdashboard.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-device-twin-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
