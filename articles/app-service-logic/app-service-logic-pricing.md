<properties 
	pageTitle="Modelo de precios de Logic Apps | Microsoft Azure" 
	description="Detalles sobre el funcionamiento de los precios en Logic Apps" 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="07/25/2016"
	ms.author="klam"/>

# Modelo de precios de Logic Apps

El servicio Logic Apps le permite escalar y ejecutar flujos de trabajo de integración en la nube. A continuación se detallan los planes de facturación y precios de Azure Logic Apps.

## Precios de consumo

Las aplicaciones lógicas recién creadas usan un plan de consumo. Con el modelo de precios de consumo de Logic Apps, solo paga por lo que utiliza. Las aplicaciones lógicas no se limitan cuando se utiliza un plan de consumo. Se miden todas las ejecuciones de acción realizadas en la ejecución de una instancia de aplicación lógica.

### ¿Cuáles son las ejecuciones de acción?

Cada paso de una definición de aplicación lógica es una acción. Esto incluye desencadenadores, pasos del flujo de control, como condiciones, ámbitos, bucles foreach y bucles do until, llamadas a conectores y llamadas a acciones nativas. Los desencadenadores son simplemente acciones diseñadas para crear una nueva instancia de aplicación lógica cuando se recibe un evento determinado. Existen varios comportamientos diferentes para desencadenadores que podrían afectar al modo en que se mide la aplicación lógica.

-	**Desencadenador de sondeo**: este desencadenador sondea continuamente un punto de conexión hasta que recibe un mensaje que satisface los criterios para crear una nueva instancia de una aplicación lógica. El intervalo de sondeo se puede configurar en el desencadenador en el diseñador de Logic Apps. Cada solicitud de sondeo, incluso si no crea una nueva instancia de una aplicación lógica, contará como una ejecución de acción.

-	**Desencadenador de webhook**: este desencadenador espera a que un cliente envíe una solicitud en un punto de conexión determinado. Cada solicitud enviada al punto de conexión de webhook se considera una ejecución de acción. El desencadenador de solicitud y de webhook HTTP son ambos desencadenadores de webhook.

-	**Desencadenador de periodicidad**: este desencadenador crea una nueva instancia de la aplicación lógica según el intervalo de periodicidad configurado en el desencadenador. Por ejemplo, un desencadenador de periodicidad puede configurarse para que se ejecute cada 3 días o incluso cada minuto.

Las ejecuciones de desencadenadores se pueden ver en la hoja de recursos de Logic Apps en la parte Historial de desencadenadores.

Todas las acciones que se ejecutaron, tanto si tuvieron éxito como si no, se miden como ejecuciones de acción. Las acciones omitidas debido a condiciones que no se cumplieron o acciones que no se ejecutaron porque la aplicación lógica terminó antes de que se completara no se cuentan como ejecuciones de acción.

Las acciones ejecutadas dentro de bucles se cuentan por cada iteración del bucle. Por ejemplo, una sola acción en un bucle foreach que interacciona mediante una lista de 10 elementos se contará como el número de elementos de la lista (10) multiplicado por el número de acciones del bucle (1) más uno por la iniciación del bucle que, en este ejemplo, sería (10 * 1) + 1 = 11 ejecuciones de acción.

No se pueden crear nuevas instancias de las aplicaciones lógicas que están deshabilitadas y, por tanto, durante este tiempo estas no se cobrarán. Tenga en cuenta que después de deshabilitar una aplicación lógica, las instancias pueden tardar un tiempo en pasar al modo inactivo antes de deshabilitarse completamente.

## Planes del Servicio de aplicaciones

Para crear una aplicación lógica ya no se necesitan planes del Servicio de aplicaciones. Las aplicaciones lógicas anteriormente creadas con un plan del Servicio de aplicaciones, seguirán comportándose como antes; de modo que, según el plan elegido, se limitarán después de que se exceda un número de ejecuciones diarias y no se facturarán mediante un medidor de ejecuciones de acción.

Planes del Servicio de aplicaciones y sus ejecuciones de acción diarias permitidas:

| |Libre, Compartido o Básico|Estándar|Premium|
|---|---|---|---|
|Ejecuciones de acción por día| 200|10\.000|50\.000|

Para cambiar una aplicación lógica que tiene un plan del Servicio de aplicaciones asociado a un modelo de consumo, quite la referencia al plan del Servicio de aplicaciones en la definición de aplicación lógica. Esto puede hacerse con una simple llamada a un cmdlet de PowerShell:

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## Precios

Para más información sobre precios, consulte los [precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

## Pasos siguientes

- [Información general de Logic Apps][whatis]
- [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0727_2016-->