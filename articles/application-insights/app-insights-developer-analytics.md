<properties
	pageTitle="Análisis del desarrollador"
	description="DevOps con Visual Studio, Application Insights y HockeyApp"
	authors="alancameronwills"
	services="application-insights"
    documentationCenter=""
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="05/18/2016"
	ms.author="awills"/>

# Análisis del desarrollador con Application Insights y HockeyApp

*Application Insights se encuentra en su versión de vista previa.*

Muchos proyectos se ejecutan en un ciclo de [DevOps](https://en.wikipedia.org/wiki/DevOps) rápido. Su función es compilar y distribuir sus aplicaciones, obtener información sobre cómo funciona y cómo las utilizan los usuarios y, después, emplear esos datos para planear más ciclos de desarrollo.

Para supervisar el uso y el rendimiento, es importante disponer de datos de telemetría de la aplicación activa, así como de comentarios de los propios usuarios.

Muchos sistemas se crean a partir de varios componentes: un servicio web, procesadores de back-end o almacenes de datos, y software de cliente que se ejecuta en el explorador del usuario o como una aplicación en un teléfono u otro dispositivo. Los datos de telemetría de estos componentes deben tomarse en conjunto.

En algunas versiones se ha restringido la distribución a los evaluadores designados; también ofrecemos distribución organizada de paquetes piloto (pruebas de nuevas características con público restringido) y pruebas A|B (pruebas paralelas de la interfaz de usuario alternativa).

Administrar las distribuciones e integrar la supervisión en varios componentes de cliente y servidor no es una tarea trivial. Este proceso constituye una parte esencial de la arquitectura de la aplicación: no se puede crear un sistema de este tipo sin un ciclo de desarrollo iterativo ni herramientas de supervisión eficaces.

En este artículo, explicaremos cómo encajan los aspectos de supervisión del ciclo de DevOps con las otras partes del proceso.

Si desea ver un ejemplo concreto, hay [un caso práctico interesante](http://aka.ms/mydrivingdocs) donde se emplean varios componentes de cliente y servidor.

## Ciclo de DevOps

Las herramientas de Visual Studio y Análisis del desarrollador ofrecen una experiencia de DevOps bien integrada. Por ejemplo, aquí se muestra un ciclo típico de una aplicación web (que podría ser ASP.NET, Node.js o Java):

![Ciclo de DevOps de aplicaciones web](./media/app-insights-developer-analytics/040.png)

* Un desarrollador protege el repositorio de código o lo combina en la rama principal. En esta ilustración, el repositorio es Git, pero podría ser igualmente [Control de versiones de Team Foundation](https://www.visualstudio.com/docs/tfvc/overview).
* Los cambios desencadenan una compilación y una prueba unitaria. El servicio de compilación puede estar en [Visual Studio Team Services o su equivalente local, Team Foundation Server](https://www.visualstudio.com/docs/vsts-tfs-overview). 
* Una prueba unitaria y una compilación correcta pueden [desencadenar una implementación automática](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition). El host de la aplicación web puede ser su propio servidor web o Microsoft Azure. 
* Se envía los datos de telemetría de la aplicación activa a [Application Insights](app-insights-overview.md) desde el servidor y los[exploradores de cliente](app-insights-javascript.md). Desde ahí se puede analizar el rendimiento de la aplicación y los patrones de uso. Las eficaces [herramientas de búsqueda](app-insights-analytics.md) lo ayudarán a diagnosticar cualquier problema. Las [alertas](app-insights-alerts.md) garantizan que esté al tanto de un problema en cuanto se produce. 
* El siguiente ciclo de desarrollo se basa en el análisis de los datos de telemetría activos.

### Aplicaciones de escritorio y de dispositivos

Para las aplicaciones de escritorio y de dispositivos, la parte de la distribución del ciclo es ligeramente diferente, ya que no solo vamos a cargar compilaciones en uno o dos servidores. En su lugar, una prueba unitaria y una compilación correcta pueden [desencadenar la carga en HockeyApp](https://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs). HockeyApp supervisa la distribución al equipo de usuarios de prueba (o al público en general, si lo prefiere).


![Ciclo de DevOps de dispositivos](./media/app-insights-developer-analytics/030.png)

HockeyApp también recopila datos de rendimiento y uso mediante los siguientes procesos:

* Comentarios textuales de usuarios con capturas de pantalla
* Informes de bloqueos
* Datos de telemetría personalizados que codifique

Una vez más, se completa el ciclo de desarrollo a medida que basa los planes de desarrollo futuros en función de los comentarios obtenidos.


## Configuración de Análisis del desarrollador

Para cada componente de la aplicación (móvil , web o escritorio), los pasos son, básicamente, los mismos. Para muchos tipos de aplicación, Visual Studio realiza automáticamente algunos de estos pasos.

1. Agregue el SDK adecuado a la aplicación. En aplicaciones de dispositivos, se trata de HockeyApp, y para los servicios web, Application Insights. Cada una de ellas tiene diversas variantes para diferentes plataformas (también se puede utilizar cualquier SDK para aplicaciones de escritorio, aunque se recomienda HockeyApp).
2. Registre la aplicación con el portal de Application Insights u HockeyApp, en función del SDK que utilice. En dicho portal es donde podrá ver análisis de la aplicación activa. Obtendrá una clave de instrumentación o identificador que se configura en la aplicación para que el SDK sepa dónde enviar sus datos de telemetría.
3. Agregue código personalizado (si lo desea) para registrar eventos o métricas con el fin de ayudarle a diagnosticar o analizar el rendimiento o uso. Hay muchos procesos de supervisión integrados, así que no los necesitará en el primer ciclo.
3. Para aplicaciones de dispositivos, siga estos pasos:
 * Cargue una compilación de depuración en HockeyApp. Desde ahí puede distribuirla a un grupo de usuarios de prueba. Cada vez que se cargan las compilaciones sucesivas, se notificará al equipo.
 * Al configurar el servicio de compilación continua, cree una definición de la versión que usa el paso de complemento para cargar las compilaciones en HockeyApp.
 
Aquí encontrará las instrucciones detalladas para los distintos tipos de aplicación:

* [Aplicación web para ASP.NET](app-insights-asp-net.md) 
* [Aplicaciones web de Java](app-insights-java-get-started.md)
* [Aplicación web para Node.js](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Aplicación para iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Aplicación para Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Aplicación para Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [Aplicación para Windows universal](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Aplicación para Windows Phone 8 y 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Aplicación para Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

<!---HONumber=AcomDC_0525_2016-->