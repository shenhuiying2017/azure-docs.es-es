---
title: "Introducción a la solución de mantenimiento predictivo: Azure | Microsoft Docs"
description: "Descripción de la solución preconfigurada de mantenimiento predictivo del Conjunto de aplicaciones de IoT de Azure."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: b370b3d7-2ce5-4906-9818-3aeedd471ee3
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 36cae39b7eaa0aff5f47f6a2511c7a0593f70b26
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="predictive-maintenance-preconfigured-solution-overview"></a>Información general de la solución preconfigurada de mantenimiento predictivo

La [solución preconfigurada][lnk_preconfigured_solutions] de *mantenimiento predictivo* es una de las soluciones preconfiguradas del [Conjunto de aplicaciones de IoT de Microsoft Azure][lnk_iot_suite]. Esta solución integra la colección de telemetría del dispositivo en tiempo real con un modelo predictivo creado mediante [Azure Machine Learning][lnk-machine-learning].

Con el Conjunto de aplicaciones de IoT de Azure, puede conectarse de forma rápida y fácil a los recursos, supervisarlos y analizar datos en tiempo real en paneles y visualizaciones. En la solución de mantenimiento predictivo, las visualizaciones y paneles le proporcionan una nueva inteligencia que puede impulsar las eficiencias y mejorar los flujos de ingresos.

## <a name="the-scenario"></a>Escenario

Fabrikam es una compañía de líneas aéreas regional que se centra en la fantástica experiencia del cliente en los precios competitivos. Una causa de los retrasos en los vuelos son los problemas de mantenimiento y el mantenimiento de los motores de los aviones supone especialmente un desafío. Fabrikam debe evitar los errores en los motores durante el vuelo a toda costa, por lo que inspecciona sus motores con regularidad y se ajusta a un programa de mantenimiento programado. Sin embargo, los motores de aviones no siempre llevan el mismo. En los motores se realizan algunas tareas de mantenimiento innecesarias. Lo que es aún más importante, surgen problemas que pueden hacer que un avión quede inmovilizado hasta que se realice el mantenimiento. Si un avión está en una ubicación donde no se encuentran disponibles los técnicos o las piezas de repuesto adecuados, estos problemas pueden resultar muy costosos.

Los motores de los aviones de Fabrikam están equipados con sensores que supervisan las condiciones del motor durante el vuelo. Fabrikam utiliza la solución de mantenimiento predictivo para recopilar los datos del sensor recopilados durante el vuelo. Tras años de acumular datos de errores y de funcionamiento del motor, los científicos de datos de Fabrikam han modelado una manera de predecir la vida útil restante (RUL) del motor de un avión. El modelo emplea una correlación entre los datos de cuatro de los sensores del motor con el desgaste del motor que lleva a averías ocasionales. Mientras Fabrikam continúa realizando inspecciones periódicas para garantizar la seguridad, ahora puede usar los modelos para calcular el RUL de cada motor después de cada vuelo. El modelo usa la telemetría recopilada por los motores durante el vuelo. Fabrikam puede predecir ahora de antemano los puntos futuros de error y el plan de mantenimiento y reparación.

> [!NOTE]
> El modelo de solución utiliza datos de desgaste del motor reales.

Al predecir el punto en el que se requiere mantenimiento, Fabrikam puede optimizar sus operaciones para reducir los costos.

Los coordinadores de mantenimiento trabajan con programadores para:

- Planear el mantenimiento de forma que coincida con la parada de un avión en una ubicación concreta.
- Asegurarse de que el avión esté fuera de servicio el tiempo suficiente sin provocar interrupciones en la programación.
- Para programar a los técnicos de forma que se tenga la seguridad de que el avión es revisado de forma eficiente sin tiempo de espera.

Los administradores del control de inventario reciben planes de mantenimiento para poder optimizar su proceso de realización de pedidos y el inventario de las piezas de repuesto.

Todas estas actividades permiten a Fabrikam minimizar el tiempo de permanencia en tierra de los aviones y reducir los costos operativos a la vez que se garantizan la seguridad de los pasajeros y de la tripulación.

