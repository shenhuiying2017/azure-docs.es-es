<properties linkid="manage-services-how-to-configure-websites" urlDisplayName="How to configure" pageTitle="How to configure web sites - Azure service management" metaKeywords="Azure websites, configuring Azure websites, Azure SQL database, Azure MySQL" description="Learn how to configure web sites in Azure, including how to configure a web site to use a SQL Database or MySQL database." metaCanonical="" services="web-sites" documentationCenter="" title="How to Configure Web Sites" authors="timamm" solutions="" manager="" editor="mollybos" />

Configuración de sitios web
===========================

En el Portal de administración de Azure puede cambiar las opciones de configuración para los sitios web y puede vincular el sitio web a otros recursos de Azure. Por ejemplo, puede vincular sitios web a una base de datos SQL para proporcionar funcionalidad adicional. También puede configurar los sitios web para utilizar una base de datos MySQL nueva o existente.

Tabla de contenido
------------------

-   [Cambio de las opciones de configuración para un sitio web](#howtochangeconfig)
-   [Configuración de un sitio web para utilizar una base de datos SQL](#howtoconfigSQL)
-   [Configuración de un sitio web para utilizar una base de datos MySQL](#howtoconfigMySQL)
-   [Configuración de un nombre de dominio personalizado](#howtodomain)
-   [Configuración de un sitio web para utilizar SSL](#howtoconfigSSL)
-   [Pasos siguientes](#next)

Cambio de las opciones de configuración para un sitio web
---------------------------------------------------------

Siga estos pasos para cambiar las opciones de configuración para un sitio web.

1.  En el Portal de administración, abra las páginas de administración del sitio web.
2.  Haga clic en la pestańa **Configure** para abrir la página de administración **Configure**.
3.  Defina las siguientes opciones de configuración para el sitio web según corresponda:
    -   **general**
        -   **.NET Framework Version**: Si la aplicación web utiliza .NET Framework, defina la versión de Framework que requiere la aplicación web.
        -   **PHP Version**: Si la aplicación web utiliza PHP, defina la versión de PHP que requiere la aplicación web.
        -   **Java Version**: Seleccione la versión mostrada de Java para habilitarla para la aplicación web o seleccione **OFF** para deshabilitar Java. Si habilita Java para su aplicación web, la opción **Web Container** le permite elegir entre las versiones Tomcat y Jetty.

            **Nota**: por razones técnicas, al habilitar Java para el sitio web se deshabilitan las opciones .NET, PHP y Python.

        -   **Python Version**: Muestra la versión de Python (no se puede configurar).
        -   **Managed Pipeline Mode**: De las dos opciones, **Classic** e **Integrated**, esta última es la opción predeterminada. Debe utilizar la opción Classic solo si tiene sitios web heredados que se ejecutan exclusivamente en versiones anteriores de IIS.
        -   **Platform**: Para sitios en modo estándar, puede elegir si desea que la aplicación se ejecute en un entorno de 32 o 64 bits. Los sitios en modo libre y compartido siempre se ejecutan en un entorno de 32 bits.
        -   **Web Sockets**: Elija **On** para permitir que su sitio web utilice aplicaciones de modelos de solicitud en tiempo real, como el chat.
        -   **Always On**: De manera predeterminada, los sitios web no se descargan si han estado inactivos durante algún tiempo. Esto permite que el sistema conserve recursos. Puede habilitar la configuración **Always On** para un sitio en modo estándar si es necesario que el sitio esté cargado en todo momento. Como es posible que los trabajos web continuos no se ejecuten de manera confiable si la opción **Always On** está deshabilitada, cuando tenga trabajos web continuos en ejecución en el sitio debe habilitar la opción **Always On**.
        -   **Edit in Visual Studio Online**: Seleccione **On** para permitir la edición de código activo con Visual Studio Online. Una vez que guarde este cambio en la configuración, en la sección **Quick Glance** de la pestańa PANEL aparecerá un vínculo llamado **Edit in Visual Studio Online**. Haga clic en el vínculo para editar su sitio web directamente en línea. Si necesita autenticarse, puede utilizar sus credenciales de implementación básicas.

            **Nota**: si tiene habilitada la opción "implementación desde el control de código fuente", es posible que una implementación sobrescriba los cambios que realiza en el editor de Visual Studio Online. Por lo tanto, es mejor no utilizar "implementación desde el control de código fuente" si desea editar el contenido del sitio directamente con Visual Studio Online.

    -   **certificates**: Solo en modo estándar, puede hacer clic en **upload** para cargar un certificado SSL para un dominio personalizado. Aquí aparecen los certificados que carga. Los certificados con un carácter comodín ("estrella") (certificados con un asterisco) son compatibles. Después de que carga un certificado, puede asignarlo a cualquier sitio web en su suscripción y región. Un certificado con asterisco solo se debe cargar una vez, pero se puede utilizar para cualquier sitio dentro del dominio para el cual es válido. Es posible eliminar un certificado solo si ningún enlace en algún sitio está activo para el certificado en cuestión.
        **Nota:** los dominios personalizados están disponibles únicamente en los modos compartido y estándar y la compatibilidad de SSL para dominios personalizados está disponible solo en modo estándar. Para obtener información acerca de la configuración de SSL para un dominio personalizado en Azure, consulte [Habilitación de HTTPS para un sitio web de Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/).
    -   **domain names**: Vea o agregue aquí nombres de dominio adicionales para el sitio web. Puede agregar dominios personalizados mediante un clic en **Manage Domains**. Los dominios personalizados están disponibles únicamente en los modos **compartido** y **estándar**. Puede cambiar el modo del sitio web en la página de administración **Scale**. Los dominios personalizados no se encuentran disponibles en modo libre. Para obtener más información sobre cómo configurar dominios personalizados, consulte [Configuración de un nombre de dominio personalizado para un sitio web de Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-custom-domain-name/).
    -   **SSL Bindings**: Los enlaces SSL a los dominios personalizados solo se encuentran disponibles en modo estándar. Seleccione un modo SSL (**SNI**, **IP** o **No SSL**) para un nombre de dominio concreto. Si elige SNI o IP, puede especificar un certificado para el dominio de entre los certificados que haya cargado. Para obtener información acerca de la configuración de SSL para un dominio personalizado en Azure, consulte [Habilitación de HTTPS para un sitio web de Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/). Para obtener más información acerca de SNI, consulte [Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication).
    -   **deployments**: Utilice estos ajustes para configurar las implementaciones.
        -   **Git URL**: Si ha creado un repositorio Git en su sitio web de Azure, esta es su dirección URL, la ubicación en la que insertar su contenido.
        -   **Deployment Trigger URL**: Esta dirección URL se puede definir en un repositorio GitHub, CodePlex, Bitbucket u otro para desencadenar la implementación cuando se inserta una confirmación en el repositorio.
        -   **Branch to Deploy**: Esta opción le permite especificar la rama que se implementará cuando inserte contenido en ella.
    -   **application diagnostics**: Defina opciones para recopilar seguimientos de diagnóstico desde una aplicación web cuyo código se haya instrumentado con seguimientos. Las opciones de registro para diagnósticos de aplicaciones incluyen:
        -   **Application Logging (File System)**: Elija **On** para que los registros de aplicaciones se escriban en el sistema de archivos del sitio web. Cuando esta opción está habilitada, el registro del sistema de archivos dura 12 horas. Puede tener acceso a los registros desde el recurso compartido de FTP para el sitio web. El vínculo al recurso compartido de FTP se puede encontrar en el **Panel**. En **Quick Glance**, elija **FTP Diagnostic Logs** o **FTPS Diagnostic Logs**.
        -   **Application Logging (Storage)**: Elija **On** para que los registros de aplicaciones se escriban en una cuenta de almacenamiento de Azure. El registro en una cuenta de almacenamiento no tiene límite de tiempo y permanece habilitado hasta que lo deshabilite. De manera predeterminada, los registros se almacenan en una tabla llamada WAWSAppLogTable.
        -   **Logging Level**: Cuando el registro está habilitado, esta opción especifica la cantidad de información que se registrará (Error, Advertencia, Información o Detalle).
        -   **Diagnostic Storage**: Haga clic en **Manage Connection** para abrir el cuadro de diálogo **Manage diagnostic storage** con las siguientes opciones para guardar registros en su cuenta de almacenamiento de Azure:
            -   **Storage Account Name**: Elija la cuenta de almacenamiento en la que desea que se guarden los registros.
            -   **Storage Access Key**: Muestra la clave para la cuenta de almacenamiento seleccionada. Si ha regenerado la clave para la cuenta de almacenamiento, escriba aquí manualmente la clave o utilice uno de los botones **Synchronize**. Los botones de sincronización se encuentran disponibles solo si el usuario que actualmente ha iniciado sesión tiene acceso a la cuenta de almacenamiento seleccionada.
            -   **Synchronize Primary Key**: Recupera la clave primaria más reciente de su cuenta de almacenamiento de Azure.
            -   **Synchronize Secondary Key**: Recupera la clave secundaria más reciente de su cuenta de almacenamiento de Azure.
                **Nota:** para obtener más información acerca de las claves de acceso a almacenamiento de Azure, consulte[Visualización, copia y regeneración de claves de acceso de almacenamiento](http://www.windowsazure.com/en-us/documentation/articles/storage-manage-storage-account/#regeneratestoragekeys).
    -   **site diagnostics**: Defina opciones para recopilar información de diagnóstico para su sitio web, incluido:
        -   **Web Server Logging**: Especifique si habilitar el registro de servidor web para el sitio web. Los registros de servidor web se guardan con formato de archivo de registro W3C extendido. Puede guardar los registros en el almacenamiento de Azure o en el sistema de archivos.
            **Sugerencia**: el tamańo máximo de almacenamiento de registros en el sistema de archivos es de 100 megabytes. Si necesita conservar un historial más grande, utilice el almacenamiento de Azure, que tiene una capacidad de almacenamiento mucho mayor.
            -   Para guardar registros de servidor web en una cuenta de almacenamiento de Azure, elija **Almacenamiento** y, a continuación, **manage storage**. En el cuadro de diálogo **Manage Storage for Site Diagnostics**, utilice la opción **Cuenta de almacenamiento** para elegir la cuenta de almacenamiento de Azure para el contenedor que contendrá los registros. Utilice la opción **Azure Blob Container** para elegir el contenedor que contendrá los registros, o bien, seleccione **Create a new blob container** para habilitar el cuadro **Blob Name** en el que podrá especificar un nombre para el contenedor nuevo.
                **Nota**: si todavía no tiene una cuenta de almacenamiento, vaya a la sección **Almacenamiento** del portal de Azure donde puede hacer clic en **New** para crear una cuenta.
            -   Si elige la opción **File System**, los registros se guardan en el sitio FTP que aparece en **FTP Diagnostic Logs** en la página de administración Panel. Si habilita el almacenamiento de sistema de archivos también habilita el cuadro **Quota**, donde puede definir la cantidad máxima de espacio en disco para los archivos de registro. El mínimo es de 25 MB y el máximo es de 100 MB. El valor predeterminado es de 35 MB. Cuando se alcanza la cuota, los archivos nuevos sobrescriben de manera sucesiva los archivos anteriores.
            -   De manera predeterminada, nunca se eliminan los registros de servidor web en una cuenta de almacenamiento de Azure. Para especificar un período después del cual los registros se eliminarán automáticamente, seleccione **Set Retention** y escriba el número de días durante los cuales conservar los registros en el cuadro **Retention Period**. También puede utilizar la opción **Set Retention** para el almacenamiento de sistema de archivos.
        -   **Detailed Error Messages**: Especifique si registrar mensajes de error detallados para el sitio web. Si la opción está habilitada, los mensajes de error detallados se guardan como archivos .htm en el sitio FTP que aparece bajo FTP Diagnostic Logs en la página de administración Panel. Después de conectarse al sitio FTP especificado, navegue a /LogFiles/DetailedErrors/ para recuperar los archivos .htm que contienen los mensajes de error detallados.
        -   **Failed Request Tracing**: Especifique si habilitar el seguimiento de solicitudes con error. Si la opción está habilitada, el resultado del seguimiento de solicitudes con error se escribe en archivos XML y se guarda en el sitio FTP que aparece en FTP Diagnostic Logs en la página de administración Panel. Después de conectarse al sitio FTP especificado, navegue a /LogFiles/W3SVC########\# (donde ########\# representa un identificador único para el sitio web) para recuperar los archivos XML que contienen el resultado del seguimiento de solicitudes con error.
            **Importante**
            La carpeta /LogFiles/W3SVC########\#/ contiene un archivo XSL y uno o más archivos XML. Asegúrese de descargar el archivo XSL en el mismo directorio de los archivos XML, porque el archivo XSL proporciona funcionalidad para dar formato y filtrar los contenidos de los archivos XML cuando se visualizan en Internet Explorer.
        -   **Remote Debugging**: Defina esta opción en **On** para permitir la depuración remota en su elección de Visual Studio 2012 o Visual Studio 2013. Cuando está habilitada, puede utilizar el depurador remoto en Visual Studio para conectarse directamente a su sitio web de Azure.
             **Nota**: la depuración remota se habilitará solo durante 48 horas y no funcionará con un nombre de sitio o un nombre de usuario que tenga más de 20 caracteres.
    -   **monitoring**: Para los sitios web en modo estándar, pruebe la disponibilidad de los extremos HTTP o HTTPS. Puede probar un extremo desde hasta tres ubicaciones distribuidas geográficamente. Una prueba de supervisión da error si el código de respuesta HTTP es mayor o igual que 400 o si la respuesta demora más de 30 segundos. Un extremo se considera disponible si sus pruebas de supervisión se realizan correctamente desde todas las ubicaciones especificadas.
    -   **developer analytics**: Elija **Add-on** para seleccionar un complemento de análisis de una lista o para ir a la tienda de Azure y elegir uno Elija **Custom** para seleccionar un proveedor de análisis, como New Relic, de una lista. Si utiliza un proveedor personalizado, debe escribir la clave de licencia en el cuadro **Provider Key**.
         **Nota**: para obtener más información sobre el uso de New Relic con sitios web de Azure, consulte [Administración del rendimiento de la aplicación New Relic en Sitios web de Azure](http://www.windowsazure.com/en-us/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/).
    -   **app settings**: Especifique pares de nombre/valor que su aplicación web cargará al inicio. En los sitios .NET, estas configuraciones se insertarán en AppSettings de la configuración .NET en tiempo de ejecución y reemplazarán la configuración existente. En los sitios PHP y Node, estas configuraciones estarán disponibles como variables de entorno en tiempo de ejecución.
    -   **connection strings**: Vea las cadenas de conexión para los recursos vinculados. En los sitios .NET, estas cadenas de conexión se insertarán en la configuración de connectionStrings de la configuración .NET en tiempo de ejecución y reemplazarán las entradas existentes en las que la clave equivalga al nombre de la base de datos vinculada. En los sitios PHP y Node, estas configuraciones estarán disponibles como variables de entorno en tiempo de ejecución, con el tipo de conexión como prefijo. Los prefijos de variable de entorno son los siguientes:
        -   SQL Server: SQLCONNSTR\_
        -   MySQL: MYSQLCONNSTR\_
        -   Base de datos SQL: SQLAZURECONNSTR\_
        -   Personalizada: CUSTOMCONNSTR\_

        Por ejemplo, si una cadena de conexión de MySQL recibió el nombre de connectionstring1, se obtendrá acceso a ella a través de la variable de entorno`MYSQLCONNSTR_connectionString1`.
        **Nota**: las cadenas de conexión se crean cuando vincula un recurso de base de datos a un sitio web y son de solo lectura cuando se visualizan en la página de administración de configuración.
    -   **default documents**: Agregue el documento predeterminado de su sitio web a esta lista si todavía no aparece. El documento predeterminado de un sitio web es la página web que aparece cuando un usuario navega a un sitio web y no especifica una página en especial en ese sitio web. Por lo tanto, si pensamos en el sitio web http://contoso.com y el documento predeterminado está definido en default.htm, se dirigirá a un usuario a http://www.contoso.com/default.htm cuando le seńale a su explorador que navegue a http://www.contoso.com. Si su sitio web contiene más de uno de los archivos que aparecen en la lista, cambie el orden de los archivos en la lista para asegurarse de que el documento predeterminado de su sitio web esté al comienzo de la lista.
    -   **handler mappings**: Agregue procesadores de script personalizados para controlar solicitudes de extensiones de archivo específicas. Especifique la extensión de archivo que se va a gestionar en el cuadro **Extension** (por ejemplo, \*.php o handler.fcgi). El procesador de script especificado en el cuadro **Script Processor Path** procesará las solicitudes a archivos que coincidan con este modelo. Se requiere una ruta de acceso absoluta para el procesador de script (la ruta de acceso D:\\home\\site\\wwwroot se puede utilizar para hacer referencia al directorio raíz de su sitio). Es posible especificar argumentos opcionales de la línea de comandos para el procesador de script en el cuadro **Additional Arguments (Optional)**.
    -   **virtual applications and directories**: Para configurar aplicaciones y directorios virtuales asociados con su sitio web, especifique cada directorio virtual y su ruta de acceso física correspondiente relacionada con la raíz del sitio. De manera opcional, puede activar la casilla **Aplicación** para marcar un directorio virtual como una aplicación en la configuración del sitio.

4 .  Haga clic en **Save** en la parte inferior de la página de administración **Configure** para guardar los cambios en la configuración.

## Configuración de un sitio web para utilizar una base de datos SQL

Siga estos pasos para vincular un sitio web a una base de datos SQL:

1.  En el [Portal de administración](http://manage.windowsazure.com), seleccione **Sitios web** para ver la lista de sitios web creados por la cuenta que actualmente inició sesión.

2.  Seleccione un sitio web de la lista de sitios web para abrir la página de **administración**.

3.  Haga clic en la pestańa **Linked Resources** y aparecerá un mensaje en la página **Linked Resources** que indica que **no tiene recursos vinculados**.

4.  Haga clic en **Link a Resource** para abrir el asistente para **vincular un recurso**.

5.  Haga clic en **Create a new resource** para mostrar una lista de los tipos de recursos que se pueden vincular a su sitio web.

6.  Haga clic en **Base de datos SQL** para mostrar el asistente para **vincular una base de datos**.

7.  Complete los campos requeridos en las páginas 3 y 4 del asistente para **vincular una base de datos** y, a continuación, haga clic en la marca de verificación **Finish** de la página 4.

Azure creará una base de datos SQL con los parámetros especificados y vinculará la base de datos al sitio web.

## Configuración de un sitio web para utilizar una base de datos MySQL
Para configurar un sitio web para que se utilice una base de datos MySQL, siga los mismos pasos que para utilizar una base de datos SQL, pero en el asistente para **vincular un recurso**, elija **MySQL Database** en lugar de **Base de datos SQL**.

De manera alternativa, puede crear el sitio web con la opción **Custom Create**. En la lista desplegable **Database**, elija **Create a new MySQL database** o **Use an existing MySQL database**.

Configuración de un nombre de dominio personalizado
---------------------------------------------------

Para obtener información sobre la configuración del sitio web para que utilice un nombre de dominio personalizado, consulte [Configuración de un nombre de dominio personalizado para un sitio web de Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-custom-domain-name/).

Configuración de un sitio web para utilizar SSL
-----------------------------------------------

Para obtener información acerca de la configuración de SSL para un dominio personalizado en Azure, consulte [Habilitación de HTTPS para un sitio web de Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/).

Pasos siguientes
----------------

-   [Escalación de sitios web](http://www.windowsazure.com/en-us/documentation/articles/web-sites-scale/)

-   [Supervisión de sitios web](http://www.windowsazure.com/en-us/documentation/articles/web-sites-monitor/)


