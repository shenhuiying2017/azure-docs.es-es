<properties linkid="manage-services-how-to-configure-websites" urlDisplayName="How to configure" pageTitle="How to configure websites - Azure service management" metaKeywords="Azure websites, configuring Azure websites, Azure SQL database, Azure MySQL" description="Learn how to configure websites in Azure, including how to configure a website to use a SQL Database or MySQL database." metaCanonical="" services="web-sites" documentationCenter="" title="How to Configure Websites" authors="mwasson" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/18/2014" ms.author="mwasson" />

# Configuración de sitios web

En el Portal de administración de Azure puede cambiar las opciones de configuración para los sitios web y puede vincular el sitio web a otros recursos de Azure, como una base de datos.

## Tabla de contenido

-   [Direccionamiento del opciones de configuración para un sitio web][Direccionamiento del opciones de configuración para un sitio web]
-   [Direccionamiento del sitio web para utilizar una base de datos SQL][Direccionamiento del sitio web para utilizar una base de datos SQL]
-   [Direccionamiento del sitio web para utilizar una base de datos MySQL][Direccionamiento del sitio web para utilizar una base de datos MySQL]
-   [Direccionamiento del nombre de dominio personalizado][Direccionamiento del nombre de dominio personalizado]
-   [Direccionamiento del sitio web para utilizar SSL][Direccionamiento del sitio web para utilizar SSL]
-   [Pasos siguientes][Pasos siguientes]

## <a name="howtochangeconfig"></a>Direccionamiento del opciones de configuración para un sitio web

<!-- HOW TO: CHANGE CONFIGURATION OPTIONS FOR A WEBSITE -->

Para definir las opciones de configuración de un sitio web:

1.  En el [Portal de administración][Portal de administración], abra las páginas de administración del sitio web.
2.  Haga clic en la pestaña **Configurar**.

La pestaña **Configurar** tiene las siguientes secciones:

### General

**Versiones del marco**. Configure estas opciones si su aplicación utiliza cualquiera de estos marcos:

-   **.NET Framework**: Configure la versión de .NET Framework.
-   **PHP**: Configure la versión de PHP o seleccione **DESACTIVADO** para deshabilitar PHP.
-   **Java**: Seleccione la versión mostrada para habilitar Java o seleccione **DESACTIVADO** para deshabilitar Java.
-   Si habilita Java, use la opción **Contenedor Web** para elegir entre las versiones Tomcat y Jetty.
-   **Python**: Seleccione la versión de Python o seleccione **DESACTIVADO** para deshabilitar Python.

Por razones técnicas, si se habilita Java para el sitio web, se deshabilitan las opciones .NET, PHP y Python.

**Modo de canalización administrado**. Configura el [modo de canalización][modo de canalización] IIS. Deje este conjunto en Integrated (el valor predeterminado) a no ser que tenga un sitio web heredado que requiera una versión anterior de IIS.

**Plataforma**. Selecciona si su aplicación se ejecuta en un entorno de 32 o 64 bits. El entorno de 64 bits requiere el modo básico o estándar. Los modos libre y compartido siempre se ejecutan en un entorno de 32 bits.

**Web Sockets**. Seleccione **ACTIVADO** para habilitar el protocolo WebSocket; por ejemplo, si el sitio web utiliza [ASP.NET SignalR][ASP.NET SignalR] o [socket.io][socket.io].

**Siempre disponible**. De forma predeterminada, los sitios web se descargan si están inactivos durante algún tiempo. Esto permite que el sistema conserve recursos. En el modo básico o estándar puede habilitar **Siempre disponible** para mantener el sitio cargado continuamente. Cuando tenga trabajos web continuos en ejecución en el sitio debe habilitar la opción **Siempre disponible** de lo contrario es posible que los trabajos no se realicen de manera fiable

**Editar en Visual Studio Online**. Permite la edición de código activo con Visual Studio Online. Si está habilitado la pestaña panel mostrará un vínculo llamado **Editar en Visual Studio Online** en la sección **Vista rápida**. Haga clic en el vínculo para editar su sitio web directamente en línea. Si necesita autenticarse, puede utilizar sus credenciales de implementación básicas.

Nota: Si habilita el control de código fuente, es posible que una implementación sobrescriba los cambios que realiza en el editor de Visual Studio Online.

### Certificados

En modo básico o estándar puede cargar certificados SSL para un dominio personalizado. Para obtener más información, consulte [Enable HTTPS for an Azure website][Enable HTTPS for an Azure website].

Aquí se enumeran los certificados que haya cargado. Después de cargar un certificado, puede asignarlo a cualquier sitio web de su suscripción y región. Los certificados comodín se pueden utilizar para cualquier sitio dentro del dominio para el cual es válido. Un certificado solo se puede eliminar si no tiene ningún vínculo activo.

