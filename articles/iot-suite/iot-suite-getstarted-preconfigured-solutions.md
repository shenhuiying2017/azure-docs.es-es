<properties
	pageTitle="Introducción a las soluciones preconfiguradas | Microsoft Azure"
	description="Siga este tutorial para aprender a implementar una solución preconfigurada del conjunto de IoT de Azure."
	services=""
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/03/2015"
     ms.author="dobett"/>

# Tutorial: Introducción a las soluciones de IoT preconfiguradas

## Introducción

Las [soluciones preconfiguradas][lnk-preconfigured-solutions] del Conjunto de aplicaciones de IoT de Azure combinan varios servicios de IoT de Azure para ofrecer soluciones integrales que implementan escenarios de negocio de IoT comunes.

Este tutorial muestra cómo aprovisionar la solución preconfigurada de *supervisión remota*. También guía por las características básicas de la solución preconfigurada de supervisión remota.

Para completar este tutorial, deberá tener una suscripción de Azure activa.

> [AZURE.NOTE]En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][lnk_free_trial].

## Aprovisionamiento de la solución preconfigurada de supervisión remota

1.  Inicie sesión en [azureiotsuite.com][lnk-azureiotsuite] con sus credenciales de la cuenta de Azure y haga clic en **+** para crear una nueva solución.

2.  Haga clic en **Seleccionar** en el icono **Supervisión remota**.

3.  Escriba un valor en **Nombre de la solución** para la solución preconfigurada de supervisión remota.

4.  Seleccione el valor en **Región** y **Suscripción** que desea usar para aprovisionar la solución.

5.  Haga clic en **Crear solución** para comenzar el proceso de aprovisionamiento. Normalmente tarda varios minutos en ejecutarse.

## Visualización del panel de la solución de supervisión remota

1.  Cuando se complete el aprovisionamiento y el icono de su solución preconfigurada indique **Listo**, haga clic en **Iniciar** para abrir el portal de su solución de supervisión remota en una nueva pestaña.

    ![][img-launch-solution]

2.  De forma predeterminada, el portal de la solución muestra el *panel de la solución*. Puede seleccionar otras vistas mediante el menú izquierdo.

    ![][img-dashboard]

## Visualización de la lista de dispositivos de la solución

1.  Haga clic en **Dispositivos** en el menú izquierdo para mostrar la *lista de dispositivos* para esta solución.

    ![][img-devicelist]

2.  Puede ver que hay cuatro dispositivos simulados creados por el proceso de aprovisionamiento.

3.  Haga clic en un dispositivo en la lista de dispositivos para ver sus detalles.

    ![][img-devicedetails]

## Envío de un comando a un dispositivo

1.  Haga clic en **Comandos** en el panel de detalles del dispositivo para el dispositivo seleccionado.

    ![][img-devicecommands]

2.  Seleccione **PingDevice** en la lista de comandos.

3.  Haga clic en **Enviar comando**.

4.  Puede ver el estado del comando en el historial de comandos.

    ![][img-pingcommand]

## Adición de un nuevo dispositivo simulado

1.  Vuelva a navegar hasta la lista de dispositivos.

2.  Haga clic en **+ Agregar un dispositivo** en la esquina inferior izquierda para agregar un nuevo dispositivo.

    ![][img-adddevice]

3.  Haga clic en **Agregar nuevo** en el icono **Dispositivo simulado**.

    ![][img-addnew]

4.  Seleccione **Permitirme definir mi propio identificador de dispositivo** y escriba un nombre de identificador de dispositivo único como **mydevice\_01**.

5.  Haga clic en **Crear**.

    ![][img-definedevice]

6. En el paso 3 de **Agregar un dispositivo simulado**, haga clic en **Listo** para volver a la lista de dispositivos.

7.  Puede ver su dispositivo **En ejecución** en la lista de dispositivos.

    ![][img-runningnew]

## Visualización y edición de reglas de solución

1.  Vuelva al panel de la solución y vea el icono **Historial de alarmas**.

    ![][img-alarmhistory]

2.  La regla **AlarmTemp** desencadena estas alarmas.

3.  Haga clic en **Reglas** en el menú izquierdo para ver las reglas de esta solución.

    ![][img-rules]

4.  La solución preconfigurada aprovisiona dos reglas.

5.  Haga clic en la regla **Temperature** en la lista de reglas para ver las propiedades de la regla.

6.  Haga clic en **Editar** en el panel de propiedades de la regla.

    ![][img-displayrule]

7.  Cambie el valor de **Umbral** a 30 y conserve todas las demás propiedades.

8.  Haga clic en **Guardar y ver reglas**.

    ![][img-editrule]

9.  Vuelva a la tabla **Historial de alarmas** en el **Panel de la solución** y observe el cambio en el comportamiento que resulta de la regla actualizada.

    ![][img-newhistory]

## Pasos siguientes

Ahora que ha creado una solución preconfigurada que funciona, puede pasar a las situaciones siguientes:

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
[img-alarmhistory]: media/iot-suite-getstarted-preconfigured-solutions/alarmhistory.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-displayrule]: media/iot-suite-getstarted-preconfigured-solutions/displayrule.png
[img-editrule]: media/iot-suite-getstarted-preconfigured-solutions/editrule.png
[img-newhistory]: media/iot-suite-getstarted-preconfigured-solutions/newhistory.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_1210_2015-->