<properties title="Application Insights" pageTitle="Application Insights: comience a supervisar el estado y el uso de la aplicación" description="Analice el uso, la disponibilidad y el rendimiento de su aplicación web de Microsoft Azure o local con Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />

# Application Insights: comience a supervisar el estado y el uso de la aplicación

*Application Insights se encuentra en su versión de vista previa.*

Application Insights permite supervisar una aplicación activa en cuanto a:

* **Disponibilidad**: las URL se prueban cada pocos minutos desde distintos lugares del mundo.
* **Rendimiento**  : detecte y diagnostique excepciones y problemas de rendimiento.
* **Uso**: descubra cómo interactúan los usuarios con la aplicación para mejorarla.

Para formas alternativas, lea [Introducción a Application Insights][start].

## <a name="add"></a>Incorporación de Application Insights al proyecto

Necesitará [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (o posterior) y una cuenta en [Microsoft Azure](http://azure.com).

### Si se trata de un proyecto nuevo:

Cuando cree un proyecto nuevo en Visual Studio 2013, asegúrese de seleccionar Application Insights. 


![Create an ASP.NET project](./media/appinsights/appinsights-01-vsnewp1.png)

La primera vez deberá iniciar sesión o registrarse en Microsoft Azure en vista previa. (Al margen de la cuenta de Visual Studio Online).

Use **Parámetros de configuración** si desea asignar al recurso de Azure un nombre diferente al del proyecto, o para que el recurso aparezca en el mismo grupo como un proyecto distinto. 

*¿No encuentra la opción Application Insights? Compruebe que está usando Visual Studio 2013 Update 3, que las Herramientas de Application Insights se encuentran habilitadas en Extensiones y actualizaciones, y que está creando un proyecto web, de la Tienda Windows o Windows Phone.*

### ....o si se trata de un proyecto existente

Haga clic con el botón secundario en el Explorador de soluciones y seleccione Agregar Application Insights.

![Choose Add Application Insights](./media/appinsights/appinsights-03-addExisting.png)

*Queda un paso pendiente para configurar el análisis de uso de web, pero veamos otros datos primero.*


### <a name="run"></a>2. Ejecutar el proyecto

Ejecute la aplicación con F5 y pruébela. Abra varias páginas.

En Visual Studio, aparecerá un recuento de los eventos que se han recibido.

![](./media/appinsights/appinsights-09eventcount.png)

### <a name="monitor"></a>3. Ver los datos de supervisión

Abra Application Insights desde un proyecto.

![Right-click your project and open the Azure portal](./media/appinsights/appinsights-04-openPortal.png)


Busque los datos en los mosaicos de **Estado de la aplicación**. Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Click through to more data](./media/appinsights/appinsights-41firstHealth.png)

Haga clic en un mosaico para ver su contenido con mayor detalle. Si lo desea, puede cambiar el contenido de los informes. [Obtenga más información sobre cómo configurar los informes de estado de la aplicación.][perf]


### <a name="deploy"></a>4. Implementar la aplicación

Implemente la aplicación y observe cómo se acumulan los datos.



Cuando la aplicación se active, [configure las pruebas web][availability] para comprobar que efectivamente está activa. 

![Example application monitor in Application Insights](./media/appinsights/appinsights-00-appblade.png)

### ¿Desea cambiar el nombre de la aplicación en el portal?

Si lo desea, puede cambiar el recurso al que el proyecto envía la telemetría. 

El "recurso" es la hoja con nombre de Application Insights donde aparecen los resultados. Puede usar varios recursos en un grupo (por ejemplo, si cuenta con varios proyectos que forman parte de una aplicación empresarial). 

En el Explorador de soluciones, haga clic con el botón secundario en ApplicationInsights.config y elija **Actualizar Application Insights**. Se abrirá un asistente donde puede elegir otro recurso existente o crear uno nuevo.

A continuación, vuelva al portal y elimine el recurso anterior.

## <a name="video"></a>Vídeo

> [AZURE.VIDEO getting-started-with-application-insights]

## <a name="next"></a>Pasos siguientes

[Seguir el uso de una aplicación web][usage]

[Supervisar el rendimiento de aplicaciones web][perf]

[Capturar y buscar registros de diagnóstico][diagnostic]

[Solución de problemas][qna]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]