### Nombres de dominio

Vea o agregue nombres de dominio adicionales para el sitio web. Para obtener más información, consulte [Configuración de un nombre de dominio personalizado para un Sitio web Azure][Configuración de un nombre de dominio personalizado para un Sitio web Azure].

### Enlaces SSL

Si carga certificados SSL puede vincularlos a nombres de dominios personalizados. Para obtener más información, consulte [Enable HTTPS for an Azure website][Enable HTTPS for an Azure website].

### Implementaciones

Esta sección aparece solo si ha habilitado la implementación desde el control de código fuente. Utilice estas opciones para configurar las implementaciones.

-   **Dirección URL de Git**. Si ha creado un repositorio de Git para el sitio web de Azure, esta es su dirección URL en la que insertará el contenido.
-   **URL de desencadenador de implementación**. Esta dirección URL se puede definir en un repositorio GitHub, CodePlex, Bitbucket u otro para desencadenar la implementación cuando se inserta una confirmación en el repositorio.
-   **Rama para implementar**. Especifica la rama que se implementará cuando inserte contenido.

Para configurar la implementación desde el control de código fuente, en la pestaña **Panel** haga clic en **Configurar implementación desde control de código fuente**.

### Diagnósticos de aplicaciones

Opciones escribir registros de diagnóstico de una aplicación web que admita el registro:

-   **Sistema de archivos**. Escribe registros en el sistema de archivos del sitio web. El registro del sistema de archivos dura 12 horas. Puede obtener acceso a los registros desde el recurso compartido de FTP del sitio web. (Consulte [Credenciales FTP][Credenciales FTP]).
-   **Almacenamiento de tablas**. Escribe registros en el almacenamiento de Tabla de Azure. No tiene ningún límite de tiempo y permanece habilitado hasta que el usuario lo deshabilita.
-   **Almacenamiento de blobs**. Escribe registros en el almacenamiento de blobs de Azure. No tiene ningún límite de tiempo y permanece habilitado hasta que el usuario lo deshabilita.

**Nivel de registro**. Cuando el registro está habilitado, esta opción especifica la cantidad de información que se registrará (Error, Advertencia, Información o Detalle).

**Administrar almacenamiento de tablas**. Cuando el almacenamiento en tabla está habilitado, haga clic en este botón para configurar la cuenta de almacenamiento y el nombre de la tabla.

**Administración del almacenamiento blob.** Cuando el almacenamiento de blob está habilitado, haga clic en este botón para configurar la cuenta de almacenamiento y el nombre del blob.

### Diagnósticos del sitio

Opciones para recopilar la información de diagnóstico para el sitio web.

**Registro del servidor web**. Habilita el registro de servidor web. Los registros se guardan con formato de archivo de registro W3C extendido. Puede guardar los registros en el almacenamiento de Azure o en el sistema de archivos del sitio web.

-   Si elige la opción **Sistema de archivos**, los registros se guardan en el sitio FTP que aparece en FTP Diagnostic Logs en la página del Panel. (Consulte [Credenciales FTP][Credenciales FTP]).
-   Si elige la opción **Sistema de archivos**, utilice la casilla **Cuota**, para definir la cantidad máxima de espacio en disco para los archivos de registro. El mínimo es de 25 MB y el máximo es de 100 MB. El valor predeterminado es de 35 MB. Cuando se alcanza la cuota, los archivos nuevos sobrescriben de manera sucesiva los archivos anteriores. Si necesita conservar un historial mayor de 100 MB, utilice el almacenamiento de Azure, que tiene una capacidad de almacenamiento mucho mayor.
-   Opcionalmente haga clic en **Establecer retención** para eliminar archivos automáticamente tras un periodo de tiempo. De manera predeterminada, los registros nunca se eliminan.

**Mensajes de error detallados**. Si la opción está habilitada, los mensajes de error detallados se guardan como archivos .htm. Para ver los archivos vaya al sitio FTP que aparece en "Registros de diagnóstico de FTP" en la página del Panel. Los archivos se guardan en /LogFiles/DetailedErrors en el sitio FTP. (Consulte [Credenciales FTP][Credenciales FTP]).

**Error del seguimiento de solicitudes**. Si se habilita esta opción, las solicitudes con errores se registran en los archivos XML. Para ver los archivos vaya al sitio FTP que aparece en "Registros de diagnóstico de FTP" en la página del Panel. (Consulte [Credenciales FTP][Credenciales FTP]). Los archivos se guardan en /LogFiles/W3SVC*xxx*, donde xxx es un identificador único. Esta carpeta contiene un archivo XSL y uno o varios archivos XML. Asegúrese de descargar el archivo XSL porque proporciona funcionalidad para dar formato y filtrar los contenidos de los archivos XML.

