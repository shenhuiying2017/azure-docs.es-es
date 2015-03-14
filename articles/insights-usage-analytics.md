<properties 
	pageTitle="Uso de análisis de usuario final" 
	description="Análisis del usuario final para sitios web de Microsoft Azure." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-09" 
	ms.author="awills"/>

# Análisis de sitios web de Microsoft Azure

¿Se pregunta cuántos usuarios han visitado su sitio?  ¿Se hace una idea del tiempo de carga medio de la página o de los exploradores que se están utilizando?  Con la inserción de unas pocas líneas de script en sus páginas web, podrá recopilar datos acerca de cómo sus clientes utilizan el sitio web. 

*Esto también lo puede hacer para sitios web que no son de Azure: [Supervisión del uso de aplicaciones web con Application Insights](../app-insights-web-track-usage/).*

![End User Analytics](./media/insights-usage-analytics/Insights_ConfiguredExperience.png)

## Configuración del análisis de usuario final

1. Haga clic en el apartado de la hoja **sitio web** que dice **Análisis de usuario final**
2. En la hoja **Configuración**, seleccione el script de instrumentación entero y cópielo.  
    ![Configuration](./media/insights-usage-analytics/Insights_CopyCode.png)
3. Pegue el script en cada una de sus páginas web, justo antes del cierre de la etiqueta </head>. Le recomendamos insertar el script en todas sus páginas web. Si está utilizando ASP.NET, puede hacerlo insertando el script en la página maestra de la aplicación.
4. Implemente y utilice la aplicación web. El uso de la información de análisis comenzará a aparecer a los 5 o 10 minutos aproximadamente.

## Exploración de los datos

El elemento Browsers session le permite consultar los diversos exploradores utilizados y las versiones de dichos exploradores.

![Browsers](./media/insights-usage-analytics/Insights_Browsers.png)

El elemento Analytics muestra

- Un desglose de los diversos tipos de dispositivos, incluidos los dispositivos de escritorio y móviles.
- Las cinco mejores páginas y gráficos con el tiempo de carga de la página durante la semana pasada.  También está disponible el número de sesiones y vistas  .
    ![Top Pages](./media/insights-usage-analytics/Insights_TopPages.png)
- Las páginas más lentas durante la semana pasada, para que pueda mejorarlas y cumplir sus objetivos y metas empresariales.


<!--HONumber=46--> 
