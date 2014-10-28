<properties title="Securing an Azure Website" pageTitle="Securing an Azure Website." description="Learn how to secure an Azure Website." metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

## Protección de una aplicación web en un Sitio web Azure

Uno de los retos que plantea el desarrollo de una aplicación web es cómo proporcionar un servicio seguro y protegido para sus clientes. En este artículo conocerá las características de Sitios web Azure que pueden proteger su aplicación web.

> [WACOM.NOTE] No es el objetivo de este documento abarcar una completa exposición de las consideraciones de seguridad para las aplicaciones basadas en web. Como punto de inicio para conocer más en profundidad la cuestión de la protección de las aplicaciones web, consulte el [proyecto de código abierto sobre seguridad en aplicaciones web (OWASP)][proyecto de código abierto sobre seguridad en aplicaciones web (OWASP)], concretamente el [proyecto de los 10 principales][proyecto de los 10 principales], que enumera los 10 principales errores de seguridad en aplicaciones web de acuerdo con los miembros de OWASP.

### Tabla de contenido

-   [Protección de comunicaciones][Protección de comunicaciones]
-   [Protección de desarrollo][Protección de desarrollo]
-   [Pasos siguientes][Pasos siguientes]

## <a name="https"></a>Protección de comunicaciones

Si utiliza el nombre de dominio ***.azurewebsites.net** que se crea para su sitio web, puede utilizar de inmediato HTTPS, ya que se proporciona un certificado SSL para todos los nombres de dominio del tipo* **.azurewebsites.net**. Si el sitio utiliza un [nombre de dominio personalizado][nombre de dominio personalizado], puede cargar un certificado SSL para habilitar HTTPS para el dominio personalizado.

Para obtener más información, consulte [Habilitación de HTTPS para un sitio web de Azure][Habilitación de HTTPS para un sitio web de Azure].

### Imposición de HTTPS

Sitios web Azure *no* impone el uso de HTTPS, los visitantes pueden seguir teniendo acceso a su sitio mediante HTTP, lo cual puede exponer información confidencial. Para imponer el uso de HTTPS, recurra al módulo **URL Rewrite**. El módulo URL Rewrite se incluye con Sitios web Azure, y le permite definir reglas que se aplican a las solicitudes entrantes antes de que las solicitudes lleguen a su aplicación. Se puede utilizar para aplicaciones escritas en cualquier lenguaje de programación admitido por Sitios web Azure.

> [WACOM.NOTE] Las aplicaciones .NET MVC deben utilizar el filtro [RequireHttps][RequireHttps] en lugar de URL Rewrite. Para obtener más información acerca del uso de RequireHttps, consulte [Implementación de una aplicación ASP.NET MVC 5 segura en un Sitio web Azure][Implementación de una aplicación ASP.NET MVC 5 segura en un Sitio web Azure].
>
> Para obtener información sobre la redirección programática de las solicitudes utilizando otros lenguajes y marcos de programación, consulte la documentación de esas tecnologías.

Las reglas de URL Rewrite se definen en un archivo **web.config** almacenado en la raíz de su aplicación. El ejemplo siguiente contiene una regla básica de URL Rewrite que impone el uso de HTTPS a todo el tráfico entrante.

<a name="example"></a>**Web.Config de ejemplo para URL Rewrite**

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <rewrite>
          <rules>
            <rule name="Force HTTPS" enabled="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{HTTPS}" pattern="off" />
              </conditions>
              <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>

Esta regla funciona devolviendo un código de estado HTTP de 301 (redirección permanente) cuando el usuario solicita una página mediante HTTP. El 301 redirige la solicitud a la misma URL que el visitante solicitó, pero sustituye la parte de HTTP de la solicitud por HTTPS. Por ejemplo, <HTTP://contoso.com> se redirigiría a <HTTPS://contoso.com>.

> [WACOM.NOTE] Si la aplicación está escrita en **Node.js**, **PHP**, **Python Django** o **Java**, probablemente no incluye un archivo web.config. Sin embargo, **Node.js**, **Python Django** y **Java** sí utilizan los tres un archivo web.config cuando se hospedan en Sitios web Azure. Azure crea automáticamente el archivo durante la implementación, así que no se ve nunca. Si incluye uno como parte de su aplicación, sobrescribirá el que Azure genera automáticamente.

