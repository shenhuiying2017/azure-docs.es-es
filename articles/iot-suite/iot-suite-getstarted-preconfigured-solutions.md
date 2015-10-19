<properties
	pageTitle="Tutorial de introducción a las soluciones preconfiguradas del conjunto de IoT de Microsoft Azure | Microsoft Azure"
	description="Siga este tutorial para aprender a implementar una solución preconfigurada del conjunto de IoT de Azure."
	services=""
	documentationCenter=".net"
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="araguila"/>

# Introducción a las soluciones de IoT preconfiguradas

## Introducción

Las soluciones preconfiguradas del conjunto de soluciones de IoT de Azure conectan varios servicios IoT de Azure para ejemplificar una solución completa que satisface un escenario empresarial de Internet de las cosas.

Este tutorial muestra cómo aprovisionar una solución preconfigurada, la **supervisión remota**. También muestra cómo ver las características básicas de la solución preconfigurada de supervisión remota.

Para completar este tutorial, necesitará lo siguiente:

-   Una suscripción de Azure activa.

    En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][].

## Aprovisionamiento de la solución preconfigurada de supervisión remota

1.  Inicie sesión en https://www.azureiotsuite.com y haga clic en **+** para crear una nueva solución.

2.  Seleccione **Supervisión remota** como el tipo de solución.

3.  Escriba un **Nombre de la solución** para la solución preconfigurada de supervisión remota.

4.  Valide la **Región** y la **Suscripción** que desea proporcionar en esta solución.

5.  Haga clic en **Crear solución**.

## Visualización del panel de la solución de supervisión remota

1.  Cuando se complete el aprovisionamiento y el icono de su solución preconfigurada indique **Listo**, haga clic en **Iniciar** para abrir el panel de su solución de supervisión remota en una nueva pestaña.

2.  De forma predeterminada, el **Panel** está seleccionado en el menú izquierdo. Este es el panel de su solución.

## Visualización de la lista de dispositivos de la solución

1.  Haga clic en **Dispositivos** en el menú izquierdo para ir a la lista de dispositivos para esta solución.

2.  En el aprovisionamiento, verá 4 dispositivos simulados aprovisionados.

3.  Haga clic en un **dispositivo individual** en la lista de dispositivos para ver los detalles asociados con dicho dispositivo.

## Envío de un comando a un dispositivo

1.  Haga clic en **Enviar comando** en el panel de detalles del dispositivo simulado seleccionado.

2.  Seleccione **PingDevice** en la lista de comandos.

3.  Haga clic en **Enviar comando**.

4.  Consulte el estado del comando que aparece en el historial de comandos.

## Adición de un nuevo dispositivo simulado

1.  Haga clic en **←** (flecha atrás) para volver a la lista de dispositivos.

2.  Haga clic en **+ Agregar un dispositivo** en la esquina inferior izquierda para agregar un nuevo dispositivo.

3.  Haga clic en **Agregar nuevo** para el **Dispositivo simulado**.

4.  Seleccione **Permitirme definir mi propio identificador de dispositivo** y agregue un nombre de identificador de dispositivo único.

5.  Haga clic en **Crear**.

6.  Haga clic en **←** (flecha atrás) para volver a la lista de dispositivos.

7.  Su dispositivo se mostrará **En ejecución** en la lista de dispositivos.

## Visualización y edición de reglas de solución

1.  Observe la tabla **Historial de alarmas** en el **Panel de la solución.**

2.  Estas alarmas se desencadenan con el resultado de la regla **AlarmTemp** especificado en **Reglas**.

3.  Haga clic en **Reglas** en el menú izquierdo para ir a las reglas de esta solución.

4.  Tras el aprovisionamiento verá que ya hay una regla habilitada.

5.  Haga clic en la **regla** en la lista de reglas para ver las propiedades asociadas.

6.  Haga clic en **Editar** en el panel de propiedades de la regla.

7.  Cambie el **Umbral** a 30 y conserve todas las demás propiedades.

8.  Haga clic en **Guardar y ver reglas**.

9.  Vuelva a la tabla **Historial de alarmas** en el **Panel de la solución** y observe el cambio en el desencadenador como resultado de la actualización de la regla.

## Pasos siguientes

Ahora que ha creado una solución preconfigurada que funciona, puede pasar a las situaciones siguientes:

-   [Directrices de personalización de soluciones preconfiguradas][]

-   [Información general del conjunto de IoT][]

[Evaluación gratuita de Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F%20target=

[Directrices de personalización de soluciones preconfiguradas]: https://azure.microsoft.com/documentation/articles/iot-suite-guidance-on-customizing-preconfigured-solutions/
[Información general del conjunto de IoT]: https://azure.microsoft.com/documentation/articles/iot-suite-overview/

<!---HONumber=Oct15_HO2-->