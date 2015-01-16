<properties urlDisplayName="Resources" pageTitle="Creación de una campaña de marketing digital en Sitios web Azure" metaKeywords="" description="En esta guía se ofrece información general de carácter técnico acerca de cómo usar Sitios web Azure para crear campañas de marketing digital. Esto incluye la implementación, la integración de medios sociales, las estrategias de escalado y la supervisión." metaCanonical="" services="" documentationCenter="" title="Create a Digital Marketing Campaign on Azure Websites" authors="jroth" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/01/2014" ms.author="jroth" />

# Creación de una campaña de marketing digital en Sitios web Azure
En esta guía se ofrece información general de carácter técnico acerca de cómo usar Sitios web Azure para crear campañas de marketing digital. Una campaña de marketing digital suele ser una entidad de corta duración pensada para conseguir un objeto de marketing a corto plazo. Hay dos escenarios principales que se han de tener en cuenta. En el primer escenario, una empresa de marketing externa crea y administra la campaña para sus clientes mientras dura la promoción. En el segundo escenario, la empresa de marketing crea y, a continuación, transfiere la titularidad de los recursos de la campaña de marketing digital a sus clientes. A continuación, el cliente ejecuta y administra la campaña de marketing digital por su cuenta. 

[Sitios web Azure][websitesoverview] es una buena opción para ambos escenarios. Permite crear con rapidez, admite varios lenguajes y marcos de trabajo, se escala para satisfacer la demanda de los usuarios y aloja muchos sistemas de control de código fuente e implementación. Con el uso de Azure, también puede tener acceso a otros servicios de Azure, como los Servicios multimedia, que pueden mejorar una campaña de marketing.

> [WACOM.NOTE]
> Si desea obtener una introducción a Sitios web Azure antes de suscribirse a una cuenta, visite <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, donde puede crear inmediatamente y de forma gratuita un sitio básico de ASP.NET de corta duración en Sitios web Azure. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Aunque se pueden usar las plataformas [Servicios en la nube de Azure][csoverview] o [Máquinas virtuales de Azure][vmoverview] para hospedar sitios web, no se trata de la opción ideal para este escenario, salvo que se precise de alguna característica que Sitios web Azure no ofrece. Para conocer las opciones, consulte [Sitios web, Servicios en la nube y Máquinas virtuales de Azure: cuándo usar cada uno][chooseservice].

En esta guía se tratan los siguientes temas:

