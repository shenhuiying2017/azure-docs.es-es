<properties 
	pageTitle="Administración de precios y cuotas para Application Insights" 
	description="Elija el plan de precios que necesite." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="awills"/>

# Administración de precios y cuotas para Application Insights

*Application Insights se encuentra en su versión de vista previa.*

Los [precios][pricing] de [Application Insights para Visual Studio][start] se basan en el volumen de datos por aplicación. Hay un nivel Gratis sustancial en el que se pueden disfrutar la mayoría de las características con algunas limitaciones.

Cada recurso de Application Insights se cobra como un servicio independiente y contribuye a la factura de la suscripción a Azure.

[Consulte el esquema de precios][pricing].

## Revisión del plan de cuotas y precios para el recurso de Application Insights

Puede abrir la hoja Cuota + Precios de la configuración del recurso de la aplicación.

![Elija Configuración, Cuota + Precios.](./media/app-insights-pricing/01-pricing.png)

## ¿Cómo funciona la cuota?

* En cada mes natural, la aplicación puede enviar hasta una cantidad especificada de telemetría a Application Insights. Consulte el [esquema de precios][pricing] para ver los números reales. 
* La cuota depende del nivel de precios que haya elegido.
* La cuota se cuenta a partir de la medianoche UTC del primer día de cada mes.
* El gráfico de puntos de datos muestra el uso que se ha hecho de la cuota en este mes.
* La cuota se mide en *puntos de datos.* Un único punto de datos es una llamada a uno de los métodos de seguimiento, independientemente de si se llama explícitamente en el código o por uno de los módulos de telemetría estándar. Cada fila que se ve en la búsqueda de diagnóstico es un punto de datos. Cada medida de una métrica como un contador de rendimiento es un punto de datos. 
* Los *datos de la sesión* no se cuentan en la cuota. Esto incluye los recuentos de datos de usuarios, sesiones, entorno y dispositivo.


## Superávit

Si una aplicación envía más de la cuota mensual, puede:

* Pagar por datos adicionales. Consulte el [esquema de precios][pricing] para obtener más información. Puede elegir esta opción de antemano. Esta opción no está disponible en el nivel de precios Gratis.
* Actualización del nivel de precios
* No haga nada. Los datos de la sesión seguirán registrándose, pero otros datos no aparecerán en la búsqueda de diagnóstico o en el explorador de métricas.

## Evaluación gratuita de Premium

Al crear un nuevo recurso de Application Insights, este se inicia en el nivel Gratis.

En cualquier momento, puede cambiar a la evaluación gratuita de Premium de 30 días. Esto le ofrecerá las ventajas del nivel Premium. Después de 30 días, la suscripción cambiará automáticamente al nivel en que se encontrase previamente, a menos que se elija explícitamente otro nivel. Puede seleccionar el nivel que desee en cualquier momento durante el período de evaluación, pero podrá seguir optando a la evaluación gratuita hasta el final del período de 30 días.


## ¿Cómo se ha utilizado mi cuota?

El gráfico de la parte inferior de la hoja de precios muestra el uso del punto de datos de la aplicación con grupos en función del tipo de punto de datos.

![En la parte inferior de la hoja de precios.](./media/app-insights-pricing/03-allocation.png)

Haga clic en el gráfico para obtener más detalles, o arrastre el puntero por el mismo para ver el detalle de un intervalo de tiempo.

## Revisión de la factura de su suscripción a Azure

Los cargos de Application Insights se agregarán a la factura de Azure. Puede ver los detalles de su factura de Azure en la sección de facturación del portal de Azure o en el [Portal de facturación de Azure](https://account.windowsazure.com/Subscriptions).

![En el menú lateral, elija Facturación.](./media/app-insights-pricing/02-billing.png)




<!--Link references-->


[start]: app-insights-get-started.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=August15_HO6-->