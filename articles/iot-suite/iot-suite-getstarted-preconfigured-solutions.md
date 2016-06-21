<properties
	pageTitle="Introducción a las soluciones preconfiguradas | Microsoft Azure"
	description="Siga este tutorial para aprender a implementar una solución preconfigurada del conjunto de IoT de Azure."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="05/25/2016"
     ms.author="dobett"/>

# Tutorial: Introducción a las soluciones preconfiguradas

## Introducción

Las [soluciones preconfiguradas][lnk-preconfigured-solutions] del Conjunto de aplicaciones de IoT de Azure combinan varios servicios de IoT de Azure para ofrecer soluciones integrales que implementan escenarios de negocio de IoT comunes.

Este tutorial muestra cómo aprovisionar la solución preconfigurada de *supervisión remota*. También guía por las características básicas de la solución preconfigurada de supervisión remota.

Para completar este tutorial, deberá tener una suscripción de Azure activa.

> [AZURE.NOTE]  En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][lnk_free_trial].

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## Visualización del panel de la solución de supervisión remota

El panel de la solución permite administrar la solución implementada. Por ejemplo, puede ver la telemetría, agregar dispositivos y configurar las reglas.

1.  Cuando se complete el aprovisionamiento y el icono de su solución preconfigurada indique **Listo**, haga clic en **Iniciar** para abrir el portal de su solución de supervisión remota en una nueva pestaña.

    ![][img-launch-solution]

2.  De forma predeterminada, el portal de la solución muestra el *panel de la solución*. Puede seleccionar otras vistas mediante el menú izquierdo.

    ![][img-dashboard]

El panel muestra la siguiente información:

- El mapa muestra la ubicación de cada dispositivo conectado a la solución. La primera vez que se ejecuta la solución hay cuatro dispositivos simulados. Los dispositivos simulados se implementan como WebJobs de Azure y la solución utiliza la API de Mapas de Bing para trazar la información en el mapa.
- El panel **Telemetry History** (Historial de telemetría) traza los datos de telemetría de temperatura y humedad de un dispositivo seleccionado prácticamente en tiempo real y muestra los datos agregados, como la humedad máxima, mínima y media.
- El panel **Alarm History** (Historial de alarmas) muestra los últimos eventos de alarma cuando un valor de telemetría ha superado un umbral. Puede definir sus propias alarmas, además de los ejemplos que crea la solución preconfigurada.

## Visualización de la lista de dispositivos de la solución

La lista de dispositivos muestra todos los dispositivos registrados en la solución. Puede ver y editar los metadatos de los dispositivos, agregar o quitar dispositivos y enviar comandos a los dispositivos.

1.  Haga clic en **Dispositivos** en el menú izquierdo para mostrar la *lista de dispositivos* para esta solución.

    ![][img-devicelist]

2.  La lista de dispositivos muestra que hay cuatro dispositivos simulados creados por el proceso de aprovisionamiento.

3.  Haga clic en un dispositivo en la lista de dispositivos para ver sus detalles.

    ![][img-devicedetails]

El panel **Device Details** (Detalles de dispositivo) contiene tres secciones:

- En la sección **Actions** (Acciones) se enumeran las acciones que se pueden realizar en el dispositivo. Si se deshabilita el dispositivo, no se podrán enviar datos de telemetría ni recibir comandos. Aunque se deshabilite un dispositivo, se puede volver a habilitar posteriormente. Se puede agregar una regla asociada con el dispositivo que desencadena una alarma cuando un valor de telemetría supera un umbral. También se puede enviar un comando a un dispositivo. La primera vez que un dispositivo se conecta indica a la solución los comandos a los que puede responder.
- En la sección **Device Properties** (Propiedades de dispositivo) se enumeran los metadatos del dispositivo. Algunos de estos metadatos proceden del propio dispositivo (como por ejemplo, el fabricante), mientras que otros los genera la solución (como la hora de creación). Los metadatos del dispositivo se pueden editar desde aquí.
- En la sección **Authentication Key** (Clave de autenticación) se enumeran las claves que puede usar el dispositivo para autenticarse en la solución.

## Envío de un comando a un dispositivo

El panel de detalles del dispositivo muestra todos los comandos que admite un dispositivo concreto y le permite enviar comandos a un dispositivo. La primera vez que se inicia un dispositivo, este envía a la solución información acerca de los comandos que admite.

1.  Haga clic en **Comandos** en el panel de detalles del dispositivo para el dispositivo seleccionado.

    ![][img-devicecommands]

2.  Seleccione **PingDevice** en la lista de comandos.

3.  Haga clic en **Enviar comando**.

4.  Puede ver el estado del comando en el historial de comandos.

    ![][img-pingcommand]

La solución hace un seguimiento del estado de cada comando que envía. Inicialmente, el resultado es **Pending** (Pendiente). Cuando el dispositivo informa de que ha ejecutado el comando, el resultado se establece en **Success** (Correcto).

## Adición de un nuevo dispositivo simulado

1.  Vuelva a navegar hasta la lista de dispositivos.

2.  Haga clic en **+ Agregar un dispositivo** en la esquina inferior izquierda para agregar un nuevo dispositivo.

    ![][img-adddevice]

3.  Haga clic en **Agregar nuevo** en el icono **Dispositivo simulado**.

    ![][img-addnew]
    
    Si elige crear un **dispositivo personalizado**, además de crear un dispositivo simulado nuevo, también puede agregar un dispositivo físico. Para más información al respecto, consulte [Conectar el dispositivo a la solución preconfigurada de supervisión remota (Windows)][lnk-connecting-devices].