- [Implementación de sitios web existentes](#deployexisting)
- [Integración con redes sociales](#socialmedia)
- [Escala según la demanda de los usuarios](#scale)
- [Integración con otros servicios](#integrate)
- [Supervisión de la campaña](#monitor)

> [WACOM.NOTE]
> En esta guía se presentan algunos de los temas y tareas más comunes relacionados con el desarrollo de sitios .COM de acceso público. Sin embargo, existen otras funciones de Sitios web Azure que puede utilizar en su implementación concreta. Para revisar estas capacidades, consulte también otras guías sobre <a href="http://www.windowsazure.com/en-us/manage/services/web-sites/global-web-presence-solution-overview/">presencia web global</a> y <a href="http://www.windowsazure.com/en-us/manage/services/web-sites/business-application-solution-overview">Aplicaciones empresariales</a>.

##<a name="deployexisting"></a>Implementación de sitios web existentes
En la guía sobre presencia web mundial, explicamos varias opciones para crear e implementar un sitio web. Si es la primera vez que utiliza Sitios web Azure, es recomendable que [consulte esa información][scenarioglobalweb]. Si crea campañas de marketing digital con frecuencia, es posible que ya disponga de recursos web que puede personalizar para diferentes promociones. En esta sección, trataremos con más detalle las opciones para implementar varios tipos de sitios web desde el control de código fuente.

Si va a reutilizar los recursos web para varios propósitos, debe tener muy en cuenta el uso de un sistema de administración de control de código fuente si aún no lo usa. Este sistema le permite almacenar plantillas de soluciones web comunes que se pueden derivar y personalizar para clientes específicos. La plataforma Sitios web Azure ofrece la opción de sincronizar con muchos repositorios de código fuente distintos. En la pestaña **Panel**, seleccione el vínculo para **configurar la implementación a partir del control de código fuente**.

![DigitalMarketingDeploy1][DigitalMarketingDeploy1]

A continuación, se abre un cuadro de diálogo con varias opciones de control de código fuente. Entre ellas, se incluyen sistemas de control de código fuente completos, como TFS, además de soluciones de implementación sencillas, como Dropbox.

![DigitalMarketingDeploy2][DigitalMarketingDeploy2]

Puede usar varias técnicas de control de código fuente para administrar el proyecto nuevo, que sigue una línea base existente. Por ejemplo, puede copiar un repositorio de línea base que se haya guardado previamente para empezar a trabajar en el nuevo proyecto, o bien puede crear una rama nueva para hacer un seguimiento de las personalizaciones del proyecto actual. Para ver un buen ejemplo de uso de ramas para la administración de diferentes implementaciones desde el mismo repositorio de control de código fuente, consulte [Varios entornos con Sitios web Azure][gitstaging]. En esta publicación se demuestra cómo usar la bifurcación de Git para administrar entornos de ensayo y producción.

Tras haber conectado el sitio web al control de código fuente, puede configurar las implementaciones desde el portal, donde también puede realizar el seguimiento de ellas. Para obtener más información acerca de cómo usar el control de código fuente con Sitios web, consulte [Publicación en Sitios web Azure desde el control de código fuente][publishingwithgit].

Al usar los recursos web existentes, también es importante tener flexibilidad para hospedar muchos tipos diferentes de sitios web. En la pestaña **Configurar**, puede seleccionar la compatibilidad con .NET y PHP para el sitio web. 

![DigitalMarketingFrameworkVersions][DigitalMarketingFrameworkVersions]

Además de estas opciones de configuración, la plataforma Sitios web ofrece compatibilidad automáticamente con Python 2.7 y Node.js. La versión predeterminada de Node.js es la 0.10.5.

Una ventaja adicional de Sitios web Azure es la velocidad con que se implementan los sitios preconfigurados en la Web. Durante la planificación, la creación de prototipos y la implementación inicial de un sitio, la agencia y sus clientes pueden consultar las versiones de trabajo reales del sitio de la campaña antes de lanzarlo. Cuando el sitio esté listo para producción, la agencia puede administrar la implementación de producción para el cliente o proporcionar los recursos web al cliente para que pueda realizar las tareas de implementación y administración.

##<a name="socialmedia"></a>Integración con redes sociales
La mayoría de las campañas de marketing digital usan los sitios de redes sociales, como Facebook o Twitter. Un punto de integración es usar las identidades de los medios sociales para la autenticación. Para ver un ejemplo de este enfoque con una aplicación ASP.NET, consulte [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un Sitio web Azure][deploysecurewebsite].

No obstante, muchas campañas de marketing digital van más allá de la autenticación y usan la integración en las redes sociales como una parte clave de su estrategia. Los sitios de redes sociales suelen tener una sección para desarrolladores en la que se explican las diferentes formas de integrar las aplicaciones con sus servicios. Los servicios que ofrecen una API de REST se pueden usar desde casi todos los marcos de trabajo web. No obstante, suele haber información disponible que es específica del lenguaje que elija. Por ejemplo, Twitter ofrece [una lista de bibliotecas disponibles que admiten la API de Twitter][twitter], incluidas las de .NET, Node.js y PHP. Este es solo un ejemplo, pero puede buscar orientación similar de los desarrolladores directamente en cada sitio de red social que elija como destino.

Para los desarrolladores de ASP.NET interesados en Facebook, Visual Studio ofrece una plantilla para una aplicación de Facebook MVC 4.  

![DigitalMarketingFacebook][DigitalMarketingFacebook]

Para obtener acceso a un tutorial acerca de cómo usar esta plantilla con Sitios web, consulte el artículo sobre cómo [crear una aplicación de Facebook con plantillas de ASP.NET MVC para Facebook y cómo hospedarlas de forma gratuita en Sitios web Azure][fbtutorial]. Para obtener acceso a una aplicación de ejemplo y a un tutorial detallado, consulte la [aplicación para cumpleaños de Facebook en ASP.NET MVC][fbbirthdayapp] y [la nueva plantilla de aplicaciones de Facebook y la biblioteca para ASP.NET MVC][fbvstemplate].

Si es desarrollador de ASP.NET, es importante que sepa que la integración con las redes sociales no se limita a las plantillas que Visual Studio ofrece. La única finalidad de una plantilla es simplificar el proceso. No obstante, como se ha mencionado anteriormente, cada sitio de red social suele ofrecer información acerca de otras formas de conectarse mediante .NET o muchos otros lenguajes y marcos de trabajo.

##<a name="scale"></a>Escala según la demanda de los usuarios
La informática en nube resulta útil para cargas de trabajo impredecibles. Las campañas de marketing digital se engloban en esta categoría. Es complicado prever la popularidad de un sitio web de una duración relativamente corta, porque depende en gran medida de captar el interés del usuario y de las interacciones de las redes sociales relacionadas que dirigen más tráfico al sitio. Azure ofrece varias opciones para escalar los sitios web y los servicios en la nube.

- Ajuste de escala manual a través del [Portal de administración de Azure][managementportal].
- Ajuste de escala mediante programación a través de la [API de administración de servicios][servicemanagementapi] o el [scripting de PowerShell][powershell].
- Ajuste de escala automático a través de la característica Autoscale (Preview).

En el Portal de administración del sitio web, vaya a la pestaña **Escala**. Hay varias opciones para el ajuste de escala. La primera opción determina el modo del sitio web, que está definido como **libre**, **compartido** o **estándar**. 

![DigitalMarketingScale][DigitalMarketingScale]

Las características y las opciones de escalabilidad aumentan con cada nivel. Por ejemplo, los sitios con el modo **libre** no se pueden escalar a varias instancias, pero los sitios con el modo **compartido** se pueden escalar a seis instancias. Si selecciona el modo **estándar**, también se le ofrecerá la opción de escalar. Este modo ejecuta el sitio en máquinas virtuales dedicadas y ofrece opciones para ajustar el tamaño de la máquina como pequeña, mediana y grande. Después de decidir el tamaño de la máquina virtual, tiene la opción de escalar a varias instancias. En el modo **estándar**, puede escalar a diez instancias. Puede encontrar una lista de diferencias entre cada modo en [Información sobre el precio de Sitios web][pricing].

Si selecciona el modo **estándar**, tiene la opción adicional de habilitar la característica Escalado automático (vista previa). Esto le permite escalar en función de la CPU. El porcentaje de **CPU de destino** representa un intervalo de utilización objetivo del procesador en Azure para las instancias del sitio web. Si el promedio de CPU es inferior a este objetivo, Azure reduce el recuento de instancias, porque la misma carga con menos instancias aumentará la utilización de las demás instancias. No obstante, no puede reducir el recuento de instancias por debajo del valor mínimo establecido en **Recuento de instancias**. De la misma forma, si el promedio de CPU es mayor que el porcentaje de **CPU de destino**, Azure aumenta el número de instancias. La distribución de la misma carga entre máquinas adicionales reduce la utilización de la CPU en cada máquina. El número de instancias agregadas se limita al valor máximo establecido en **Recuento de instancias**.

![DigitalMarketingAutoScale][DigitalMarketingAutoScale]

El ajuste de escala automático usa los recursos de forma mucho más eficaz. Por ejemplo, es posible que la campaña de marketing digital tenga más actividad por la noche y durante los fines de semana. Esto también controla un escenario con eficacia en el que la popularidad de una campaña aumenta inesperadamente. El ajuste de escala automático ayuda a controlar con eficacia el aumento de la carga y, a continuación, reduce las instancias (y el coste) cuando disminuye la carga. 

Para obtener más información acerca del ajuste de escala de los sitios web, consulte [Escalación de sitios web][scalewebsite]. Este tema también está estrechamente relacionado con la supervisión. Para obtener más información, consulte la sección de esta guía que trata sobre la [supervisión de la campaña](#monitor).

> [WACOM.NOTE]
> Si se trata de aplicaciones web que optan por usar los servicios en la nube y los roles web, hay una opción adicional para escalar en función de la longitud de los elementos de una cola. En un servicio en la nube, los roles que procesan colas back-end constituyen un patrón de arquitectura común. Para obtener más información sobre el escalado de servicios en la nube, consulte: <a href="http://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-scale-a-cloud-service/">Escalado de un servicio en la nube</a>.

##<a name="integrate"></a>Integración con otros servicios
Un sitio de marketing digital a menudo incorporará medios enriquecidos, como la transmisión por secuencias de vídeo. El hospedaje de estos sitios en Azure ofrece una estrecha integración con los servicios de Azure relacionados. Por ejemplo, puede usar los Servicios multimedia de Azure para codificar y transmitir vídeo para el sitio web. Para obtener más información acerca de los Servicios multimedia, consulte [Introducción a los escenarios y conceptos de los Servicios multimedia de Azure][mediaservices].

Se pueden usar otros servicios de Azure para crear una aplicación más sólida. Por ejemplo, la plataforma Servicios web puede usar el almacenamiento en caché distribuido que ofrece el nuevo servicio [Servicio de caché de Azure (vista previa)][caching]. También puede usar los Servicios de almacenamiento de Azure para almacenar los recursos y datos de la aplicación. Por ejemplo, los gráficos, vídeos y otros archivos grandes se pueden almacenar en blobs de forma duradera. Los servicios de base de datos, como Base de datos SQL de Azure y MySQL, también están disponibles para los requisitos de datos relacionales.

##<a name="monitor"></a>Supervisión de la campaña
La pestaña **Supervisar** contiene métricas que pueden ayudarle a evaluar el éxito y rendimiento del sitio de marketing digital.

![DigitalMarketingMonitor][DigitalMarketingMonitor]

Por ejemplo, puede consultar los patrones y niveles de uso teniendo en cuenta métricas como **Tiempo de CPU**, **Solicitudes** y **Salida de datos**. Todas estas métricas aumentarán a medida que la campaña de marketing adquiera mayor popularidad. La información acerca del uso puede usarse para decidir si escalar horizontal o verticalmente. Para obtener más información, consulte [Supervisión de sitios web][monitoring].

Para los modos **libre** y **compartido**, también debe conocer las cuotas de recursos. En la pestaña **Panel**, el portal muestra las estadísticas actuales de uso de varias cuotas y cuándo se restablecerán dichas cuotas. Estas estadísticas de uso varían en función del modo seleccionado. En la siguiente captura de pantalla se indican las estadísticas mostradas para el modo **libre**. En el modo **compartido**, no dispone de ninguna cuota para **Data Out**. En el modo **estándar**, se muestran solo los valores de **Almacenamiento del sistema de archivos** y **Tamaño**.

![DigitalMarketingUsageOverview][DigitalMarketingUsageOverview]

Si observa que está a punto de agotar estas cuotas, considere cambiar de **libre** a **compartido** o de **compartido** a **estándar**. En el modo **estándar**, tiene recursos dedicados en una o varias máquinas virtuales pequeñas, medianas o grandes. 

Además de usar el Portal de administración para obtener esta información, hay una serie de herramientas de terceros que ofrecen datos de supervisión avanzados de los sitios web. Por ejemplo, puede instalar un complemento New Relic de la Tienda Azure. Para obtener acceso a un tutorial acerca de cómo usar New Relic para la supervisión, consulte [Administración del rendimiento de la aplicación New Relic en Sitios web Azure][newrelic]. 

Por último, los sitios web con el modo estándar pueden permitir las alertas y la supervisión de extremos. Una supervisión de extremos trata regularmente de alcanzar el sitio web y, a continuación, genera informes sobre el tiempo de respuesta. Las alertas ofrecen notificaciones por correo electrónico si el tiempo de respuesta supera el umbral especificado. Para obtener más información, consulte la sección de supervisión del escenario [presencia web global][scenarioglobalweb] y el tema [Recepción de notificaciones de alerta y administración de reglas de alerta en Azure][receivealerts].

##<a name="summary"></a>Resumen
La plataforma Sitios web Azure es una buena opción para el contenido web reutilizable que se personaliza para campañas de marketing individuales. Esta plataforma admite muchos de los lenguajes, marcos de trabajo y sistemas de control de código cuente populares, por lo que facilita la migración de estos recursos y flujos de trabajo a la nube. La plantilla de la aplicación de Facebook ASP.NET facilita la creación de aplicaciones de Facebook, pero puede usar casi todos los tipos de integraciones en redes sociales de terceros que admitan las interfaces web. La plataforma Servicios multimedia de Azure y otros servicios relacionados de Azure ofrecen herramientas adicionales para crear un sitio bien diseñado para campañas. Además, las diversas opciones de ajuste de escala manual y automático resultan útiles para controlar la demanda de los usuarios, que puede resultar complicado de prever. Para obtener más información, consulte los siguientes artículos técnicos.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Ámbito</th>
   <th align="left" valign="top">Recursos</th>
</tr>
<tr>
   <td valign="middle"><strong>Plan</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/en-us/manage/services/web-sites/choose-web-app-service">Sitios web Azure, Servicios en la nube y Máquinas virtuales: cuándo usar cada uno</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Creación</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-create-websites/">Creación e implementación de un sitio web</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Implementación</strong></td>
   <td valign="top">- <a href="http://azure.microsoft.com/en-us/documentation/articles/web-sites-deploy/">Implementación de un sitio web de Azure</a><br/>- <a href="http://www.windowsazure.com/en-us/develop/net/common-tasks/publishing-with-git/">Publicación del control de código fuente a Sitios web Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Redes sociales</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/en-us/develop/net/tutorials/web-site-with-sql-database/">Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL</a><br/>- <a href="http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx">Creación de una aplicación de Facebook con plantillas de ASP.NET MVC para Facebook</a><br/>- <a href="http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx">Plantilla de aplicaciones de Facebook y biblioteca para ASP.NET MVC</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Escala</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-scale-websites/">Escalación de sitios web</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Medios enriquecidos</strong></td>
   <td valign="top">- <a href="http://msdn.microsoft.com/en-us/library/windowsazure/dn223282.aspx">Introducción a los escenarios y conceptos de los Servicios multimedia de Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Monitor</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/">Supervisión de sitios web</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Reproducción de notificaciones de alerta y administración de reglas de alerta en Azure.</a></td>
</tr>
</table>

  [websitesoverview]:/en-us/documentation/services/web-sites/
  [csoverview]:/en-us/documentation/services/cloud-services/
  [vmoverview]:/en-us/documentation/services/virtual-machines/
  [chooseservice]:/en-us/manage/services/web-sites/choose-web-app-service
  [scenarioglobalweb]:/en-us/manage/services/web-sites/global-web-presence-solution-overview/
  
  
  [publishingwithgit]:/en-us/develop/net/common-tasks/publishing-with-git/
  [gitstaging]:http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
  [deploysecurewebsite]:/en-us/develop/net/tutorials/web-site-with-sql-database/
  [twitter]:https://dev.twitter.com/docs/twitter-libraries#dotnet
  [fbtutorial]:http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx
  [fbbirthdayapp]:http://www.asp.net/mvc/tutorials/mvc-4/aspnet-mvc-facebook-birthday-app
  [fbvstemplate]:http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx
  [managementportal]:http://manage.windowsazure.com/
  [servicemanagementapi]:http://msdn.microsoft.com/en-us/library/windowsazure/ee460799.aspx
  [powershell]:http://msdn.microsoft.com/en-us/library/windowsazure/jj152841.aspx
  [pricing]:https://www.windowsazure.com/en-us/pricing/details/web-sites/
  [scalewebsite]:/en-us/manage/services/web-sites/how-to-scale-websites/
  
  [mediaservices]:http://msdn.microsoft.com/en-us/library/windowsazure/dn223282.aspx
  [caching]:http://msdn.microsoft.com/en-us/library/windowsazure/dn386094.aspx
  [monitoring]:/en-us/manage/services/web-sites/how-to-monitor-websites/
  [newrelic]:/en-us/develop/net/how-to-guides/new-relic/
  [receivealerts]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx
  
  
  
   [DigitalMarketingDeploy1]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy1.png
  [DigitalMarketingDeploy2]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy2.png
  [DigitalMarketingFrameworkVersions]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_FrameworkVersions.png
  [DigitalMarketingFacebook]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Facebook.png
  [DigitalMarketingScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Scale.png
  [DigitalMarketingAutoScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_AutoScale.png
  [DigitalMarketingMonitor]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Monitor.png
  [DigitalMarketingUsageOverview]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_UsageOverview.png
  
  
  
  
  

<!--HONumber=35.1-->