**Depuración remota** Habilita la depuración remota. Cuando esté habilitada, puede usar la depuración remota en Visual Studio para conectarse directamente a su sitio web de Azure. La depuración remota permanecerá habilitada durante 48 horas.

**Nota**: La depuración remota no funcionará con un nombre de sitio o un nombre de usuario que tenga más de 20 caracteres.

### Supervisión

En modo estándar, pruebe la disponibilidad de los extremos HTTP o HTTPS desde ubicaciones geodistribuidas. Una prueba de supervisión da error si el código de respuesta HTTP es un error (4xx o 5xx) o si la respuesta se retrasa más de 30 segundos. Un extremo se considera disponible si sus pruebas de supervisión se realizan correctamente desde todas las ubicaciones especificadas.

Para obtener más información, consulte [Inserción de Supervisar el estado de los extremos web][Inserción de Supervisar el estado de los extremos web].

### Análisis del desarrollador

Seleccione **Complemento** para elegir un complemento de análisis de una lista o para ir a la tienda de Azure para elegir uno. Elija **Personalizado** para seleccionar un proveedor de análisis, como New Relic, de una lista. Si utiliza un proveedor personalizado, debe escribir la clave de licencia en el cuadro **Clave de proveedor**.

Para obtener más información sobre el uso de New Relic con sitios web de Azure, consulte [Administración del rendimiento de la aplicación New Relic en Sitios web de Azure][Administración del rendimiento de la aplicación New Relic en Sitios web de Azure].

### Configuración de aplicaciones

Pares nombre/valor que se cargarán en su aplicación web al iniciarse.

-   En los sitios .NET, estas configuraciones se insertarán en AppSettings de la configuración .NET en tiempo de ejecución y reemplazará la configuración existente.

-   Las aplicaciones PHP, Python, Java y Node pueden acceder a estas configuraciones como variables de entorno en tiempo de ejecución. En cada configuración de aplicación se crean dos variables de entorno; una con el nombre especificado en el entrada de configuración de la aplicación y otra con el prefijo APPSETTING\_. Ambas contienen el mismo valor.

### Cadenas de conexión

Cadenas de conexión para los recursos vinculados.

En los sitios .NET, estas cadenas de conexión se insertarán en la configuración de connectionStrings de la configuración .NET en tiempo de ejecución y reemplazarán las entradas existentes en las que la clave equivalga al nombre de la base de datos vinculada.

En las aplicaciones PHP, Python, Java y Node, estas configuraciones estarán disponibles como variables de entorno en tiempo de ejecución, con el tipo de conexión como prefijo. Los prefijos de variable de entorno son los siguientes:

-   SQL Server: SQLCONNSTR\_
-   MySQL: MYSQLCONNSTR\_
-   Base de datos SQL: SQLAZURECONNSTR\_
-   Personalizada: CUSTOMCONNSTR\_

Por ejemplo, si una cadena de conexión de MySQL recibió el nombre de connectionstring1, se obtendrá acceso a ella a través de la variable de entorno`MYSQLCONNSTR_connectionString1`.

**Nota**: Las cadenas de conexión se crean cuando vincula un recurso de base de datos a un sitio web. Las cadenas de conexión creadas de esta manera son de solo lectura cuando se visualizan en la
página de administración de configuración.

### Documentos predeterminados

El documento predeterminado de un sitio web es la página web que aparece no especifica una página en especial en ese sitio web. Si la lista de un sitio web contiene varios archivos, asegúrese de poner el documento predeterminado al principio de la lista.

Es posible que las aplicaciones utilicen módulos que enruten en base a la URL, en lugar de ofrecer funcionalidad a contenido estático, en cuyo caso no existe realmente un documento predeterminado.

### Asignaciones de controlador

Utilice esta zona para agregar procesadores de script personalizados para controlar solicitudes de extensiones de archivo específicas.

-   **Extensión**. La extensión de archivo que se va a gestionar, por ejemplo, \*.php o handler.fcgi.
-   **Ruta de acceso del procesador de script**. La ruta absoluta del procesador de script. El procesador de script procesará las solicitudes a archivos que coincidan con esta extensión de archivo. Utilice la ruta de acceso `D:\home\site\wwwroot`r para hacer referencia al directorio raíz de su sitio.
-   **Argumentos adicionales**. Argumentos opcionales de la línea de comandos para el procesador de script

### Directorios y aplicaciones virtuales

