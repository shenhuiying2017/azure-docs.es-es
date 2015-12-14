<properties
 pageTitle="Solución preconfigurada de mantenimiento predictivo | Microsoft Azure"
 description="Descripción de la solución preconfigurada de mantenimiento predictivo de IoT de Azure IoT."
 services=""
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="na"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="12/01/2015"
 ms.author="stevehob"/>

# Información general de la solución preconfigurada de mantenimiento predictivo

La solución preconfigurada de *mantenimiento predictivo* es una de las [soluciones preconfiguradas][lnk_preconfigured_solutions] publicadas como parte del [Conjunto de IoT de Microsoft Azure][lnk_iot_suite]. Esta solución integra la colección de telemetría del dispositivo en tiempo real con un modelo predictivo creado mediante [aprendizaje automático de Azure][lnk_machine_learning].


Con el Conjunto de aplicaciones de IoT de Azure, las empresas pueden conectarse de forma rápida y con facilidad, supervisar los activos y analizar datos en tiempo real. La solución preconfigurada de mantenimiento predictivo toma los datos y usa visualizaciones y paneles enriquecidos para ofrecer a las empresas nueva inteligencia que puede impulsar las eficiencias y mejorar los flujos de ingresos.

## Escenario

Fabrikam es una compañía de líneas aéreas regional que se centra en la fantástica experiencia del cliente en los precios competitivos. Una causa de los retrasos en los vuelos son los problemas de mantenimiento y el mantenimiento de los motores de los aviones supone especialmente un desafío. Los errores en los motores durante el vuelo deben evitarse a toda costa, por lo que Fabrikam inspecciona sus motores con regularidad y se ajusta a un programa de mantenimiento programado. Sin embargo, los motores de aviones no siempre llevan el mismo. En los motores se realizan algunas tareas de mantenimiento innecesarias. Lo que es aún más importante, surgen problemas que pueden hacer que un avión quede inmovilizado hasta que se realice el mantenimiento. Esto provoca costosas demoras, especialmente si se trata de una aeronave en una ubicación donde no se encuentran disponibles los técnicos o las piezas de repuesto adecuados.

Los motores de los aviones de Fabrikam están equipados con sensores que supervisan las condiciones del motor durante el vuelo. Fabrikam usa el Conjunto de aplicaciones de IoT para recopilar los datos de sensor recopilados durante el vuelo. Tras años de acumular datos de errores y de funcionamiento del motor, los científicos de datos de Fabrikam han modelado una manera de predecir la vida útil restante (RUL) del motor de un avión. Lo que han identificado es una correlación entre los datos de cuatro de los sensores de motor con el desgaste del motor que lleva a errores ocasionales. Mientras Fabrikam continúa realizando inspecciones periódicas para garantizar la seguridad, ahora puede usar los modelos para calcular el RUL de cada motor después de cada vuelo empleando la telemetría recopilada de los motores durante el vuelo. Fabrikam puede predecir ahora de antemano los puntos futuros de error y el plan de mantenimiento y reparación.

Al predecir el punto en el que se requiere mantenimiento, Fabrikam puede optimizar sus operaciones para reducir los costos. Los coordinadores de mantenimiento trabajan con programadores para planear que el mantenimiento coincida con la parada de una aeronave en una ubicación concreta y para garantizar tiempo suficiente para que el avión esté fuera de servicio sin causar interrupciones en la programación. Fabrikam puede programar a los técnicos según corresponda, asegurando que el servicio a los aviones se presta de manera eficaz sin tiempo de espera. Los administradores del control de inventario reciben planes de mantenimiento para poder optimizar su proceso de realización de pedidos y el inventario de las piezas de repuesto. Todo esto permite a Fabrikam minimizar el tiempo de inoperatividad de los aviones y reducir los costos operativos a la vez que se garantizan la seguridad de los pasajeros y de la tripulación.

## Cómo se crea la solución de un mantenimiento predictivo

Para entender la manera en que el [Conjunto de aplicaciones de IoT de Azure][lnk_iot_suite] ofrece las capacidades que los clientes necesitan para obtener el potencial del mantenimiento predictivo, revise esta[infografía][lnk_infographic].

Para mostrar cómo funcionan estas capacidades desde la telemetría del dispositivo recopilada a través de los servicios del Conjunto de aplicaciones de IoT Suite y presentada a los usuarios en una aplicación web, la solución aprovecha un modelo de aprendizaje automático de Azure existente disponible como plantilla. Microsoft ha creado un modelo de regresión y ha publicado la plantilla completa, datos<sup>[1]</sup> e instrucciones paso a paso [aquí][lnk_regression_model].

La solución preconfigurada de mantenimiento predictivo de IoT de Azure usa el modelo de regresión creado a partir de esta plantilla; se implementa en su suscripción de Azure y se expone a través de una API publicada. La solución incluye un subconjunto de los datos de prueba que representa 4 (del total de 100) motores y 4 (del total de 21) flujos de datos de sensor que ofrecen un resultado exacto del modelo entrenado.

*[1] A. Saxena and K. Goebel (2008). "Conjunto de datos de simulación de degradación del motor de turbofán", NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*.

## Pasos siguientes

Para más información acerca de cómo IoT de Azure habilita escenarios de mantenimiento predictivo, lea [Capturar el valor del Internet de las cosas][lnk_capture_value].

[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

<!---HONumber=AcomDC_1203_2015-->