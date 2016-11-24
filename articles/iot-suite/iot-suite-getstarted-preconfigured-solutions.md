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
ms.date: 11/16/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 1a6dd35278f0a4a4f972642c40a0976986dd79ae
ms.openlocfilehash: 79c9f2d8c683be493160fffe40992006b039f60f


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Tutorial: Introducción a las soluciones preconfiguradas
## <a name="introduction"></a>Introducción
Las [soluciones preconfiguradas][lnk-preconfigured-solutions] del Conjunto de aplicaciones de IoT de Azure combinan varios servicios de IoT de Azure para ofrecer soluciones integrales que implementan escenarios de negocio de IoT comunes. La solución preconfigurada de *supervisión remota* permite conectar y supervisar los dispositivos. Puede utilizar la solución para analizar la transmisión de datos de los dispositivos y mejorar los resultados de negocio realizando procesos para responder automáticamente a esa transmisión de datos.

Este tutorial muestra cómo aprovisionar la solución preconfigurada de supervisión remota. También le guía por las características básicas de la solución de supervisión remota. Puede acceder a muchas de estas características a través del panel de soluciones que se implementa junto con la solución preconfigurada:

![Panel de la solución preconfigurada de supervisión remota][img-dashboard]

Para completar este tutorial, deberá tener una suscripción activa de Azure.

> [!NOTE]
> En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte [Evaluación gratuita de Azure][lnk_free_trial].
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

## <a name="view-the-device-list"></a>Visualización de la lista de dispositivos
La lista de dispositivos muestra todos los dispositivos registrados en la solución. Puede ver y editar los metadatos de los dispositivos, agregar o quitar dispositivos y enviar comandos a los dispositivos.

1. Haga clic en **Dispositivos** en el menú izquierdo para mostrar la *lista de dispositivos* para esta solución.
   
   ![Lista de dispositivos en el panel][img-devicelist]
2. La lista de dispositivos muestra que hay cuatro dispositivos simulados creados por el proceso de aprovisionamiento.
3. Haga clic en un dispositivo en la lista de dispositivos para ver sus detalles.
   
   ![Detalles de los dispositivos en el panel][img-devicedetails]

El panel **Detalles del dispositivo** contiene tres secciones:

* En la sección **Acciones** se enumeran las acciones que se pueden realizar en el dispositivo. Si se deshabilita el dispositivo, no se podrán enviar datos de telemetría ni recibir comandos. Aunque se deshabilite un dispositivo, se puede volver a habilitar posteriormente. Se puede agregar una regla asociada con el dispositivo que desencadena una alarma cuando un valor de telemetría supera un umbral. También se puede enviar un comando a un dispositivo. La primera vez que un dispositivo se conecta indica a la solución los comandos a los que puede responder.
* En la sección **Propiedades de dispositivo** se enumeran los metadatos del dispositivo. Algunos de estos metadatos proceden del propio dispositivo (como por ejemplo, el fabricante), mientras que otros los genera la solución (como la hora de creación). Los metadatos del dispositivo se pueden editar desde aquí.
* En la sección **Clave de autenticación** se enumeran las claves que puede usar el dispositivo para autenticarse en la solución.

## <a name="send-a-command-to-a-device"></a>Envío de un comando a un dispositivo
El panel de detalles del dispositivo muestra todos los comandos que admite un dispositivo concreto y le permite enviar comandos a un dispositivo. La primera vez que se inicia un dispositivo, este envía a la solución información acerca de los comandos que admite.

1. Haga clic en **Comandos** en el panel de detalles del dispositivo para el dispositivo seleccionado.
   
   ![Comandos de los dispositivos en el panel][img-devicecommands]
2. Seleccione **PingDevice** en la lista de comandos.
3. Haga clic en **Enviar comando**.
4. Puede ver el estado del comando en el historial de comandos.
   
   ![Estado de los comandos en el panel][img-pingcommand]

La solución hace un seguimiento del estado de cada comando que envía. Inicialmente, el resultado es **Pendiente**. Cuando el dispositivo informa de que ha ejecutado el comando, el resultado se establece en **Correcto**.

## <a name="add-a-new-simulated-device"></a>Adición de un nuevo dispositivo simulado
Al implementar la solución preconfigurada, aprovisiona automáticamente los cuatro dispositivos de ejemplo que se pueden ver en la lista de dispositivos. Estos dispositivos son *dispositivos simulados* que se ejecutan en un WebJob de Azure. Los dispositivos simulados facilitan la experimentación de la solución preconfigurada sin necesidad de implementar dispositivos físicos reales. Si desea conectar un dispositivo real a la solución, consulte el tutorial [Conectar el dispositivo a la solución preconfigurada de supervisión remota (Windows)][lnk-connect-rm].

Los pasos siguientes explican cómo agregar un nuevo dispositivo simulado a la solución:

1. Vuelva a navegar hasta la lista de dispositivos.
2. Haga clic en **+ Agregar un dispositivo** en la esquina inferior izquierda para agregar un nuevo dispositivo.
   
   ![Agregar un dispositivo a la solución preconfigurada][img-adddevice]
