<properties 
	pageTitle="Cómo obtener soporte técnico del equipo de desarrollo de Application Insights | Microsoft Azure" 
	description="Cuando tenga un caso que requiera un soporte técnico especial por parte del equipo de desarrollo de Application Insights, este es el modo de enviar los detalles para obtenerlo." 
	services="application-insights" 
    documentationCenter=""
	authors="alexbulankou" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2016" 
	ms.author="albulank"/>
	
# Cómo obtener soporte técnico del equipo de desarrollo de Application Insights
	
Si aparece un problema técnico con [Visual Studio Application Insights](app-insights-overview.md), aquí encontrará las opciones para obtener ayuda:

## 1\. Consulta de los documentos

* ¿Faltan datos? Consulte: [muestreo](app-insights-sampling.md), [administración de precios y cuotas](app-insights-pricing.md).
* Solución de problemas: [ASP.NET](app-insights-troubleshoot-faq.md) | [Java](app-insights-java-troubleshoot.md)

## 2\. Búsqueda en los foros

* [Foro de MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

## 3\. ¿Plan de soporte técnico de Azure?

Existen situaciones donde desea que los desarrolladores investiguen su caso concreto.

Si tiene un [plan de soporte técnico con Microsoft Azure](https://azure.microsoft.com/support/plans/) puede [abrir una incidencia de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## 4\. Póngase en contacto con el equipo de Application Insights

Si no tiene ningún plan de soporte técnico, nuestro equipo de desarrollo está encantado de ofrecer la mejor asistencia a los clientes de Application Insights mientras se prepara para el hito de disponibilidad general. Hay **una nueva opción de compatibilidad**: puede describirnos su caso enviando un formulario de comentarios en el Portal de Azure; un desarrollador del equipo de Application Insights se pondrá en contacto con usted para ayudar a resolver el problema.


1. En el [portal de Application Insights](https://portal.azure.com), haga clic en la cara sonriente en la esquina superior derecha:  

    ![Botón de comentarios](./media/app-insights-get-dev-support/01.png)

2. En el cuadro de comentarios, asegúrese de especificar **AppInsights** en la primera línea y, después, incluya la siguiente información:

    ```

    AppInsights   
    ikey: <instrumentation key>   
    sdk: <SDK that you are using, including name and version>  
    issue: <please describe the problem you are having>

    ```   

    ![Cuadro de diálogo de comentarios](./media/app-insights-get-dev-support/02.png)

3. Seleccione "Sí, estoy de acuerdo con que me envíen mensajes de correo electrónico sobre mis comentarios.".

    ![Sección de envío](./media/app-insights-get-dev-support/03.png)

Un ingeniero del equipo de Application Insights se pondrá en contacto con usted pronto. Hacemos el máximo esfuerzo para proporcionar este servicio, pero no se puede proporcionar ningún acuerdo de nivel de servicio formal en este momento.

<!---HONumber=AcomDC_0615_2016-->