Para entender la manera en que el [Conjunto de aplicaciones de IoT de Azure][lnk_iot_suite] ofrece las funcionalidades que los clientes necesitan para alcanzar el potencial del mantenimiento predictivo, consulte esta [infografía][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-is-built"></a>Cómo se crea la solución de un mantenimiento predictivo

La solución aprovecha un modelo de Azure Machine Learning existente disponible como plantilla para mostrar cómo actúan estas funcionalidades a partir de la telemetría del dispositivo recopilada mediante los servicios del Conjunto de aplicaciones de IoT. Microsoft ha creado un [modelo de regresión][lnk_regression_model] de un motor de avión basado en los datos disponibles públicamente<sup>\[1\]</sup> y las instrucciones paso a paso sobre cómo utilizar el modelo.

La solución de mantenimiento predictivo de Azure IoT usa el modelo de regresión creado a partir de esta plantilla. El modelo se implementa en la suscripción de Azure y se expone mediante una API generada automáticamente. La solución incluye un subconjunto de los datos de prueba que representa 4 motores (de un total de 100) y 4 flujos de datos de sensor (de un total de 21). Estos datos son suficientes para proporcionar un resultado preciso del modelo entrenado.

*\[1\] A. Saxena and K. Goebel (2008). "Conjunto de datos de simulación de degradación del motor de turbofán", NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## <a name="get-started-with-predictive-maintenance"></a>Introducción al mantenimiento predictivo

En este tutorial se muestra cómo aprovisionar la solución de mantenimiento predictivo. También le lleva por las características básicas de la solución de mantenimiento predictivo. Puede acceder a muchas de estas características mediante el panel de soluciones que se implementa junto con la solución preconfigurada.

Para completar este tutorial, deberá tener una suscripción activa de Azure.

> [!NOTE]
> En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte la [evaluación gratuita de Azure][lnk_free_trial].

1. Inicie sesión en [azureiotsuite.com][lnk-azureiotsuite] con sus credenciales de la cuenta de Azure y haga clic en **+** para crear una nueva solución.
1. Haga clic en **Seleccionar** en el icono de **Mantenimiento predictivo**.
1. Escriba un **nombre** para la solución preconfigurada de mantenimiento predictivo.
1. Seleccione la **región** y la **suscripción** que desea usar para aprovisionar la solución.
1. Haga clic en **Crear solución** para comenzar el proceso de aprovisionamiento. Este proceso normalmente tarda varios minutos en ejecutarse.

### <a name="wait-for-the-provisioning-process-to-complete"></a>Espere a que se complete el proceso de aprovisionamiento

1. Haga clic en el icono de la solución con el estado **Aprovisionando**.
1. Observe los **estados de aprovisionamiento** , ya que los servicios de Azure se implementan en su suscripción de Azure.
1. Una vez que se complete el aprovisionamiento, el estado cambia a **Listo**.
1. Haga clic en el icono y verá los detalles de la solución en el panel derecho. En este panel, puede iniciar el panel de soluciones y acceder al área de trabajo de Machine Learning.

> [!NOTE]
> Si surgen problemas al implementar la solución preconfigurada, revise las secciones [Permisos en el sitio azureiotsuite.com][lnk-permissions] y [Preguntas más frecuentes][lnk-faq]. Si los problemas persisten, cree un vale de servicio en el [portal][lnk-portal].

¿Hay detalles que esperaría ver que no aparezcan para su solución? Realice sugerencias sobre características en [User Voice](https://feedback.azure.com/forums/321918-azure-iot) (Voz del usuario).

## <a name="view-the-solution"></a>Visualización de la solución

Esta sección le guía por la interfaz de usuario de la solución.

### <a name="predictive-maintenance-dashboard"></a>Panel de mantenimiento predictivo 

Esta página de la aplicación web utiliza controles de Power BI JavaScript (consulte el [repositorio de imágenes de Power BI][lnk-powerbi]) para ver:

* Los datos de salida de los trabajos de Stream Analytics en el almacenamiento de blobs.
* El recuento de ciclos y la vida útil restante por cada motor de avión.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Observación del comportamiento de la solución en la nube

En el Portal de Azure, desplácese al grupo de recursos con el nombre de la solución elegida para poder ver los recursos aprovisionados.

![][img-resource-group]

Al aprovisionar la solución preconfigurada, recibirá un mensaje de correo electrónico con un vínculo al área de trabajo de Machine Learning. También puede navegar al área de trabajo de Machine Learning desde la página [azureiotsuite.com][lnk-azureiotsuite] de la solución de aprovisionamiento. Hay un icono disponible en esta página cuando la solución se encuentra en el estado **Listo**.

![][img-machine-learning]

En el portal de la solución, puede ver que el ejemplo se aprovisiona con cuatro dispositivos simulados que representan dos aviones con dos motores por avión y cuatro sensores por motor. Cuando va al portal de la solución por primera vez, se detiene la simulación.

![][img-simulation-stopped]

Haga clic en **Iniciar simulación** para empezar la simulación. El historial del sensor, la vida útil restante, los ciclos y el historial de vida útil restante rellenan con datos el panel.

![][img-simulation-running]

Si la vida útil restante es inferior a 160 (un umbral arbitrario elegido para fines de demostración), el portal de la solución muestra un símbolo de advertencia junto a la presentación de la vida útil restante. El portal de la solución también resalta en amarillo el motor del avión. Observe cómo los valores de la vida útil restante presentan una tendencia descendente en general pero tienden a rebotar hacia arriba y hacia abajo. Este comportamiento es consecuencia de las longitudes del ciclo que varían y de la precisión del modelo.

![][img-simulation-warning]

La simulación completa tarda alrededor de 35 minutos en finalizar 148 ciclos. Se alcanza el umbral de vida útil restante de 160 por primera vez en unos 5 minutos y ambos motores alcanzan el umbral en aproximadamente unos 8 minutos.

La simulación ejecuta el conjunto de datos completo para 148 ciclos y se establece en los valores finales de la vida útil existente y de ciclos.

Puede detener la simulación en cualquier punto, pero al hacer clic en **Iniciar simulación** se reproduce la simulación desde el principio del conjunto de datos.

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de cómo IoT de Azure habilita escenarios de mantenimiento predictivo, lea el documento sobre [Captura del valor de Internet de las cosas][lnk_capture_value].

Examine el [tutorial][lnk-predictive-walkthrough] de la solución de mantenimiento predictivo.

También puede explorar algunas de las demás características y funcionalidades de las soluciones preconfiguradas del conjunto de aplicaciones de IoT:

* [Preguntas más frecuentes sobre el Conjunto de aplicaciones de IoT][lnk-faq]
* [Seguridad de Internet de las cosas desde el principio][lnk-security-groundup]

[img-resource-group]: media/iot-suite-predictive-overview/resource-group.png
[img-simulation-stopped]: media/iot-suite-predictive-overview/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-overview/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-overview/simulation-warning.png
[img-machine-learning]: media/iot-suite-predictive-overview/machine-learning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-options.md
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3

[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/