<properties title="How to use end user analytics" pageTitle="How to use end user analytics" description="Learn about end user analytics in Azure." authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-29" ms.author="awills"></tags>

# Análisis para sitios web

¿Se pregunta cuántos usuarios han visitado su sitio? ¿Se hace una idea del tiempo de carga medio de la página o de los exploradores que se están utilizando? Con la inserción de unas pocas líneas de script en sus páginas web, podrá recopilar datos acerca de cómo sus clientes utilizan el sitio web.

![Análisis de usuario final][]

## Configuración del análisis de usuario final

1.  Haga clic en el elemento del cuadro **Web site** que pone **End user analytics**
2.  En la hoja **Configuración**, seleccione el script de instrumentación completo y cópielo.
    ![Configuración][]
3.  Pegue el script en cada una de sus páginas web, justo antes del cierre de la etiqueta.
    . Le recomendamos insertar el script en todas sus páginas web. Si está utilizando ASP.NET, puede hacerlo insertando el script en la página maestra de la aplicación.
4.  Implemente y utilice la nueva aplicación web. El uso de la información de análisis comenzará a aparecer a los 5 o 10 minutos aproximadamente.

## Exploración de los datos

El elemento Browsers session le permite consultar los diversos exploradores utilizados y las versiones de dichos exploradores.

![Exploradores][]

El elemento Analytics muestra

-   Un desglose de los diversos tipos de dispositivos, incluidos los dispositivos de escritorio y móviles.
-   Las cinco mejores páginas y gráficos con el tiempo de carga de la página durante la semana pasada. El número de sesiones y vistas también está disponible en
    ![Mejores páginas][].
-   Las páginas más lentas durante la semana pasada, para que pueda mejorarlas y cumplir sus objetivos y metas empresariales.

  [Análisis de usuario final]: ./media/insights-usage-analytics/Insights_ConfiguredExperience.png
  [Configuración]: ./media/insights-usage-analytics/Insights_CopyCode.png
  [Exploradores]: ./media/insights-usage-analytics/Insights_Browsers.png
  [Mejores páginas]: ./media/insights-usage-analytics/Insights_TopPages.png