3. Haga clic en **Agregar nuevo** en el icono **Simulated Device** (Dispositivo simulado).
   
   ![Establecer nuevos detalles de los dispositivos en el panel][img-addnew]
   
   Si elige crear un **dispositivo personalizado**, además de crear un dispositivo simulado nuevo, también puede agregar un dispositivo físico. Para más información acerca de cómo conectar dispositivos físicos a la solución, consulte [Conectar el dispositivo a la solución preconfigurada de supervisión remota (Windows)][lnk-connect-rm].
4. Seleccione **Let me define my own Device ID** (Permitirme definir mi propio identificador de dispositivo) y escriba un nombre de identificador de dispositivo único como **mydevice_01**.
5. Haga clic en **Crear**.
   
   ![Guardar un dispositivo nuevo][img-definedevice]
6. En el paso 3 de **Add a simulated device** (Agregar un dispositivo simulado), haga clic en **Listo** para volver a la lista de dispositivos.
7. Puede ver su dispositivo **En ejecución** en la lista de dispositivos.
   
    ![Ver un dispositivo nuevo en la lista de dispositivos][img-runningnew]
8. También puede ver la telemetría simulada del dispositivo nuevo en el panel:
   
    ![Ver telemetría desde el nuevo dispositivo][img-runningnew-2]

## <a name="edit-the-device-metadata"></a>Edición de los metadatos del dispositivo
Cuando el dispositivo se conecta por primera vez a la solución envía sus metadatos a esta. Al editar los metadatos del dispositivo mediante el panel de soluciones, envía los nuevos valores de metadatos al dispositivo y los almacena en la base de datos de DocumentDB. Para más información, consulte [Registro de identidades de dispositivo y DocumentDB][lnk-devicemetadata].

1. Vuelva a navegar hasta la lista de dispositivos.
2. Seleccione el nuevo dispositivo en la **lista de dispositivos** y haga clic en **Editar** para editar las **propiedades del dispositivo**:
   
   ![Editar los metadatos del dispositivo][img-editdevice]
3. Desplácese hacia abajo y realice un cambio en los valores de latitud y longitud. Luego, haga clic en **Guardar cambios en registro de dispositivos**.
   
    ![Editar los metadatos del dispositivo][img-editdevice2]
4. Vuelva a navegar al panel y observará que ha cambiado la ubicación del dispositivo en el mapa:
   
    ![Editar los metadatos del dispositivo][img-editdevice3]

## <a name="add-a-rule-for-the-new-device"></a>Adición de una regla para el nuevo dispositivo
No hay reglas para el nuevo dispositivo que acaba de agregar. En esta sección, agregará una regla que desencadenará una alarma cuando la temperatura notificada por el nuevo dispositivo supere los 47 grados. Antes de comenzar, observe que el historial de telemetría del nuevo dispositivo en el panel muestra que la temperatura de dispositivo nunca supera los 45 grados.

1. Vuelva a navegar hasta la lista de dispositivos.
2. Seleccione el dispositivo nuevo en la **lista de dispositivos** y haga clic en **Add rule** (Agregar regla) para agregar una regla al dispositivo.
3. Cree una regla que utilice **Temperature** como campo de datos y **AlarmTemp** como resultado cuando la temperatura supere los 47 grados:
   
    ![Agregar una regla de dispositivo][img-adddevicerule]
4. Haga clic en **Guardar y ver reglas** para guardar los cambios.
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
> Es posible definir las acciones que pueden enviar un mensaje de correo electrónico o un SMS en respuesta a una regla, o bien integrarse en un sistema de línea de negocio a través de una [aplicación lógica][lnk-logic-apps]. Para más información, consulte [Tutorial: Connect Logic App to your Azure IoT Suite Remote Monitoring preconfigured solution] (Tutorial: conexión de una aplicación lógica a la solución preconfigurada de supervisión remota del Conjunto de aplicaciones de IoT de Azure)[lnk-logicapptutorial].
> 
> 

## <a name="other-features"></a>Otras características
Con el portal de soluciones, se puede buscar dispositivos con características específicas, como un número de modelo:

![Buscar un dispositivo][img-search]

Puede deshabilitar un dispositivo y, después, quitarlo:

![Deshabilitar y quitar un dispositivo][img-disable]

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
> Para asegurarse de que elimina todo lo relacionado con la solución preconfigurada, hágalo en el sitio [azureiotsuite.com][lnk-azureiotsuite] y no elimine el grupo de recursos en el portal.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha implementado una solución preconfigurada de trabajo, puede continuar su introducción al Conjunto de aplicaciones de IoT con la lectura de los artículos siguientes:

* [Tutorial de la solución preconfigurada de supervisión remota][lnk-rm-walkthrough]
* [Conectar el dispositivo a la solución preconfigurada de supervisión remota (Windows)][lnk-connect-rm]
* [Permisos en el sitio azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
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
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md



<!--HONumber=Nov16_HO3-->