### .NET

En el caso de las aplicaciones .NET, modifique el archivo web.config por su aplicación y agregue la sección **\<rewrite\>** del [ejemplo][ejemplo] a la sección **\<system.WebServer\>**.

Si el archivo web.config ya incluye una sección **\<rewrite\>**, agregue el valor de **\<rule\>** del [ejemplo][ejemplo] como primera entrada en la sección **\<rules\>**.

### PHP

En el caso de las aplicaciones PHP, guarde el [ejemplo][ejemplo] como archivo web.config en la raíz de su aplicación y, a continuación, implemente de nuevo la aplicación en su Sitio web Azure.

### Node.js, Python Django y Java

Se crea automáticamente un archivo web.config para aplicaciones Node.js, Python Django y Java si estas no proporcionan uno, pero dicho archivo solo existe en el servidor porque se crea durante la implementación. El archivo generado automáticamente contiene valores de configuración que le indican a Azure cómo hospedar su aplicación.

Para recuperar y modificar el archivo generado automáticamente desde el Sitio web, siga los pasos que se enumeran a continuación.

1.  Descargue el archivo mediante FTP (consulte [Carga/descarga de archivos a través de FTP y recopilación de registros de diagnóstico][Carga/descarga de archivos a través de FTP y recopilación de registros de diagnóstico]).

2.  Agréguelo a la raíz de su aplicación.

3.  Agregue las reglas de reescritura utilizando la siguiente información.

    -   **Node.js y Python Django**

        El archivo web.config generado por las aplicaciones Node.js y Python Django ya tendrá una sección **\<rewrite\>**, que contiene entradas de tipo **\<rule\>** que son necesarias para el correcto funcionamiento del sitio. Para imponer el uso de HTTPS en el sitio, agregue el valor de **\<rule\>** del ejemplo como primera entrada en la sección **\<rules\>**. Esto forzará el uso de HTTPS y dejará el resto de reglas intactas.

    -   **Java**

        El archivo web.config para aplicaciones Java que utilizan Apache Tomcat no contiene una sección **\<rewrite\>**; así pues, debe agregar la sección **\<rewrite\>** del ejemplo en la sección **\<system.webServer\>**.

4.  Implemente el proyecto de nuevo (incluyendo en archivo web.config actualizado) en Azure.

Una vez que implemente un archivo web.config con una regla de reescritura para imponer el uso de HTTPS, esta debería surtir efecto de inmediato y redirigir todas las solicitudes a HTTPS.

Para obtener más información sobre el módulo URL Rewrite para IIS, consulte la documentación de [URL Rewrite][URL Rewrite].

## <a name="develop"></a>Protección de desarrollo

### Perfiles de publicación y configuración de publicación

Al desarrollar aplicaciones, realizar tareas de mantenimiento o automatizar tareas mediante herramientas como **Visual Studio**, **Web Matrix**, **Azure PowerShell** o la **Interfaz de línea de comandos entre plataformas** de Azure, puede utilizar un archivo de *configuración de publicación* o un *perfil de publicación*. Ambos le autentican en Azure, y deberían estar protegidos para evitar el acceso no autorizado.

-   Un archivo de **configuración de publicación** contiene

    -   El identificador de suscripción a Azure

    -   Un certificado de administración que le permite realizar tareas de administración para su suscripción *sin tener que proporcionar un nombre de cuenta o contraseña*.

-   Un archivo de **perfil de publicación** contiene

    -   Información para publicación en su Sitio web Azure

Si utiliza una herramienta que emplea configuración de publicación o perfil de publicación, importe el archivo que contiene la configuración o el perfil de publicación en dicha herramienta y, a continuación, **elimine** el archivo. Si debe conservar el archivo, por ejemplo para compartirlo con otros que trabajan en el proyecto, almacénelo en una ubicación segura, como un directorio **cifrado** con permisos restringidos.

