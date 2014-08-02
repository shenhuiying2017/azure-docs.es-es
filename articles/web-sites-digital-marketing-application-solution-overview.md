<properties linkid="websites-digital-marketing" urlDisplayName="Resources" pageTitle="Create a Digital Marketing Campaign on Azure Web Sites" metaKeywords="" description="This guide provides a technical overview of how to use Azure Web Sites to create digital marketing campaigns. This includes deployment, social media integration, scaling strategies, and monitoring." metaCanonical="" services="" documentationCenter="" title="Create a Digital Marketing Campaign on Azure Web Sites" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

Creación de una campaña de marketing digital en Sitios web Azure
================================================================

En esta guía se ofrece información general de carácter técnico acerca de cómo usar Sitios web Azure para crear campañas de marketing digital. Una campaña de marketing digital suele ser una entidad de corta duración pensada para conseguir un objeto de marketing a corto plazo. Hay dos escenarios principales que se han de tener en cuenta. En el primer escenario, una empresa de marketing externa crea y administra la campaña para sus clientes mientras dura la promoción. En el segundo escenario, la empresa de marketing crea y, a continuación, transfiere la titularidad de los recursos de la campaña de marketing digital a sus clientes. A continuación, el cliente ejecuta y administra la campaña de marketing digital por su cuenta.

[Sitios web Azure](/en-us/documentation/services/web-sites/) es una buena opción para ambos escenarios. Permite crear con rapidez, admite varios lenguajes y marcos de trabajo, se escala para satisfacer la demanda de los usuarios y aloja muchos sistemas de control de código fuente e implementación. Con el uso de Azure, también puede tener acceso a otros servicios de Azure, como los Servicios multimedia, que pueden mejorar una campaña de marketing.

Aunque se pueden usar las plataformas [Servicios en la nube de Azure](/en-us/documentation/services/cloud-services/) o [Máquinas virtuales de Azure](/en-us/documentation/services/virtual-machines/) para hospedar sitios web, no se trata de la opción ideal para este escenario, salvo que se precise de alguna característica que Sitios web Azure no ofrece. Para conocer las opciones, consulte [Sitios web, Servicios en la nube y Máquinas virtuales de Azure: cuándo usar cada uno](/en-us/manage/services/web-sites/choose-web-app-service).

En esta guía se tratan los siguientes temas:

-   [Implementación de sitios web existentes](#deployexisting)
-   [Integración con redes sociales](#socialmedia)
-   [Escala según la demanda de los usuarios](#scale)
-   [Integración con otros servicios](#integrate)
-   [Supervisión de la campaña](#monitor)

**Nota:**

En esta guía se presentan algunos de los temas y tareas más comunes relacionados con el desarrollo de sitios .COM de acceso público. Sin embargo, existen otras funciones de Sitios web Azure que puede utilizar en su implementación concreta. Para revisar estas capacidades, consulte las otras guías sobre[presencia web global](http://www.windowsazure.com/en-us/manage/services/web-sites/global-web-presence-solution-overview/) y [aplicaciones empresariales](http://www.windowsazure.com/en-us/manage/services/web-sites/business-application-solution-overview).

Implementación de sitios web existentes
---------------------------------------

En la guía sobre presencia web mundial, explicamos varias opciones para crear e implementar un sitio web. Si es la primera vez que utiliza Sitios web Azure, es recomendable que [consulte esa información](/en-us/manage/services/web-sites/global-web-presence-solution-overview/). Si crea campañas de marketing digital con frecuencia, es posible que ya disponga de recursos web que puede personalizar para diferentes promociones. En esta sección, trataremos con más detalle las opciones para implementar varios tipos de sitios web desde el control de código fuente.

Si va a reutilizar los recursos web para varios propósitos, debe tener muy en cuenta el uso de un sistema de administración de control de código fuente si aún no lo usa. Este sistema le permite almacenar plantillas de soluciones web comunes que se pueden derivar y personalizar para clientes específicos. La plataforma Sitios web Azure ofrece la opción de sincronizar con muchos repositorios de código fuente distintos. En la pestaña **Panel**, seleccione el vínculo **Set up deployment from source control**.

![Implementación de marketing digital 1](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy1.png)

A continuación, se abre un cuadro de diálogo con varias opciones de control de código fuente. Entre ellas, se incluyen sistemas de control de código fuente completos, como TFS, además de soluciones de implementación sencillas, como Dropbox.

![Implementación de marketing digital 2](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy2.png)

Puede usar varias técnicas de control de código fuente para administrar el proyecto nuevo, que sigue una línea base existente. Por ejemplo, puede copiar un repositorio de línea base que se haya guardado previamente para empezar a trabajar en el nuevo proyecto, o bien puede crear una rama nueva para hacer un seguimiento de las personalizaciones del proyecto actual. Para ver un buen ejemplo de uso de ramas para la administración de diferentes implementaciones desde el mismo repositorio de control de código fuente, consulte [Multiple Environments with Azure Web Sites](http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites). En esta publicación se demuestra cómo usar la bifurcación de Git para administrar entornos de ensayo y producción.

Tras haber conectado el sitio web al control de código fuente, puede configurar las implementaciones desde el portal, donde también puede realizar el seguimiento de ellas. Para obtener más información acerca de cómo usar el control de código fuente con Sitios web, consulte [Publicación en Sitios web Azure desde el control de código fuente](/en-us/develop/net/common-tasks/publishing-with-git/).

Al usar los recursos web existentes, también es importante tener flexibilidad para hospedar muchos tipos diferentes de sitios web. En la pestaña **Configure**, puede seleccionar la compatibilidad con .NET y PHP para el sitio web.

![Versiones de marco de trabajo para marketing digital](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_FrameworkVersions.png)

Además de estas opciones de configuración, la plataforma Sitios web ofrece compatibilidad automáticamente con Python 2.7 y Node.js. La versión predeterminada de Node.js es la 0.10.5.

Una ventaja adicional de Sitios web Azure es la velocidad con que se implementan los sitios preconfigurados en la Web. Durante la planificación, la creación de prototipos y la implementación inicial de un sitio, la agencia y sus clientes pueden consultar las versiones de trabajo reales del sitio de la campaña antes de lanzarlo. Cuando el sitio esté listo para producción, la agencia puede administrar la implementación de producción para el cliente o proporcionar los recursos web al cliente para que pueda realizar las tareas de implementación y administración.

Integración con redes sociales
------------------------------

La mayoría de las campañas de marketing digital usan los sitios de redes sociales, como Facebook o Twitter. Un punto de integración es usar las identidades de los medios sociales para la autenticación. Para ver un ejemplo de este enfoque con una aplicación ASP.NET, consulte [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un Sitio web Azure](/en-us/develop/net/tutorials/web-site-with-sql-database/).

No obstante, muchas campañas de marketing digital van más allá de la autenticación y usan la integración en las redes sociales como una parte clave de su estrategia. Los sitios de redes sociales suelen tener una sección para desarrolladores en la que se explican las diferentes formas de integrar las aplicaciones con sus servicios. Los servicios que ofrecen una API de REST se pueden usar desde casi todos los marcos de trabajo web. No obstante, suele haber información disponible que es específica del lenguaje que elija. Por ejemplo, Twitter ofrece [una lista de bibliotecas disponibles que admiten la API de Twitter](https://dev.twitter.com/docs/twitter-libraries#dotnet) (en inglés), incluidas las de .NET, Node.js y PHP. Este es solo un ejemplo, pero puede buscar orientación similar de los desarrolladores directamente en cada sitio de red social que elija como destino.

Para los desarrolladores de ASP.NET interesados en Facebook, Visual Studio ofrece una plantilla para una aplicación de Facebook MVC 4.

![Facebook para marketing digital](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Facebook.png)

Para obtener acceso a un tutorial acerca de cómo usar esta plantilla con Sitios web, consulte [Creating a Facebook app using ASP.NET MVC Facebook Templates and hosting them for free on Azure Websites](http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx). Para obtener acceso a una aplicación de ejemplo y a un tutorial detallado, consulte [ASP.NET MVC Facebook Birthday App](http://www.asp.net/mvc/tutorials/mvc-4/aspnet-mvc-facebook-birthday-app) y [The new Facebook application template and library for ASP.NET MVC](http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx).

Si es desarrollador de ASP.NET, es importante que sepa que la integración con las redes sociales no se limita a las plantillas que Visual Studio ofrece. La única finalidad de una plantilla es simplificar el proceso. No obstante, como se ha mencionado anteriormente, cada sitio de red social suele ofrecer información acerca de otras formas de conectarse mediante .NET o muchos otros lenguajes y marcos de trabajo.

Escala según la demanda de los usuarios
---------------------------------------

La informática en nube resulta útil para cargas de trabajo impredecibles. Las campañas de marketing digital se engloban en esta categoría. Es complicado prever la popularidad de un sitio web de una duración relativamente corta, porque depende en gran medida de captar el interés del usuario y de las interacciones de las redes sociales relacionadas que dirigen más tráfico al sitio. Azure ofrece varias opciones para escalar los sitios web y los servicios en la nube.

-   Ajuste de escala manual a través del [Portal de administración de Azure](http://manage.windowsazure.com/).
-   Ajuste de escala mediante programación a través de la [API de administración de servicios](http://msdn.microsoft.com/en-us/library/windowsazure/ee460799.aspx) o el [scripting de PowerShell](http://msdn.microsoft.com/en-us/library/windowsazure/jj152841.aspx).
-   Ajuste de escala automático a través de la característica Autoscale (Preview).

En el Portal de administración del sitio web, vaya a la pestaña **Escala**. Hay varias opciones para el ajuste de escala. La primera opción determina el modo del sitio web, que está definido como **libre**, **compartido** o **estándar**.

![Escala de marketing digital](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Scale.png)

Las características y las opciones de escalabilidad aumentan con cada nivel. Por ejemplo, los sitios con el modo **libre** no se pueden escalar a varias instancias, pero los sitios con el modo **compartido** se pueden escalar a seis instancias. Si selecciona el modo **estándar**, también se le ofrecerá la opción de escalar. Este modo ejecuta el sitio en máquinas virtuales dedicadas y ofrece opciones para ajustar el tamaño de la máquina como pequeña, mediana y grande. Después de decidir el tamaño de la máquina virtual, tiene la opción de escalar a varias instancias. En el modo **estándar**, puede escalar a diez instancias. Puede encontrar una lista de diferencias entre cada modo en [Información sobre el precio de Sitios web](https://www.windowsazure.com/en-us/pricing/details/web-sites/).

Si selecciona el modo **estándar**, tiene la opción adicional de habilitar la característica Autoscale (Preview). Esto le permite escalar en función de la CPU. El porcentaje de **CPU de destino** representa un intervalo de utilización objetivo del procesador en Azure para las instancias del sitio web. Si el promedio de CPU es inferior a este objetivo, Azure reduce el recuento de instancias, porque la misma carga con menos instancias aumentará la utilización de las demás instancias. No obstante, no puede reducir el recuento de instancias por debajo del valor mínimo establecido en **Recuento de instancias**. De la misma forma, si el promedio de CPU es mayor que el porcentaje de **CPU de destino**, Azure aumenta el número de instancias. La distribución de la misma carga entre máquinas adicionales reduce la utilización de la CPU en cada máquina. El número de instancias agregadas se limita al valor máximo establecido en **Recuento de instancias**.

![Autoescala para marketing digital](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_AutoScale.png)

El ajuste de escala automático usa los recursos de forma mucho más eficaz. Por ejemplo, es posible que la campaña de marketing digital tenga más actividad por la noche y durante los fines de semana. Esto también controla un escenario con eficacia en el que la popularidad de una campaña aumenta inesperadamente. El ajuste de escala automático ayuda a controlar con eficacia el aumento de la carga y, a continuación, reduce las instancias (y el coste) cuando disminuye la carga.

Para obtener más información acerca del ajuste de escala de los sitios web, consulte [Escalación de sitios web](/en-us/manage/services/web-sites/how-to-scale-websites/). Este tema también está estrechamente relacionado con la supervisión. Para obtener más información, consulte la sección de esta guía que trata sobre la [supervisión de la campaña](#monitor).

**Nota:**

Si se trata de aplicaciones web que optan por usar los servicios en la nube y los roles web, hay una opción adicional para escalar en función de la longitud de los elementos de una cola. En un servicio en la nube, los roles que procesan colas back-end constituyen un patrón de arquitectura común. Para obtener más información acerca del ajuste de escala de los servicios en la nube, consulte [Escalación de un servicio en la nube](http://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-scale-a-cloud-service/).

Integración con otros servicios
-------------------------------

Un sitio de marketing digital a menudo incorporará medios enriquecidos, como la transmisión por secuencias de vídeo. El hospedaje de estos sitios en Azure ofrece una estrecha integración con los servicios de Azure relacionados. Por ejemplo, puede usar los Servicios multimedia de Azure para codificar y transmitir vídeo para el sitio web. Para obtener más información acerca de los Servicios multimedia, consulte [Introducción a los escenarios y conceptos de los Servicios multimedia de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/dn223282.aspx).

Se pueden usar otros servicios de Azure para crear una aplicación más sólida. Por ejemplo, la plataforma Servicios web puede usar el almacenamiento en caché distribuido que ofrece el nuevo servicio [Windows Azure Cache Service (vista previa)](http://msdn.microsoft.com/en-us/library/windowsazure/dn386094.aspx). También puede usar los Servicios de almacenamiento de Azure para almacenar los recursos y datos de la aplicación. Por ejemplo, los gráficos, vídeos y otros archivos grandes se pueden almacenar en blobs de forma duradera. Los servicios de base de datos, como Base de datos SQL de Azure y MySQL, también están disponibles para los requisitos de datos relacionales.

Supervisión de la campaña
-------------------------

La pestaña **Monitor** contiene métricas que pueden ayudarle a evaluar el éxito y rendimiento del sitio de marketing digital.

![Supervisión de marketing digital](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Monitor.png)

Por ejemplo, puede consultar los patrones y niveles de uso teniendo en cuenta métricas como **CPU Time**, **Requests** y **Data Out**. Todas estas métricas aumentarán a medida que la campaña de marketing adquiera mayor popularidad. La información acerca del uso puede usarse para decidir si escalar horizontal o verticalmente. Para obtener más información, consulte [Supervisión de sitios web](/en-us/manage/services/web-sites/how-to-monitor-websites/).

Para los modos **libre** y **compartido**, también debe conocer las cuotas de recursos. En la pestaña **Panel**, el portal muestra las estadísticas actuales de uso de varias cuotas y cuándo se restablecerán dichas cuotas. Estas estadísticas de uso varían en función del modo seleccionado. En la siguiente captura de pantalla se indican las estadísticas mostradas para el modo **libre**. En el modo **compartido**, no dispone de ninguna cuota para **Data Out**. En el modo **estándar**, se muestran solo los valores de **Almacenamiento del sistema de archivos** y **Tamaño**.

![Introducción al uso de marketing digital](./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_UsageOverview.png)

Si observa que está a punto de agotar estas cuotas, considere cambiar de **libre** a **compartido** o de **compartido** a **estándar**. En el modo **estándar**, tiene recursos dedicados en una o varias máquinas virtuales pequeñas, medianas o grandes.

Además de usar el Portal de administración para obtener esta información, hay una serie de herramientas de terceros que ofrecen datos de supervisión avanzados de los sitios web. Por ejemplo, puede instalar un complemento New Relic de la Tienda Azure. Para obtener acceso a un tutorial acerca de cómo usar New Relic para la supervisión, consulte [Administración del rendimiento de la aplicación New Relic en Sitios web Azure](/en-us/develop/net/how-to-guides/new-relic/).

Por último, los sitios web con el modo estándar pueden permitir las alertas y la supervisión de extremos. Una supervisión de extremos trata regularmente de alcanzar el sitio web y, a continuación, genera informes sobre el tiempo de respuesta. Las alertas ofrecen notificaciones por correo electrónico si el tiempo de respuesta supera el umbral especificado. Para obtener más información, consulte la sección de supervisión del escenario [presencia web global](/en-us/manage/services/web-sites/global-web-presence-solution-overview/) y el tema [Recepción de notificaciones de alerta y administración de reglas de alerta en Azure](http://msdn.microsoft.com/library/windowsazure/dn306638.aspx).

Resumen
-------

La plataforma Sitios web Azure es una buena opción para el contenido web reutilizable que se personaliza para campañas de marketing individuales. Esta plataforma admite muchos de los lenguajes, marcos de trabajo y sistemas de control de código cuente populares, por lo que facilita la migración de estos recursos y flujos de trabajo a la nube. La plantilla de la aplicación de Facebook ASP.NET facilita la creación de aplicaciones de Facebook, pero puede usar casi todos los tipos de integraciones en redes sociales de terceros que admitan las interfaces web. La plataforma Servicios multimedia de Azure y otros servicios relacionados de Azure ofrecen herramientas adicionales para crear un sitio bien diseñado para campañas. Además, las diversas opciones de ajuste de escala manual y automático resultan útiles para controlar la demanda de los usuarios, que puede resultar complicado de prever. Para obtener más información, consulte los siguientes artículos técnicos.

<table data-morhtml="true" cellspacing="0" border="1">
<tr data-morhtml="true">
   <th data-morhtml="true" align="left" valign="top">&Aacute;mbito</th>
   <th data-morhtml="true" align="left" valign="top">Recursos</th>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Plan</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/manage/services/web-sites/choose-web-app-service">Sitios web, Servicios en la nube y M&aacute;quinas virtuales de Azure: cu&aacute;ndo usar cada uno</a>.</td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Creaci&oacute;n</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-create-websites/">Creaci&oacute;n e implementaci&oacute;n de un sitio web</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Implementaci&oacute;n</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/develop/net/common-tasks/publishing-with-git/">Publicaci&oacute;n del control de c&oacute;digo fuente a Sitios web Azure</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-create-websites/">Implementaci&oacute;n de una aplicaci&oacute;n web ASP.NET en un sitio web de Azure</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Redes sociales</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/develop/net/tutorials/web-site-with-sql-database/">Implementaci&oacute;n de una aplicaci&oacute;n ASP.NET MVC segura con suscripci&oacute;n, OAuth y Base de datos SQL</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx">Create a Facebook app using ASP.NET MVC Facebook Templates</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx">Facebook application template and library for ASP.NET MVC</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Escala</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-scale-websites/">Escalaci&oacute;n de sitios web</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Medios enriquecidos</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://msdn.microsoft.com/en-us/library/windowsazure/dn223282.aspx">Introducci&oacute;n a los escenarios y conceptos de los Servicios multimedia de Azure</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Supervisi&oacute;n</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/">Supervisi&oacute;n de sitios web</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Recepci&oacute;n de notificaciones de alerta y administraci&oacute;n de reglas de alerta en Azure</a>.</td>
</tr>
</table>


