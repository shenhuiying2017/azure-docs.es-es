<properties linkid="websites-global-web-presence" urlDisplayName="Create a Global Web Presence on Azure Websites" pageTitle="Create a Global Web Presence on Azure Websites" metaKeywords="" description="This guide provides a technical overview of how to host your organization's (.COM) site on Azure Websites. This includes deployment, custom domains, SSL, and monitoring." metaCanonical="http://www.windowsazure.com/es-es/documentation/articles/web-sites-global-web-presence-solution-overview/" services="" documentationCenter="" title="Create a Global Web Presence on Azure Websites" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Creación de una presencia web global en Sitios web Azure

En esta guía se ofrece información general de carácter técnico acerca de cómo hospedar el sito (.COM) de su organización en Azure. A este escenario también se hace referencia como una presencia web global. Esta guía se centra en el uso de [Sitios web Azure][Sitios web Azure], ya que es la forma más fácil y rápida de crear, migrar, escalar y administrar una aplicación web en Azure. No obstante, algunos requisitos de la aplicación se prestan mejor a [Servicios en la nube de Azure][Servicios en la nube de Azure] o [Máquinas virtuales de Azure][Máquinas virtuales de Azure] con IIS. También son opciones excelentes para hospedar aplicaciones web. Si se encuentra en la fase de planificación inicial, consulte el documento [Sitios web, Servicios en la nube y Máquinas virtuales de Azure: cuándo usar cada uno][Sitios web, Servicios en la nube y Máquinas virtuales de Azure: cuándo usar cada uno]. En caso de que no exista ningún requisito para usar Servicios en la nube o Máquinas virtuales, recomendamos usar Sitios web para hospedar la presencia web global. En el resto de este documento se ofrecerá orientación acerca del uso de Sitios web con este escenario.

En esta guía se tratan los siguientes temas:

