<properties linkid="websites-business-application" urlDisplayName="Create a Line-of-Business Application on Azure Web Sites" pageTitle="Create a Line-of-Business Application on Azure Web Sites" metaKeywords="Web Sites" description="This guide provides a technical overview of how to use Azure Web Sites to create intranet, line-of-business applications. This includes authentication strategies, service bus relay, and monitoring." umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" title="Create a Line-of-Business Application on Azure Web Sites" authors="" />

Creación de una aplicación de línea de negocio en Sitios web Azure
==================================================================

En esta guía se ofrece información general de carácter técnico sobre cómo utilizar Sitios web Azure para crear aplicaciones de línea de negocio. En el ámbito de este documento, estas aplicaciones se consideran aplicaciones de intranet que deben protegerse para utilizarse internamente con fines empresariales. Las aplicaciones empresariales presentan dos características distintivas. Por un lado, requieren autenticación, normalmente en un directorio corporativo. Por otro lado, suelen requerir algún tipo de acceso a los datos y servicios locales o integración con ellos. Esta guía se centra en el desarrollo de aplicaciones empresariales en [Sitios web Azure](/en-us/documentation/services/web-sites/). Sin embargo, en determinadas situaciones [Servicios en la nube Azure](/en-us/documentation/services/cloud-services/) o [Máquinas virtuales Azure](/en-us/documentation/services/virtual-machines/) se ajustarían mejor a sus requisitos. Es importante consultar las diferencias entre estas opciones en el tema [Sitios web, Servicios en la nube y Máquinas virtuales de Azure: cuándo usar cada uno](/en-us/manage/services/web-sites/choose-web-app-service).

En esta guía se tratan los siguientes temas:

-   [Evaluación de las ventajas](#benefits)
-   [Selección de una estrategia de autenticación](#authentication)
-   [Creación de un sitio web de Azure compatible con la autenticación](#createintranetsite)
-   [Uso del bus de servicio para la integración con los recursos locales](#servicebusrelay)
-   [Supervisión de la aplicación](#monitor)

**Nota:**

En esta guía se presentan algunos de los temas y tareas más comunes relacionados con el desarrollo de sitios .COM de acceso público. Sin embargo, existen otras funciones de Sitios web Azure que puede utilizar en su implementación concreta. Para obtener información acerca de estas funciones, consulte también las otras guías sobre [presencia web mundial](http://www.windowsazure.com/en-us/manage/services/web-sites/global-web-presence-solution-overview/) y [campañas de marketing digital](http://www.windowsazure.com/en-us/manage/services/web-sites/digital-marketing-campaign-solution-overview) (en inglés).

Evaluación de las ventajas
--------------------------

Dado que las aplicaciones de línea de negocio suelen estar dirigidas a usuarios corporativos, debe tener en cuenta las razones para usar la nube en lugar de los recursos e infraestructura corporativos locales. En primer lugar, están algunas de las ventajas habituales de la migración a la nube, como la posibilidad de escalar o reducir verticalmente la capacidad con cargas de trabajo dinámicas. Tomemos como ejemplo una aplicación para administrar las revisiones anuales del rendimiento. La mayor parte del año una aplicación de este tipo tendrá muy poco tráfico; sin embargo, durante el período de revisión, el tráfico aumentará notablemente en el caso de las empresas de gran tamaño. Azure ofrece opciones de escalado que permiten a la empresa escalar su capacidad horizontalmente para soportar la carga durante el período de revisión de mucho tráfico y ahorrar dinero volviendo a reducir su capacidad el resto del año. Otra ventaja de la nube es la posibilidad de centrarse en el desarrollo de aplicaciones en lugar de en la adquisición y administración de la infraestructura.

Además de estas ventajas estándar, al migrar una aplicación empresarial a la nube se posibilita que los empleados y asociados la utilicen desde cualquier lugar. Los usuarios no necesitan estar conectados a la red corporativa todo el tiempo para utilizar la aplicación, con lo que el departamento de TI evita complejas soluciones de proxy inverso. Existen diversas opciones de autenticación para garantizar la protección del acceso a las aplicaciones de empresa. Estas opciones se describen en las siguientes secciones de esta guía.

Selección de una estrategia de autenticación
--------------------------------------------

En el contexto de las aplicaciones empresariales, es esencial elegir una buena estrategia de autenticación. Existen varias opciones:

-   Utilizar el [servicio Azure Active Directory](/en-us/documentation/services/active-directory/). Puede utilizarlo como un directorio independiente, o bien sincronizarlo con un directorio Active Directory local. De este modo, las aplicaciones interactuarán con Azure Active Directory para autenticar a los usuarios. Para obtener información general acerca de esta estrategia, consulte [Uso de Azure Active Directory](/en-us/manage/windows/fundamentals/identity/#ad).
-   Utilizar Máquinas virtuales y Red virtual Azure para instalar Active Directory. Esta opción le permite ampliar una instalación de Active Directory local a la nube. También puede optar por usar los servicios de federación de Active Directory (ADFS) para federar las solicitudes de identidad en el directorio AD local. De este modo, la autenticación de la aplicación Azure pasará por los ADFS para llegar al directorio Active Directory local. Para obtener más información acerca de esta estrategia, consulte [Ejecución de Windows Server Active Directory en máquinas virtuales](/en-us/manage/windows/fundamentals/identity/#adinvm) y [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx).
-   Utilizar un servicio intermediario, como el [servicio de control de acceso de Azure](http://msdn.microsoft.com/library/windowsazure/hh147631.aspx) (ACS), para usar varios servicios de identidad con el fin de autenticar a los usuarios. Con esta opción se logra una abstracción para la autenticación a través de Active Directory o a través de otro proveedor de identidades. Para obtener más información, consulte [Uso del control de acceso de Azure Active Directory](/en-us/manage/windows/fundamentals/identity/#ac).

En el caso que nos ocupa, la primera opción, consistente en el uso de Azure Active Directory, es el camino más rápido para implementar una estrategia de autenticación para la aplicación. El resto de esta guía se centra en Azure Active Directory. Sin embargo, dependiendo de los requisitos de su empresa, es posible que alguna de las otras dos soluciones resulte más apropiada. Por ejemplo, si no se le permite sincronizar la información de identidad con la nube, la solución de los ADFS puede ser la más conveniente. O bien, si debe ofrecer compatibilidad con otros proveedores de identidades, como Facebook, la solución del ACS sería más adecuada.

Antes de empezar a configurar un nuevo directorio de Azure Active Directory, compruebe si los servicios existentes, como Office 365 o Windows Intune, ya utilizan uno. De ser así, debe asociar la suscripción existente a la suscripción de Azure. Para obtener más información, consulte [¿Qué es un inquilino de Azure AD?](http://technet.microsoft.com/en-us/library/jj573650.aspx).

Si actualmente no cuenta con ningún servicio que utilice Azure Active Directory, puede crear un nuevo directorio en el Portal de administración. Utilice la sección **Active Directory** del Portal de administración para tal fin.

![Azure AD para aplicaciones empresariales](./media/web-sites-business-application-solution-overview/BusinessApplications_AzureAD.png)

Una vez creado el directorio, tiene la opción de crear y administrar usuarios, aplicaciones integradas y dominios.

![Usuarios de AD para aplicaciones empresariales](./media/web-sites-business-application-solution-overview/BusinessApplications_AD_Users.png)

Para obtener información detallada acerca de estos pasos, consulte [Incorporación del inicio de sesión a la aplicación web utilizando Azure AD](http://msdn.microsoft.com/library/windowsazure/dn151790.aspx). Si va a utilizar el nuevo directorio como recurso independiente, el siguiente paso consiste en desarrollar aplicaciones que se integren con él. Sin embargo, si dispone de identidades locales de Active Directory, lo habitual es sincronizarlas con el nuevo directorio Azure Active Directory. Para obtener más información acerca de cómo hacerlo, consulte [Descripción general de la integración de directorios](http://technet.microsoft.com/en-us/library/jj573653.aspx).

Una vez creado y poblado el directorio, debe crear aplicaciones web que requieran autenticación y, a continuación, integrarlas con el directorio. Estos pasos se explican en las dos secciones siguientes.

Creación de un sitio web de Azure compatible con la autenticación
-----------------------------------------------------------------

En la guía sobre presencia web mundial, explicamos varias opciones para crear e implementar un sitio web. Si es la primera vez que utiliza Sitios web Azure, es recomendable que [consulte esa información](/en-us/manage/services/web-sites/global-web-presence-solution-overview/). Los usuarios que desean una aplicación web de intranet que utilice la autenticación de Windows suelen recurrir a una aplicación ASP.NET en Visual Studio. Una de las razones es la estrecha integración y compatibilidad con este tipo de aplicación que ofrecen ASP.NET y Visual Studio.

Por ejemplo, a la hora de crear un proyecto ASP.NET MVC 4 en Visual Studio, en el cuadro de diálogo de creación de proyectos aparece la opción de crear una **aplicación de intranet**.

![Aplicación de intranet en VS para aplicaciones empresariales](./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IntranetApp.png)

Esta opción hace cambios en la configuración del proyecto para que este sea compatible con la autenticación de Windows. En concreto, el archivo web.config establece el atributo **mode** del elemento **authentication** en **Windows**. Este cambio debe realizarlo manualmente si crea un proyecto ASP.NET diferente, por ejemplo, un proyecto de formularios web, o si trabaja con un proyecto existente.

En el caso de los proyectos MVC, también debe cambiar dos valores de la ventana de propiedades del proyecto. Establezca **Autenticación de Windows** en **Habilitada** y **Autenticación anónima** en **Deshabilitada**.

![Propiedades de VS para aplicaciones empresariales](./media/web-sites-business-application-solution-overview/BusinessApplications_VS_Properties.png)

Si desea utilizar Azure Active Directory para la autenticación, debe registrar la aplicación en el directorio y, a continuación, modificar su configuración para establecer conexión. Esto puede hacerse de dos maneras en Visual Studio:

-   [Herramienta de identidades y acceso para Visual Studio](#identityandaccessforvs)
-   [Herramientas de Microsoft ASP.NET para Azure Active Directory](#aspnettoolsforwaad)

### Herramienta de identidades y acceso para Visual Studio:

Un método consiste en utilizar la [herramienta de identidades y acceso](http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e) (que puede descargar e instalar). Esta herramienta se integra con Visual Studio en el menú contextual del proyecto. Las siguientes instrucciones y capturas de pantalla proceden de Visual Studio 2012. Haga clic con el botón secundario en el proyecto y seleccione **Identity and Access**. Debe configurar tres elementos. En la pestaña **Proveedores**, debe escribir un valor en **Path to the STS metadata document** y en **APP ID URI** (para obtener estos valores, consulte la sección acerca del [registro de aplicaciones en Azure Active Directory](#registerwaadapp)).

![Identidad y acceso en VS para aplicaciones empresariales](./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IdentityAndAccess.png)

Por último, debe realizar un cambio en la pestaña **Configuración** del cuadro de diálogo **Identity and Access**. Debe activar la casilla **Enable web farm cookies**. Para obtener información detallada acerca de estos pasos, consulte [Incorporación del inicio de sesión a la aplicación web utilizando Azure AD](http://msdn.microsoft.com/library/windowsazure/dn151790.aspx).

#### Registro de aplicaciones en Azure Active Directory:

Para poder completar la información de la pestaña **Proveedores**, debe registrar la aplicación en Azure Active Directory. En el Portal de administración de Azure, en la sección **Active Directory**, seleccione su directorio y, a continuación, abra la pestaña **Applications**. De este modo, podrá agregar el sitio web de Azure utilizando la URL. Tenga en cuenta que al seguir estos pasos inicialmente se establece la URL en la dirección localhost facilitada para la depuración local en Visual Studio. Más adelante, cuando implemente el sitio web, se cambiará a la URL real de este.

![Aplicaciones integradas de AD para aplicaciones empresariales](./media/web-sites-business-application-solution-overview/BusinessApplications_AD_IntegratedApps.png)

Una vez agregado el sitio web, el portal proporciona tanto la ruta al documento de metadatos de STS (la **URL del documento de metadatos de Federation**) como el **URI de identificación de la aplicación**. Estos valores se utilizan en la pestaña **Proveedores** del cuadro de diálogo **Identity and Access** de Visual Studio.

![Aplicación agregada de AD para aplicaciones empresariales](./media/web-sites-business-application-solution-overview/BusinessApplications_AD_AppAdded.png)

### Herramientas de Microsoft ASP.NET para Azure Active Directory:

Una manera alternativa de realizar los pasos anteriores consiste en utilizar las [herramientas de Microsoft ASP.NET para Azure Active Directory](http://go.microsoft.com/fwlink/?LinkID=282306). Para ello, haga clic en el elemento **Enable Azure Authentication** del menú **Proyecto** de Visual Studio. De este modo, aparece un sencillo cuadro de diálogo donde debe escribir la dirección de su dominio de Azure Active Directory (no la URL de la aplicación).

![Habilitación de la autenticación en VS para aplicaciones empresariales](./media/web-sites-business-application-solution-overview/BusinessApplications_VS_EnableAuth.png)

Si es el administrador de ese dominio de Active Directory, active la casilla **Provision this application in the Azure AD**. Así se registrará la aplicación en Active Directory. Si no es el administrador, desactive esa casilla y facilite la información que se va a mostrar al administrador. El administrador puede utilizar el Portal de administración para crear una aplicación integrada siguiendo los pasos descritos anteriormente para la herramienta de identidades y acceso. Para obtener información detallada acerca de cómo usar las herramientas de ASP.NET para Azure Active Directory, consulte [Azure Authentication](http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication).

A la hora de administrar la aplicación de línea de negocio, puede utilizar cualquiera de los sistemas de control de código fuente compatibles para la implementación. Sin embargo, debido al alto grado de integración con Visual Studio de este ejemplo, es probable que elija Team Foundation Service (TFS) como sistema de control de código fuente. Si es el caso, tenga en cuenta que Sitios web Azure ofrece integración con TFS. En el Portal de administración, abra la pestaña **Panel** del sitio web. A continuación, seleccione **Set up deployment from source control**. Siga las instrucciones sobre cómo usar TFS.

![Implementación de aplicaciones empresariales](./media/web-sites-business-application-solution-overview/BusinessApplications_Deploy.png)

Uso del bus de servicio para la integración con los recursos locales
--------------------------------------------------------------------

Muchas aplicaciones de línea de negocio deben integrarse con los datos y servicios locales. Existen varias razones por las que no es posible migrar determinados tipos de datos a la nube. Estas razones pueden ser prácticas o reglamentarias. Si se encuentra en la fase de planificación y es momento de decidir qué datos hospedar en Azure y qué datos conservar en el entorno local, es importante revisar los recursos del [Centro de confianza de Azure](/en-us/support/trust-center/). Las aplicaciones web híbridas se ejecutan en Azure y obtienen acceso a recursos que deben permanecer en el entorno local.

Si utiliza Máquinas virtuales o Servicios en la nube, puede recurrir a una red virtual para conectar las aplicaciones de Azure con una red corporativa. Sin embargo, Sitios web no admite redes virtuales, por lo que la mejor manera de llevar a cabo este tipo de integración con Sitios web es mediante el [servicio de retransmisión de bus de servicio de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj860549.aspx). El servicio de retransmisión de bus de servicio permite a las aplicaciones en la nube conectarse de manera segura con servicios de WCF que se ejecutan en una red corporativa. El bus de servicio permite esta comunicación sin abrir puertos de firewall.

En el diagrama que aparece a continuación, la aplicación en la nube y el servicio de WCF local se comunican con el bus de servicio a través de un espacio de nombres creado previamente. El servicio de WCF local tiene acceso a datos y servicios internos que no se pueden migrar a la nube. Asimismo, registra un extremo del espacio de nombres. El sitio web que se ejecuta en Azure también se conecta con este extremo del bus de servicio. Basta con que puedan realizar solicitudes HTTP públicas salientes para completar este paso.

![Retransmisión de bus de servicio para aplicaciones empresariales](./media/web-sites-business-application-solution-overview/BusinessApplications_ServiceBusRelay.png)

A continuación, el bus de servicio conecta la aplicación en la nube con el servicio de WCF local. Esto proporciona una arquitectura básica para crear aplicaciones híbridas que utilicen servicios y recursos tanto de Azure como locales. Para obtener más información, consulte [Uso del servicio de retransmisión de bus de servicio](/en-us/develop/net/how-to-guides/service-bus-relay/) y el tutorial [Tutorial sobre mensajería retransmitida del bus de servicio](http://msdn.microsoft.com/en-us/library/windowsazure/ee706736.aspx). En el siguiente vínculo podrá consultar un ejemplo de uso de esta técnica: [Enterprise Pizza: Conexión de sitios web a los recursos locales utilizando el bus de servicio](http://code.msdn.microsoft.com/windowsazure/Enterprise-Pizza-e2d8f2fa).

Supervisión de la aplicación
----------------------------

Las aplicaciones empresariales se benefician de las funciones estándar de los sitios web, como el escalado y la supervisión. En el caso de las aplicaciones empresariales que soportan cargas variables dependiendo del día o la hora, la función de autoescalado (vista previa) puede ayudar a escalar el sitio horizontalmente y luego devolverlo a sus dimensiones originales para utilizar los recursos de manera eficiente. Entre las opciones de supervisión se incluye la supervisión de los extremos y de las cuotas. Todo esto se explica en detalle en las guías sobre [presencia web mundial](/en-us/manage/services/web-sites/global-web-presence-solution-overview/) y [campañas de marketing digital](/en-us/manage/services/web-sites/digital-marketing-campaign-solution-overview).

Las necesidades de supervisión de las aplicaciones de línea de negocio pueden variar en función de la importancia que estas tengan para la empresa. En el caso de aplicaciones esenciales, es recomendable invertir en una solución de supervisión de terceros, como [New Relic](http://newrelic.com/azure).

Las aplicaciones de línea de negocio suele administrarlas el equipo de TI. Si se produce un error o un funcionamiento inesperado, el equipo de TI puede habilitar un registro más detallado y, a continuación, analizar los datos resultantes para identificar los problemas. En el Portal de administración, abra la pestaña **Configure** y revise las opciones de las secciones **Diagnóstico de aplicaciones** y **site diagnostics**.

![Diagnóstico para aplicaciones empresariales](./media/web-sites-business-application-solution-overview/BusinessApplications_Diagnostics.png)

Utilice los diferentes registros de aplicaciones y sitios para solucionar problemas del sitio web. Observe que algunas de las opciones indican **File System**, lo que quiere decir que los archivos de registro se guardan en el sistema de archivos del sitio. Es posible obtener acceso a ellos mediante FTP, Azure PowerShell o las herramientas de línea de comandos de Azure. Otras opciones indican **Almacenamiento**. Esto quiere decir que la información se envía a la cuenta de almacenamiento de Azure que especifique. En el apartado **Web Server Logging**, también tiene la opción de especificar una cuota de disco para el sistema de archivos o una directiva de retención para la opción de almacenamiento. De este modo, evitará que se almacene una cantidad ilimitada de datos de registro.

![Retención para diagnóstico de aplicaciones empresariales](./media/web-sites-business-application-solution-overview/BusinessApplications_Diag_Retention.png)

Para obtener más información acerca de la configuración del registro, consulte [Configuración de registros de diagnóstico y descarga para un sitio web](/en-us/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics).

Además de obtener acceso a los registros sin procesar mediante FTP o herramientas de almacenamiento, como el explorador de almacenamiento de Azure, también puede ver la información de registro en Visual Studio. Para obtener información detallada acerca de cómo utilizar estos registros para solucionar problemas, consulte [Solución de problemas de Sitios web Azure en Visual Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

Resumen
-------

Azure le permite hospedar aplicaciones de intranet seguras en la nube. Azure Active Directory ofrece la posibilidad de autenticar a los usuarios para que solo los miembros de la organización puedan obtener acceso a las aplicaciones. El servicio de retransmisión de bus de servicio ofrece un mecanismo para que las aplicaciones web se comuniquen con los servicios y datos locales. Este modelo de aplicación híbrida facilita la publicación de aplicaciones empresariales en la nube sin migrar también todos los datos y servicios dependientes. Una vez implementadas, las aplicaciones empresariales se benefician de las funciones estándar de escalado y supervisión proporcionadas por Sitios web Azure. Para obtener más información, consulte los siguientes artículos técnicos.

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
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Creaci&oacute;n e implementaci&oacute;n</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/develop/net/tutorials/get-started/">Implementaci&oacute;n de una aplicaci&oacute;n web ASP.NET en un sitio web de Azure</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/develop/net/tutorials/web-site-with-sql-database/">Implementaci&oacute;n de una aplicaci&oacute;n ASP.NET MVC en Azure</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Autenticaci&oacute;n</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/manage/windows/fundamentals/identity/">Informaci&oacute;n sobre las opciones de identidades de Azure</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/documentation/services/active-directory/">Servicio Azure Active Directory</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://technet.microsoft.com/en-us/library/jj573650.aspx">&iquest;Qu&eacute; es un inquilino de Azure AD?</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://msdn.microsoft.com/library/windowsazure/dn151790.aspx">Incorporaci&oacute;n del inicio de sesi&oacute;n a la aplicaci&oacute;n web utilizando Azure AD</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication">Azure Authentication</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Retransmisi&oacute;n de bus de servicio</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/develop/net/how-to-guides/service-bus-relay/">Uso del servicio de retransmisi&oacute;n de bus de servicio</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://msdn.microsoft.com/en-us/library/windowsazure/ee706736.aspx">Tutorial sobre mensajer&iacute;a retransmitida del bus de servicio</a></td>
</tr>
<tr data-morhtml="true">
   <td data-morhtml="true" valign="middle"><strong data-morhtml="true">Supervisi&oacute;n</strong></td>
   <td data-morhtml="true" valign="top">- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/">Supervisi&oacute;n de sitios web</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Recepci&oacute;n de notificaciones de alerta y administraci&oacute;n de reglas de alerta en Azure</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics">Configuraci&oacute;n de registros de diagn&oacute;stico y descarga para un sitio web</a><br data-morhtml="true" />- <a data-morhtml="true" href="http://www.windowsazure.com/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/">Soluci&oacute;n de problemas de Sitios web Azure en Visual Studio</a></td>
</tr>
</table>