Para configurar aplicaciones y directorios virtuales asociados con su sitio web, especifique cada directorio virtual y su ruta de acceso física correspondiente relacionada con la raíz del sitio. De manera opcional, puede activar la casilla **Aplicación** para marcar un directorio virtual como una aplicación en la configuración del sitio.

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A SQL DATABASE -->

## <a name="howtoconfigSQL"></a>Direccionamiento del sitio web para utilizar una base de datos SQL

Siga estos pasos para vincular un sitio web a una base de datos SQL:

1.  En el [Portal de administración][1], seleccione **Sitios web** para ver la lista de sitios web creados por la cuenta que actualmente inició sesión.

2.  Seleccione un sitio web de la lista de sitios web para abrir las páginas de **administración**.

3.  Haga clic en la pestaña **Recursos vinculados** y aparecerá un mensaje en la página **Recursos vinculados** que indica que **no tiene recursos vinculados**.

4.  Haga clic en **Vincular un recurso** para abrir el asistente para **vincular un recurso**.

5.  Haga clic en **Crear un nuevo recurso** para mostrar una lista de los tipos de recursos que se pueden vincular a su sitio web.

6.  Haga clic en **Base de datos SQL** para mostrar el asistente para **vincular una base de datos**.

7.  Complete los campos requeridos en las páginas 3 y 4 del asistente para **vincular una base de datos** y, a continuación, haga clic en la marca de verificación **Finalizar** de la página 4.

Azure creará una base de datos SQL con los parámetros especificados y vinculará la base de datos al sitio web.

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A MYSQL DATABASE -->

## <a name="howtoconfigMySQL"></a>Direccionamiento del sitio web para utilizar una base de datos MySQL

Para configurar un sitio web para que se utilice una base de datos MySQL, siga los mismos pasos que para utilizar una base de datos SQL, pero en el asistente para **vincular un recurso**, elija **Base de datos MySQL** en lugar de **Base de datos SQL**.

De manera alternativa, puede crear el sitio web con la opción **Creación personalizada**. En la lista desplegable **Base de datos**, elija **Crear una nueva base de datos MySQL** o **Usar una base de datos MySQL existente**.

## <a name="howtodomain"></a>Direccionamiento del nombre de dominio personalizado

Para obtener información sobre la configuración del sitio web para que utilice un nombre de dominio personalizado, consulte [Configuración de un nombre de dominio personalizado para un sitio web de Azure][Configuración de un nombre de dominio personalizado para un Sitio web Azure].

## <a name="howtoconfigSSL"></a>Direccionamiento del sitio web para utilizar SSL

Para obtener información acerca de la configuración de SSL para un dominio personalizado en Azure, consulte [Habilitación de HTTPS para un sitio web de Azure][Habilitación de HTTPS para un sitio web de Azure].

## <a name="next"></a>Pasos siguientes

-   [Escalación de sitios web][Escalación de sitios web]

-   [Supervisión de sitios web][Supervisión de sitios web]

  [Direccionamiento del opciones de configuración para un sitio web]: #howtochangeconfig
  [Direccionamiento del sitio web para utilizar una base de datos SQL]: #howtoconfigSQL
  [Direccionamiento del sitio web para utilizar una base de datos MySQL]: #howtoconfigMySQL
  [Direccionamiento del nombre de dominio personalizado]: #howtodomain
  [Direccionamiento del sitio web para utilizar SSL]: #howtoconfigSSL
  [Pasos siguientes]: #next
  [Portal de administración]: https://manage.windowsazure.com/
  [modo de canalización]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [socket.io]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-nodejs-chat-app-socketio/
  [Enable HTTPS for an Azure website]: href="http://www.windowsazure.com/es-es/documentation/articles/web-sites-configure-ssl-certificate/
  [Configuración de un nombre de dominio personalizado para un Sitio web Azure]: http://www.windowsazure.com/es-es/documentation/articles/web-sites-custom-domain-name/
  [Credenciales FTP]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-manage#ftp-credentials
  [Inserción de Supervisar el estado de los extremos web]: http://go.microsoft.com/fwLink/?LinkID=279906&clcid=0x409
  [Administración del rendimiento de la aplicación New Relic en Sitios web de Azure]: http://www.windowsazure.com/es-es/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/
  [1]: http://manage.windowsazure.com
  [Habilitación de HTTPS para un sitio web de Azure]: http://www.windowsazure.com/es-es/documentation/articles/web-sites-configure-ssl-certificate/
  [Escalación de sitios web]: http://www.windowsazure.com/es-es/documentation/articles/web-sites-scale/
  [Supervisión de sitios web]: http://www.windowsazure.com/es-es/documentation/articles/web-sites-monitor/
