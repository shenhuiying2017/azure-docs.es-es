<properties title="How to use end user analytics" pageTitle="How to use end user analytics" description="Learn about end user analytics in Azure." authors="vladj" />

Análisis de sitios web
======================

¿Se pregunta cuántos usuarios han visitado su sitio? ¿Se hace una idea del tiempo de carga medio de la página o de los exploradores que se están utilizando? Con la inserción de unas pocas líneas de script en sus páginas web, podrá recopilar datos acerca de cómo sus clientes utilizan el sitio web.

![Análisis de usuario final](./media/insights-usage-analytics/Insights_ConfiguredExperience.png)

Configuración del análisis de usuario final
-------------------------------------------

1.  Haga clic en el elemento del cuadro **Web site** que pone **End user analytics**
2.  En el cuadro **Configuration**, seleccione el script de instrumentación completo y cópielo.

    ![Configuración](./media/insights-usage-analytics/Insights_CopyCode.png)

3.  Pegue el script en cada una de sus páginas web, justo antes del cierre de la etiqueta &lt;/head\>. Le recomendamos insertar el script en todas sus páginas web. Si está utilizando ASP.NET, puede hacerlo insertando el script en la página maestra de la aplicación.

4.  Implemente y utilice la nueva aplicación web. El uso de la información de análisis comenzará a aparecer a los 5 o 10 minutos aproximadamente.

Exploración de los datos
------------------------

El elemento Browsers session le permite consultar los diversos exploradores utilizados y las versiones de dichos exploradores.

![Exploradores](./media/insights-usage-analytics/Insights_Browsers.png)

El elemento Analytics muestra un desglose de los diversos tipos de dispositivos, incluidos los dispositivos de escritorio y móviles, las cinco mejores páginas y gráficos con el tiempo de carga de la página durante la semana pasada, y el número de sesiones y vistas.

![Mejores páginas](./media/insights-usage-analytics/Insights_TopPages.png)

-   Las páginas más lentas durante la semana pasada, para que pueda mejorarlas y cumplir sus objetivos y metas empresariales.