Además, debería asegurarse de que se protegen las credenciales importadas. Por ejemplo, tanto **Azure PowerShell** como la **Interfaz de línea de comandos entre plataformas de Azure** almacenan la información importada en su **directorio particular** (*~* en Linux o sistemas OS X y */usuarios/sunombredeusuario* en sistemas Windows). Para conseguir una seguridad adicional, **cifre** estas ubicaciones mediante herramientas de cifrado disponibles para su sistema operativo.

### Valores de configuración y cadenas de conexión

Es habitual almacenar cadenas de conexión, credenciales de autenticación y otra información de tipo confidencial en archivos de configuración. Lamentablemente, estos archivos pueden resultar expuestos en su sitio web o podrían aparecer en búsquedas de un repositorio público, con lo que la información podría quedar al descubierto.

Sitios web Azure le permite almacenar la información de configuración como parte del entorno de tiempo de ejecución de Sitios web en forma de **configuración de aplicación** y **cadenas de conexión**. Estos valores se exponen a su aplicación en el tiempo de ejecución a través de *variables de entorno* para la mayoría de lenguajes de programación. En el caso de las aplicaciones .NET, estos valores se insertan en la configuración de .NET en el tiempo de ejecución.

**Configuración de aplicación** y **cadenas de conexión** se pueden configurar mediante el Portal de administración de Azure o herramientas como PowerShell o la Interfaz de la línea de comandos entre plataformas de Azure.

Para obtener más información sobre configuración de aplicación y cadenas de conexión, consulte [Configuración de Sitios web][Configuración de Sitios web].

### FTPS

Azure proporciona acceso FTP seguro al sistema de archivos para el sitio web a través de **FTPS**. Esto le permite tener acceso seguro al código de la aplicación en el Sitio web, así como a los registros de diagnóstico. El vínculo FTPS para el sitio web se puede encontrar en el **Panel** del sitio, en el [Portal de administración de Azure][Portal de administración de Azure].

Para obtener más información acerca de FTPS, consulte [File Transfer Protocol][File Transfer Protocol].

## Pasos siguientes

Para obtener más información sobre la seguridad en la plataforma de Azure, aprender cómo notificar un **abuso o incidente de seguridad** o informar a Microsoft de que va a realizar una **prueba de penetración** en su sitio, consulte la sección de seguridad del [Centro de confianza de Microsoft Azure][Centro de confianza de Microsoft Azure].

Para obtener más información sobre los archivos **web.config** o **applicationhost.config** en Sitios web Azure, consulte [Opciones de configuración desbloqueadas en Sitios web Azure][Opciones de configuración desbloqueadas en Sitios web Azure].

Para obtener información sobre información de registro para Sitios web Azure, que puede resultar útil en la detección de ataques, consulte [Habilitación del registro de diagnóstico][Habilitación del registro de diagnóstico].

  [proyecto de código abierto sobre seguridad en aplicaciones web (OWASP)]: https://www.owasp.org/index.php/Main_Page
  [proyecto de los 10 principales]: https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project
  [Protección de comunicaciones]: #https
  [Protección de desarrollo]: #develop
  [Pasos siguientes]: #next
  [nombre de dominio personalizado]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-custom-domain-name/
  [Habilitación de HTTPS para un sitio web de Azure]: /es-es/documentation/articles/web-sites-configure-ssl-certificate/
  [RequireHttps]: http://msdn.microsoft.com/es-es/library/system.web.mvc.requirehttpsattribute.aspx
  [Implementación de una aplicación ASP.NET MVC 5 segura en un Sitio web Azure]: /es-es/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [ejemplo]: #example
  [Carga/descarga de archivos a través de FTP y recopilación de registros de diagnóstico]: http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx
  [URL Rewrite]: http://www.iis.net/downloads/microsoft/url-rewrite
  [Configuración de Sitios web]: /es-es/documentation/articles/web-sites-configure/
  [Portal de administración de Azure]: https://manage.windowsazure.com
  [File Transfer Protocol]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
  [Centro de confianza de Microsoft Azure]: /es-es/support/trust-center/security/
  [Opciones de configuración desbloqueadas en Sitios web Azure]: http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/
  [Habilitación del registro de diagnóstico]: /es-es/documentation/articles/web-sites-enable-diagnostic-log/