4.  Seleccione **Permitirme definir mi propio identificador de dispositivo** y escriba un nombre de identificador de dispositivo único como **mydevice\_01**.

5.  Haga clic en **Crear**.

    ![][img-definedevice]

6. En el paso 3 de **Agregar un dispositivo simulado**, haga clic en **Listo** para volver a la lista de dispositivos.

7. Puede ver su dispositivo **En ejecución** en la lista de dispositivos.

    ![][img-runningnew]

8. También puede ver la telemetría simulada del dispositivo nuevo en el panel:

    ![][img-runningnew-2]

## Edición de los metadatos del dispositivo

1.  Vuelva a navegar hasta la lista de dispositivos.

2.  Seleccione el nuevo dispositivo en la **lista de dispositivos** y haga clic en **Editar** para editar las **propiedades del dispositivo**:

    ![][img-editdevice]

3. Desplácese hacia abajo y realice un cambio en los valores de latitud y longitud. Luego, haga clic en **Save changes to device registry** (Guardar cambios en registro de dispositivo).

    ![][img-editdevice2]

4. Vuelva a navegar al panel y observará que ha cambiado la ubicación del dispositivo en el mapa:

    ![][img-editdevice3]

## Adición de una regla para el nuevo dispositivo

No hay reglas para el nuevo dispositivo que acaba de agregar. En esta sección, agregará una regla que desencadenará una alarma cuando la temperatura notificada por el nuevo dispositivo supere los 47 grados. Antes de comenzar, observe que el historial de telemetría del nuevo dispositivo en el panel muestra que la temperatura de dispositivo nunca supera los 45 grados.

1.  Vuelva a navegar hasta la lista de dispositivos.

2.  Seleccione el dispositivo nuevo en la **lista de dispositivos** y haga clic en **Agregar regla** para agregar una regla nueva al dispositivo.

3. Cree una regla que utilice **Temperature** como campo de datos y **AlarmTemp** como resultado cuando la temperatura supere los 47 grados:

    ![][img-adddevicerule]

4. Haga clic en **Save and View Rules** (Guardar y ver reglas) para guardar los cambios.

5.  Haga clic en **Commands** (Comandos) en el panel de detalles del dispositivo nuevo.

    ![][img-adddevicerule2]

6.  Seleccione **ChangeSetPointTemp** en la lista de comandos y en **SetPointTemp** seleccione 45. Después, haga clic en **Send Command** (Enviar comando):

    ![][img-adddevicerule3]

7.  Vuelva a navegar al panel de la solución. Al poco tiempo, verá una entrada nueva en el panel **Alarm History** (Historial de alarmas) cuando la temperatura que notifique el nuevo dispositivo supere el umbral de 47 grados:

    ![][img-adddevicerule4]

8. Todas las reglas se pueden revisar y editar en la página **Rules** (Reglas) del panel:

    ![][img-rules]

9. Todas las acciones que se pueden realizar en respuesta a una regla se pueden revisar y editar en la página **Actions** (Acciones) del panel:

    ![][img-actions]

> [AZURE.NOTE] Es posible definir las acciones que pueden enviar un mensaje de correo electrónico o un SMS en respuesta a una regla, o bien integrarse en un sistema de línea de negocio a través de una [aplicación lógica][lnk-logic-apps].

## Entre bambalinas

Al implementar una solución preconfigurada, el proceso de implementación crea varios recursos en la suscripción de Azure seleccionada. Dichos recursos se pueden ver en el [Portal][lnk-portal] de Azure. El proceso de implementación crea un **grupo de recursos** cuyo nombre se basa en el nombre que eligió para la solución preconfigurada:

![][img-portal]

Para ver la configuración de cada recurso, selecciónelo en la lista de recursos del grupo de recursos. La captura de pantalla anterior muestra la configuración del Centro de IoT usada en la solución preconfigurada.

También puede ver el código fuente de la solución preconfigurada. El código fuente de la solución preconfigurada de supervisión remota está en [azure-iot-remote-monitoring][lnk-rmgithub]\:

- La carpeta **DeviceAdministration** contiene el código fuente del panel.
- La carpeta **Simulator** contiene el código fuente del dispositivo simulado.
- La carpeta **EventProcessor** contiene el código fuente del proceso de back-end que controla los datos de telemetría entrantes.

Cuando haya terminado, puede eliminar la solución preconfigurada de la suscripción de Azure en el sitio [azureiotsuite.com][lnk-azureiotsuite] \(esto le permite eliminar fácilmente todos los recursos que se aprovisionaron cuando se creó la solución preconfigurada).

> [AZURE.NOTE] Para asegurarse de que elimina todo lo relacionado con la solución preconfigurada, elimínelo de m [azureiotsuite.com][lnk-azureiotsuite], no elimine simplemente el grupo de recursos en el portal.

## Pasos siguientes

Ahora que ha creado una solución preconfigurada que funciona, puede pasar a los tutoriales siguientes:

-   [Directrices de personalización de soluciones preconfiguradas][lnk-customize]
-   [Información general de la solución preconfigurada de mantenimiento predictivo][lnk-predictive]

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

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md
[lnk-connecting-devices]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-faq]: iot-suite-faq.md

<!---HONumber=AcomDC_0615_2016-->