-   [Creación de un sitio web de Azure](#createwebsite)
-   [Implementación del sitio web](#deploywebsite)
-   [Incorporación de un dominio personalizado](#customdomain)
-   [Protección del sitio web con SSL](#ssl)
-   [Supervisión del sitio](#monitor)

<div class="dev-callout">
<strong>Nota:</strong>
<p>En esta gu&iacute;a se presentan algunos de los temas y tareas m&aacute;s comunes relacionados con el desarrollo de sitios .COM de acceso p&uacute;blico. Sin embargo, existen otras funciones de Sitios web Azure que puede utilizar en su implementaci&oacute;n concreta. Para revisar estas capacidades, consulte tambi&eacute;n las otras gu&iacute;as sobre <a href="http://www.windowsazure.com/es-es/manage/services/web-sites/digital-marketing-campaign-solution-overview">campa&ntilde;as de marketing digital</a> y <a href="http://www.windowsazure.com/es-es/manage/services/web-sites/business-application-solution-overview">aplicaciones empresariales</a>.</p>
</div>

## <a name="createwebsite"></a>Creación de un sitio web de Azure

En el Portal de administración de Azure puede crear un sitio web de Azure nuevo de varias formas. Si hace clic en el botón **New** en la parte inferior del portal, se abrirá el siguiente cuadro de diálogo:

![Creación de presencia global][Creación de presencia global]

Hay tres opciones para crear un sitio web nuevo: **Quick Create**, **Custom Create** y **From Gallery**. Con cada una de estas opciones, debe seleccionar una región de Azure adecuada para la mayor parte de la base de usuarios.

Si va a migrar un sitio existente, la opción **Custom Create** le permite crear o asociar una Base de datos SQL o MySQL. Esta opción también ofrece la posibilidad de especificar varias opciones de control de código fuente para la implementación, como GitHub o Team Foundation Server (TFS). Si ya administra el sitio web con un mecanismo de control de código fuente, esta opción le permite configurar el sitio web de Azure rápidamente para la implementación.

La opción **From Gallery** le permite configurar un sitio nuevo con varios marcos de trabajo, como Drupal o WordPress. Esto puede resultar útil para configurar con rapidez un sitio nuevo que posteriormente puede personalizar con el marco de trabajo seleccionado.

Como la mayoría de los servicios de Azure, debe seleccionar una región de Azure para el sitio web nuevo. Azure tiene muchas regiones en todo el mundo. Tras implementar el sitio web en alguna región, será posible obtener acceso a él en todo el mundo a través de Internet. No obstante, varias regiones ofrecen mayor flexibilidad. Un beneficio evidente es implementar los sitios en las regiones más próximas a los usuarios.

Para obtener pasos detallados para crear un sitio web nuevo, consulte [Empezar a utilizar Sitios web Azure y ASP.NET][Empezar a utilizar Sitios web Azure y ASP.NET].

## <a name="deploywebsite"></a>Implemente el sitio web

Hay varias formas de implementar sitios web en Azure. Si ha seleccionado un marco de trabajo desde la galería, ya dispondrá de un sitio inicial implementado. No obstante, para avanzar, debe configurar algún tipo de procedimiento de edición e implementación. Algunas de las opciones de implementación son:

-   Usar un cliente FTP
-   Implementar desde el control de código fuente
-   Publicar desde Visual Studio
-   Publicar desde [WebMatrix][WebMatrix]

Cada una de estas opciones tiene varias ventajas. La posibilidad de publicar desde un cliente FTP es una solución sencilla y completa para enviar archivos nuevos al sitio. También supone que todas las herramientas o procesos de publicación existentes que se basan en FTP puedan continuar funcionando con Sitios web Azure. El control de código fuente ofrece el mejor control de las versiones de contenido del sitio, porque se puede hacer un seguimiento de los cambios, publicarlos y realizar la reversión a las versiones anteriores en caso de que sea necesario. Las opciones para publicar directamente desde Visual Studio o Web Matrix resultan cómodas para los desarrolladores que usan cualquiera de estas herramientas. Un escenario útil para esta característica es durante las primeras fases de un proyecto o para la creación de prototipos. En ambos casos, la publicación y las pruebas frecuentes son posiblemente más convenientes desde el interior del entorno de desarrollo.

Muchas de las tareas de implementación de este escenario implican el uso de información en el Portal de administración de Azure. Vaya al sitio web, seleccione la pestaña **Panel** y, a continuación, busque la sección **quick glance**. En la captura de pantalla siguiente se muestran varias opciones.

![Vista rápida de web global][Vista rápida de web global]

Algunas herramientas de control de código fuente y clientes FTP requieren el acceso mediante nombre de usuario/contraseña. Para un sitio web nuevo, las credenciales no se crean automáticamente. No obstante, puede crearlas con facilidad; para ello, haga clic en **Reset your deployment credentials**. Tras completar esta tarea, puede usar cualquier cliente FTP para implementar el sitio web con la utilización de estas credenciales junto con la opción **FTP Host Name** en la misma página **Panel**.

![Configuración FTP para web global][Configuración FTP para web global]

Tenga en cuenta que el nombre de usuario para la implementación y el cliente FTP es una combinación del nombre del sitio web y el nombre de usuario que ha proporcionado. Por tanto, si el sitio fuera "<http://contoso.azurewebsite.net>" y si su nombre de usuario fuera "myuser", el nombre de usuario para la implementación y el cliente FTP sería "contoso\\myuser".

También tiene la opción de implementar mediante un servicio de administración de control de código fuente, como GitHub o TFS en línea. Haga clic en la opción **Set up deployment from source control**. A continuación, siga las instrucciones para el servicio o sistema de control de código fuente que elija. Para obtener instrucciones paso a paso sobre la publicación desde un repositorio Git local, consulte [Publicación en Sitios web Azure desde el control de código fuente][Publicación en Sitios web Azure desde el control de código fuente].

Si piensa usar Visual Studio para crear y administrar el sitio, puede elegir publicar directamente desde Visual Studio. Uno de los métodos consiste en hacer clic en la opción **Download the publish profile**. Esto le permitirá guardar un archivo publishsettings que puede importarse en Visual Studio para la publicación web.

<div class="dev-callout">
<strong>Nota:</strong>
<p>Es importante mantener el archivo <i>publishsettings</i> seguro y fuera del control de c&oacute;digo fuente, porque contiene los nombres de usuario y las contrase&ntilde;as para la implementaci&oacute;n y tambi&eacute;n cualquier cadena de conexi&oacute;n de base de datos vinculada.</p>
</div>

También es posible importar la información de la suscripción directamente a Visual Studio. Por ejemplo, considere un proyecto de ASP.NET local en Visual Studio. Haga clic con el botón secundario en el proyecto web y, a continuación, seleccione **Publicar**. El botón **Import** del cuadro de diálogo **Publish Web** le permite importar un archivo que contenga la configuración de la suscripción a Azure o el archivo publishsettings que ha descargado del panel Sitios web. En la captura de pantalla siguiente se muestran estas opciones.

![Publicación en VS para web global][Publicación en VS para web global]

Para obtener más información acerca de la publicación en Azure desde Visual Studio, consulte Implementación de una aplicación web ASP.NET en un sitio web de Azure.

Una opción para el desarrollo y la implementación es WebMatrix desde el Portal de administración de Azure.

![WebMatrix para web global][WebMatrix para web global]

Para obtener más información acerca de esta opción, consulte [Desarrollo e implementación de un sitio web con Microsoft WebMatrix][Desarrollo e implementación de un sitio web con Microsoft WebMatrix].

A pesar de que estos pasos ofrecen lo que necesita para implementar su sitio .COM, también debe crear un plan para administrar el ciclo de publicación de contenido en curso. Estas opciones podrían ir desde la reversión de una solución personalizada, pasando por las reimplementaciones periódicas de un sitio que cambia con poca frecuencia, y hasta un sistema de administración de contenido (CMS) completo. Si va a crear un sitio web nuevo, debe tener en cuenta que hay opciones en la galería para usar los marcos de CMS existentes, como [Drupal][Drupal] o [Umbraco][Umbraco].

## <a name="customdomain"></a>Incorporación de un dominio personalizado

Si se trata de su presencia web global, deberá asociar su nombre de dominio registrado al sitio web. Hay muchos proveedores externos que ofrecen servicios de registro de dominios. Cada uno de estos proveedores admite la creación de diferentes tipos de registros DNS para administrar el dominio. Un registro DNS ayuda a asignar una URL descriptiva, como "www.contoso.com", a la URL real o la dirección IP que hospeda el sitio.

<div class="dev-callout">
<strong>Nota:</strong>
<p>En la informaci&oacute;n que se expone a continuaci&oacute;n, se nombran dos tipos de registros DNS que resultan interesantes. En primer lugar, un registro CNAME puede redirigir desde una URL, como &quot;www.contoso.com&quot;, hasta una URL diferente, como &quot;contoso.azurewebsites.net&quot;. En segundo lugar, un registro A puede asignar una URL, como &quot;www.contoso.com&quot;, a una direcci&oacute;n IP, como 172.16.48.1.</p>
</div>

Para Sitios web Azure, primero debe crear un registro CNAME en el sitio web de Azure. Esta configuración se hace a través del sitio del registrador externo. A continuación se expone un registro CNAME de ejemplo.


<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Tipo</th>
   <th align="left" valign="top">Host</th>
   <th align="left" valign="top">Respuesta</th>
   <th align="left" valign="top">TTL</th>
</tr>
<tr>
   <td valign="top"><strong>CNAME</strong></td>
   <td valign="top">www.contoso.com</td>
   <td valign="top">contoso.azurewebsites.net</td>
   <td valign="top">8000</td>
</tr>
</table>



Si el dominio está recién registrado, la resolución del dominio en todos los servidores DNS puede llevar un día o más, dado que funcionan con las entradas DNS almacenadas en caché. No obstante, si el dominio ya existe, el cambio de CNAME debería producirse en un minuto. Tenga en cuenta que el registro CNAME ofrece una asignación entre el dominio (que debe cualificarse con un alias de subdominio, como "www") y la URL del sitio web de Azure. Ningún lado del registro CNAME incluye el prefijo "http://".

En el Portal de administración de Azure, compruebe que está utilizando los modos **compartido** o está\*\*\*\*ndar en la pestaña **Escala** (los dominios personalizados no se admiten para los sitios web **libres**). A continuación, vaya a la pestaña **Configure** y haga clic en el botón **Manage Domains**. Esto le permite asociar el sitio web al nombre de dominio personalizado.

![WebMatrix para web global][WebMatrix para web global]

Antes de colocar el dominio personalizado en la lista, primero debe ir al proveedor DNS y crear un registro CNAME para el dominio personalizado (www.contoso.com) que dirija a la URL del sitio web de Azure (contoso.azurewebsites.net). Después de la propagación, puede escribir el dominio personalizado en el cuadro de diálogo ilustrado en la captura de pantalla anterior. La presencia del registro CNAME para www.contoso.com que dirija a este sitio web garantiza disponer de la autoridad para usar dicho nombre de dominio con este sitio web. En este punto, puede crear un registro A con la dirección IP de la parte inferior del cuadro de diálogo.

| Tipo  | Host        | Respuesta   | TTL  |
|-------|-------------|-------------|------|
| **A** | contoso.com | 172.16.48.1 | 8000 |

Para obtener más información, consulte [Configuración de un nombre de dominio personalizado para un Sitio web Azure][Configuración de un nombre de dominio personalizado para un Sitio web Azure].

## <a name="ssl"></a>Protección del sitio web con SSL

Si el sitio contiene información de solo lectura, no es necesario proporcionar acceso seguro al sitio. No obstante, si recopila información de usuarios, realiza operaciones de comercio electrónico o administra otros datos delicados, debe proteger el sitio. La seguridad es un tema complejo y, por ello, en este documento no se pueden tratar todos los procedimientos y técnicas recomendados. No obstante, es importante destacar el proceso de habilitar la Capa de sockets seguros (SSL) para el sitio web. SSL permite a los usuarios conectar el sitio de una manera cifrada con direcciones HTTPS en lugar de HTTP. Hay pasos específicos necesarios para usar SSL con Sitios web Azure.

La plataforma Sitios web Azure ofrece automáticamente una conexión segura a la URL real del sitio. Por ejemplo, si el sitio fuera <http://contoso.azurewebsites.net> puede conectarse a través de SSL solo con cambiar "http" por "https", como en **https**://contoso.azurewebsites.net.

No obstante, si usa un nombre de dominio personalizado, debe realizar los pasos para cargar un certificado y habilitar SSL a través del Portal de administración de Azure para el sitio web. Los pasos siguientes ofrecen un resumen de este proceso, pero puede encontrar instrucciones detalladas en el tema [Configuración de un certificado SSL para un sitio web de Azure][Configuración de un certificado SSL para un sitio web de Azure].

En primer lugar, obtenga un certificado SSL de una entidad de certificación. Si va a proteger el dominio con varios subdominios (por ejemplo, www.contoso.com y staging.contoso.com), deberá obtener un certificado comodín (\*.contoso.com). Puede ser más caro, por lo que debe decidir si la flexibilidad de este tipo de certificado justifica el coste.

Tras obtener el certificado de la entidad de certificación, no basta con cargarlo en Azure con el mismo formato. Debe generar un archivo .pfx con el comando openssl. El comando openssl forma parte del proyecto OpenSSL. Los orígenes se distribuyen en el [sitio web de OpenSSL][sitio web de OpenSSL], pero normalmente puede encontrar también una versión precompilada de la herramienta en Internet. En el ejemplo siguiente, se usan un certificado, myserver.crt, y el archivo de clave privada, myserver.key, para crear un archivo .pfx.

    openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

Para cargar el certificado en Azure, primero vaya a la pestaña **Escala** y compruebe que ejecuta el modo **estándar**. El uso de SSL para dominios personalizados no se admite con los modos **libre** o **compartido**. En la pestaña **Configure**, haga clic en el botón **upload a certificate**.

![Carga de certificado para web global][Carga de certificado para web global]

A continuación, en la sección **ssl bindings**, asigne el certificado al nombre de dominio que protege. Hay dos opciones para esta asignación: SSL SNI y SSL basada en IP.

![Enlaces SSL para web global][Enlaces SSL para web global]

La opción **SSL basada en IP** es la forma tradicional de asignar la dirección IP dedicada pública al nombre de dominio. Funciona con todos los exploradores. La opción **SSL SNI** permite que varios dominios compartan la misma dirección IP y, a pesar de ello, tengan diferentes certificados SSL asociados para cada dominio. SSL SNI no funciona con algunos exploradores anteriores (para obtener más información sobre la compatibilidad, consulte la [entrada de Wikipedia para SSL SNI][entrada de Wikipedia para SSL SNI]). Hay un cargo mensual (prorrateado por horas) asociado a cada certificado SSL y el precio varía en función de si la opción de SSL se basa en IP o en SSL SNI. Para obtener información sobre el precio, consulte [Información sobre el precio de Sitios web][Información sobre el precio de Sitios web]. Para obtener más información acerca de este proceso, consulte [Configuración de un certificado SSL para un sitio web de Azure][Configuración de un certificado SSL para un sitio web de Azure].

## <a name="monitor"></a>Supervisión del sitio

Después de que el sitio controle activamente las solicitudes de los usuarios, es importante usar la supervisión. Por ejemplo, debe saber si la carga de usuario causa un tiempo elevado de CPU, que podría indicar la necesidad de escalar el sitio. Por otra parte, la ineficacia de la aplicación puede aumentar el tiempo de respuesta o producir errores. En esta sección se tratan algunas de las capacidades de supervisión integradas en el Portal de administración de Azure.

La pestaña **Monitor** contiene algunas métricas clave del sitio web en formato gráfico.

![Supervisión web global 1][Supervisión web global 1]

Puede personalizar las métricas en este gráfico con el botón Add Metrics.

![Supervisión web global 2][Supervisión web global 2]

Para los sitios que se ejecutan en modo **estándar**, también puede habilitar las alertas y la supervisión de extremos. En la pestaña **Configure**, vaya a la sección **monitoring** y configure un extremo. Este extremo se ejecuta desde una o varias ubicaciones especificadas y trata periódicamente de obtener acceso al sitio web. Se recopila información sobre los errores y el tiempo.

En la pestaña **Monitor**, este extremo parece mostrar el tiempo de respuesta. Si selecciona la métrica del extremo, puede agregar una regla de alertas si hace clic en el icono **Add Rule**.

![Supervisión web global 3][Supervisión web global 3]

La regla puede enviar correos electrónicos a los administradores o a otras personas cuando el tiempo de respuesta supera el umbral especificado.

![Supervisión web global 4][Supervisión web global 4]

Si observa que el sitio precisa de escalado, puede hacerlo en la pestaña **Escala** manualmente, o bien usar la característica Autoscale (Preview). La pestaña Escala ofrece opciones para escalar verticalmente (máquinas dedicadas más grandes) u horizontalmente (instancias compartidas adicionales o instancias dedicadas del mismo tamaño). No obstante, la característica Autoscale (Preview) solo admite el escalado horizontal. Para obtener información más detallada acerca de la supervisión de sitios web, consulte la sección "Escala según la demanda de los usuarios" del escenario [Campaña de marketing digital][Campaña de marketing digital]. Consulte también [Supervisión de sitios web][Supervisión de sitios web].

## <a name="summary"></a>Resumen

Para crear el sitio (.COM) de su organización, las tareas habituales comprenden la elección de un marco de desarrollo, la creación del sitio, la implementación, la asignación de dominios personalizados y la supervisión. Si se trata de sitios que deben proteger los datos de los usuarios, se recomienda encarecidamente usar SSL. En este artículo se ha ofrecido información general acerca de la ejecución de estas tareas con Sitios web Azure. Para obtener más información, consulte los siguientes artículos técnicos a los que se hace referencia en el documento.


<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">&Aacute;mbito</th>
   <th align="left" valign="top">Recursos</th>
</tr>
<tr>
   <td valign="middle"><strong>Plan</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/es-es/manage/services/web-sites/choose-web-app-service">Sitios web Azure, Servicios en la nube y M&aacute;quinas virtuales: cu&aacute;ndo usar cada uno</a>.</td>
</tr>
<tr>
   <td valign="middle"><strong>Creaci&oacute;n</strong></td>
   <td valign="top">- <a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-dotnet-get-started/">Introducci&oacute;n a Sitios web Azure y ASP.NET</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Implementaci&oacute;n</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/es-es/develop/net/common-tasks/publishing-with-git/">Publicaci&oacute;n en Sitios web Azure desde el control de c&oacute;digo fuente</a><br/>- <a href="http://www.windowsazure.com/es-es/develop/net/tutorials/get-started/">Implementaci&oacute;n de una aplicaci&oacute;n web ASP.NET en un sitio web de Azure</a><br/>- <a href="http://www.windowsazure.com/es-es/develop/net/tutorials/website-with-webmatrix/">Desarrollo e implementaci&oacute;n de un sitio web con Microsoft WebMatrix</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Dominios personalizados</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/es-es/develop/net/common-tasks/custom-dns-web-site/">Configuraci&oacute;n de un nombre de dominio personalizado para un sitio web de Azure.</a></td>
</tr>
<tr>
   <td valign="middle"><strong>SSL</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/es-es/develop/net/common-tasks/enable-ssl-web-site/">Configuraci&oacute;n de un certificado SSL para un sitio web de Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Monitor</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/es-es/manage/services/web-sites/how-to-monitor-websites/">Supervisi&oacute;n de sitios web</a></td>
</tr>
</table>


  [Sitios web Azure]: /es-es/documentation/services/web-sites/
  [Servicios en la nube de Azure]: /es-es/documentation/services/cloud-services/
  [Máquinas virtuales de Azure]: /es-es/documentation/services/virtual-machines/
  [Sitios web, Servicios en la nube y Máquinas virtuales de Azure: cuándo usar cada uno]: /es-es/manage/services/web-sites/choose-web-app-service
  [campañas de marketing digital]: http://www.windowsazure.com/es-es/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [aplicaciones empresariales]: http://www.windowsazure.com/es-es/manage/services/web-sites/business-application-solution-overview
  [Creación de presencia global]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Create.png
  [Empezar a utilizar Sitios web Azure y ASP.NET]: /es-es/documentation/articles/web-sites-dotnet-get-started
  [WebMatrix]: http://www.microsoft.com/web/webmatrix/
  [Vista rápida de web global]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_QuickGlance.png
  [Configuración FTP para web global]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_FTPSettings.png
  [Publicación en Sitios web Azure desde el control de código fuente]: /es-es/develop/net/common-tasks/publishing-with-git/
  [Publicación en VS para web global]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_VS_Publish.png
  [WebMatrix para web global]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_WebMatrix.png
  [Desarrollo e implementación de un sitio web con Microsoft WebMatrix]: /es-es/develop/net/tutorials/get-started/
  [Drupal]: https://drupal.org/
  [Umbraco]: http://umbraco.com/
  [Configuración de un nombre de dominio personalizado para un Sitio web Azure]: /es-es/develop/net/common-tasks/custom-dns-web-site/
  [Configuración de un certificado SSL para un sitio web de Azure]: /es-es/develop/net/common-tasks/enable-ssl-web-site/
  [sitio web de OpenSSL]: http://www.openssl.org/
  [Carga de certificado para web global]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Uplodate_Cert.png
  [Enlaces SSL para web global]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_SSL_Bindings.png
  [entrada de Wikipedia para SSL SNI]: http://en.wikipedia.org/wiki/Server_Name_Indication
  [Información sobre el precio de Sitios web]: /es-es/pricing/details/web-sites/#service-ssl
  [Supervisión web global 1]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor1.png
  [Supervisión web global 2]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor2.png
  [Supervisión web global 3]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor3.png
  [Supervisión web global 4]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor4.png
  [Campaña de marketing digital]: /es-es/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [Supervisión de sitios web]: /es-es/manage/services/web-sites/how-to-monitor-websites/
  [Sitios web Azure, Servicios en la nube y Máquinas virtuales: cuándo usar cada uno]: http://www.windowsazure.com/es-es/manage/services/web-sites/choose-web-app-service

