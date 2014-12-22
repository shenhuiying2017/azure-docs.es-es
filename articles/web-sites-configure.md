<properties urlDisplayName="How to configure" pageTitle="Configuración de sitios web - administración de servicios de Azure" metaKeywords="sitios web de Azure, configuración de sitios web de Azure, base de datos SQL de Azure, Azure MySQL" description="Learn how to configure websites in Azure, including how to configure a website to use a SQL Database or MySQL database." metaCanonical="" services="web-sites" documentationCenter="" title="How to Configure Websites" authors="mwasson" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/18/2014" ms.author="mwasson" />


# Configuración de Sitios web #
En el Portal de administración de Azure puede cambiar las opciones de configuración para los sitios web y puede vincular el sitio web a otros recursos de Azure, como una base de datos.

## Tabla de contenido ##
- [Direccionamiento del opciones de configuración para un sitio web](#howtochangeconfig)
- [Direccionamiento del sitio web para utilizar una base de datos SQL](#howtoconfigSQL)
- [Direccionamiento del sitio web para utilizar una base de datos MySQL](#howtoconfigMySQL)
- [Direccionamiento del nombre de dominio personalizado](#howtodomain)
- [Direccionamiento del sitio web para utilizar SSL](#howtoconfigSSL)
- [Pasos siguientes](#next)


##<a name="howtochangeconfig"></a>Creación de las opciones de configuración para un sitio web

<!-- HOW TO: CHANGE CONFIGURATION OPTIONS FOR A WEBSITE -->

Para definir las opciones de configuración de un sitio web:

1. En el [Portal de administración](https://manage.windowsazure.com/), abra las páginas de administración del sitio web.
1. Haga clic en la pestaña <strong>Configurar</strong>.

La pestaña **Configurar** tiene las siguientes secciones:

### General

**Versiones del marco**. Configure estas opciones si su aplicación utiliza cualquiera de estos marcos: 

- **.NET Framework**: Configure la versión de .NET Framework. 
- **PHP**: Configure la versión de PHP o seleccione **DESACTIVADO** para deshabilitar PHP. 
- **Java**: Seleccione la versión mostrada para habilitar Java o seleccione <strong>DESACTIVADO</strong> para deshabilitar Java. 
- Si habilita Java, use la opción <strong>Contenedor Web</strong> para elegir entre las versiones Tomcat y Jetty.
- **Python**: Seleccione la versión de Python o seleccione **DESACTIVADO** para deshabilitar Python.

Por razones técnicas, si se habilita Java para el sitio web, se deshabilitan las opciones .NET, PHP y Python.

<strong>Modo de canalización administrado</strong>. Configura el [modo de canalización](http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application) IIS. Deje este conjunto en Integrated (el valor predeterminado) a no ser que tenga un sitio web heredado que requiera una versión anterior de IIS.

<strong>Plataforma</strong>. Selecciona si su aplicación se ejecuta en un entorno de 32 o 64 bits. El entorno de 64 bits requiere el modo básico o estándar. Los modos libre y compartido siempre se ejecutan en un entorno de 32 bits.

<strong>Web Sockets</strong>. Seleccione **ACTIVADO** para habilitar el protocolo WebSocket; por ejemplo, si el sitio web utiliza [ASP.NET SignalR](http://www.asp.net/signalr) o [socket.io](http://azure.microsoft.com/es-es/documentation/articles/web-sites-nodejs-chat-app-socketio/).

<strong>Siempre disponible</strong>. De forma predeterminada, los sitios web se descargan si están inactivos durante algún tiempo. Esto permite que el sistema conserve recursos. En el modo básico o estándar puede habilitar <strong>Siempre disponible</strong> para mantener el sitio cargado continuamente. Cuando tenga trabajos web continuos en ejecución en el sitio debe habilitar la opción **Siempre disponible** de lo contrario es posible que los trabajos no se realicen de manera fiable.

<strong>Editar en Visual Studio Online</strong>. Permite la edición de código activo con Visual Studio Online. Si está habilitado la pestaña panel mostrará un vínculo llamado <strong>Editar en Visual Studio Online</strong> en la sección <strong>Vista rápida</strong>. Haga clic en el vínculo para editar su sitio web directamente en línea. Si necesita autenticarse, puede utilizar sus credenciales de implementación básicas.

Nota: Si habilita el control de código fuente, es posible que una implementación sobrescriba los cambios que realiza en el editor de Visual Studio Online. 


### Certificados

En modo básico o estándar puede cargar certificados SSL para un dominio personalizado. Para obtener más información, consulte [Habilitación de HTTPS para un sitio web de Azure](href="http://www.windowsazure.com/es-es/documentation/articles/web-sites-configure-ssl-certificate/). 

Aquí se enumeran los certificados que haya cargado. Después de cargar un certificado, puede asignarlo a cualquier sitio web de su suscripción y región. Los certificados comodín se pueden utilizar para cualquier sitio dentro del dominio para el cual es válido. Un certificado solo se puede eliminar si no tiene ningún vínculo activo.

### Nombres de dominio

Vea o agregue nombres de dominio adicionales para el sitio web. Para obtener más información, consulte [Configuración de un nombre de dominio personalizado para un Sitio web Azure](http://www.windowsazure.com/es-es/documentation/articles/web-sites-custom-domain-name/).

### Enlaces SSL

Si carga certificados SSL puede vincularlos a nombres de dominios personalizados. Para obtener más información, consulte [Habilitación de HTTPS para un sitio web de Azure](href="http://www.windowsazure.com/es-es/documentation/articles/web-sites-configure-ssl-certificate/).

### Implementaciones

Esta sección aparece solo si ha habilitado la implementación desde el control de código fuente. Utilice estas opciones para configurar las implementaciones.

- <strong>Dirección URL de Git</strong>. Si ha creado un repositorio de Git para el sitio web de Azure, esta es su dirección URL en la que insertará el contenido.
- <strong>URL de desencadenador de implementación</strong>. Esta dirección URL se puede definir en un repositorio GitHub, CodePlex, Bitbucket u otro para desencadenar la implementación cuando se inserta una confirmación en el repositorio.
- <strong>Rama para implementar</strong>. Especifica la rama que se implementará cuando inserte contenido.

Para configurar la implementación desde el control de código fuente, en la pestaña **Panel**, haga clic en **Configurar implementación desde control de código fuente**. 

### Diagnósticos de aplicaciones

Opciones para escribir registros de diagnóstico de una aplicación web que admita el registro: 

- <strong>Sistema de archivos</strong>. Escribe registros en el sistema de archivos del sitio web. El registro del sistema de archivos dura 12 horas. Puede obtener acceso a los registros desde el recurso compartido de FTP del sitio web. (Consulte [Credenciales FTP](http://azure.microsoft.com/es-es/documentation/articles/web-sites-manage#ftp-credentials)).
- <strong>Almacenamiento de tablas</strong>. Escribe registros en el almacenamiento de Tabla de Azure. No tiene ningún límite de tiempo y permanece habilitado hasta que el usuario lo deshabilita. 
- <strong>Almacenamiento de blobs</strong>. Escribe registros en el almacenamiento de blobs de Azure. No tiene ningún límite de tiempo y permanece habilitado hasta que el usuario lo deshabilita.

<strong>Nivel de registro</strong>. Cuando el registro está habilitado, esta opción especifica la cantidad de información que se registrará (Error, Advertencia, Información o Detalle).

**Administrar almacenamiento de tablas**. Cuando el almacenamiento en tabla está habilitado, haga clic en este botón para configurar la cuenta de almacenamiento y el nombre de la tabla.

**Administración del almacenamiento blob.** Cuando el almacenamiento de blob está habilitado, haga clic en este botón para configurar la cuenta de almacenamiento y el nombre del blob.

### Diagnósticos del sitio

Opciones para recopilar la información de diagnóstico para el sitio web.

<strong>Registro del servidor web</strong>. Habilita el registro de servidor web. Los registros se guardan con formato de archivo de registro W3C extendido. Puede guardar los registros en el almacenamiento de Azure o en el sistema de archivos del sitio web.
 
- Si elige la opción <strong>Sistema de archivos</strong>, los registros se guardan en el sitio FTP que aparece en "Registros de diagnóstico de FTP" en la página del Panel. (Consulte [Credenciales FTP](http://azure.microsoft.com/es-es/documentation/articles/web-sites-manage#ftp-credentials)). 
- Si elige la opción **Sistema de archivos**, use el cuadro <strong>Cuota</strong> para definir la cantidad máxima de espacio en disco para los archivos de registro. El mínimo es de 25 MB y el máximo es de 100 MB. El valor predeterminado es de 35 MB. Cuando se alcanza la cuota, los archivos nuevos sobrescriben de manera sucesiva los archivos anteriores. Si necesita conservar un historial mayor de 100 MB, utilice el almacenamiento de Azure, que tiene una capacidad de almacenamiento mucho mayor.
- Opcionalmente haga clic en <strong>Establecer retención</strong> para eliminar archivos automáticamente tras un periodo de tiempo. De manera predeterminada, los registros nunca se eliminan.   

<strong>Mensajes de error detallados</strong>. Si la opción está habilitada, los mensajes de error detallados se guardan como archivos .htm. Para ver los archivos vaya al sitio FTP que aparece en "Registros de diagnóstico de FTP" en la página del Panel. Los archivos se guardan en /LogFiles/DetailedErrors en el sitio FTP. (Consulte [Credenciales FTP](http://azure.microsoft.com/es-es/documentation/articles/web-sites-manage#ftp-credentials)).

<strong>Error del seguimiento de solicitudes</strong>. Si se habilita esta opción, las solicitudes con errores se registran en los archivos XML. Para ver los archivos vaya al sitio FTP que aparece en "Registros de diagnóstico de FTP" en la página del Panel. (Consulte [Credenciales FTP](http://azure.microsoft.com/es-es/documentation/articles/web-sites-manage#ftp-credentials)). Los archivos se guardan en /LogFiles/W3SVC*xxx*, donde xxx es un identificador único. Esta carpeta contiene un archivo XSL y uno o varios archivos XML. Asegúrese de descargar el archivo XSL porque proporciona funcionalidad para dar formato y filtrar los contenidos de los archivos XML.

<strong>Depuración remota</strong> Habilita la depuración remota. Cuando esté habilitada, puede usar la depuración remota en Visual Studio para conectarse directamente a su sitio web de Azure. La depuración remota permanecerá habilitada durante 48 horas.

**Nota**: La depuración remota no funcionará con un nombre de sitio o un nombre de usuario que tenga más de 20 caracteres. 

### Supervisión

En modo estándar o básico, pruebe la disponibilidad de los extremos HTTP o HTTPS desde ubicaciones geodistribuidas. Una prueba de supervisión da error si el código de respuesta HTTP es un error (4xx o 5xx) o si la respuesta se retrasa más de 30 segundos. Un extremo se considera disponible si sus pruebas de supervisión se realizan correctamente desde todas las ubicaciones especificadas. 

Para obtener más información, consulte [Supervisión del estado de los extremos web](http://go.microsoft.com/fwLink/?LinkID=279906&clcid=0x409).


### Análisis del desarrollador

Elija <strong>Complemento</strong> para elegir un complemento de análisis de una lista o para ir a la tienda de Azure para elegir uno. Elija <strong>Personalizado</strong> para seleccionar un proveedor de análisis, como New Relic, de una lista. Si utiliza un proveedor personalizado, debe escribir la clave de licencia en el cuadro <strong>Clave de proveedor</strong>. 

Para obtener más información sobre el uso de New Relic con sitios web de Azure, consulte <a href="http://www.windowsazure.com/es-es/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/">Administración del rendimiento de la aplicación New Relic en Sitios web de Azure</a>.

### Configuración de aplicaciones

Pares nombre/valor que se cargarán en su aplicación web al iniciarse. 

- En los sitios .NET, estas configuraciones se insertarán en AppSettings de la configuración .NET en tiempo de ejecución y reemplazará la configuración existente. 

- Las aplicaciones PHP, Python, Java y Node pueden acceder a estas configuraciones como variables de entorno en tiempo de ejecución. En cada configuración de aplicación se crean dos variables de entorno; una con el nombre especificado en el entrada de configuración de la aplicación y otra con el prefijo APPSETTING_. Ambas contienen el mismo valor.

### Cadenas de conexión

Cadenas de conexión para los recursos vinculados. 

En los sitios .NET, estas cadenas de conexión se insertarán en la configuración de connectionStrings de la configuración .NET en tiempo de ejecución y reemplazarán las entradas existentes en las que la clave equivalga al nombre de la base de datos vinculada. 

En las aplicaciones PHP, Python, Java y Node, estas configuraciones estarán disponibles como variables de entorno en tiempo de ejecución, con el tipo de conexión como prefijo. Los prefijos de variable de entorno son los siguientes: 

- SQL Server: SQLCONNSTR_
- MySQL: MYSQLCONNSTR_
- Base de datos SQL: SQLAZURECONNSTR_
- Personalizada: CUSTOMCONNSTR_

Por ejemplo, si una cadena de conexión de MySQL recibió el nombre de connectionstring1, se obtendrá acceso a ella a través de la variable de entorno <code>MYSQLCONNSTR_connectionString1</code>.

<strong>Nota</strong>: Las cadenas de conexión se crean cuando vincula un recurso de base de datos a un sitio web. Las cadenas de conexión creadas de esta manera son de solo lectura cuando se visualizan en la 
página de administración de la configuración.

### Documentos predeterminados

El documento predeterminado de un sitio web es la página web que aparece no especifica una página en especial en ese sitio web. Si la lista de un sitio web contiene varios archivos, asegúrese de poner el documento predeterminado al principio de la lista.

Es posible que las aplicaciones utilicen módulos que enruten en base a la URL, en lugar de ofrecer funcionalidad a contenido estático, en cuyo caso no existe realmente un documento predeterminado.   

### Asignaciones de controlador

Utilice esta zona para agregar procesadores de script personalizados para controlar solicitudes de extensiones de archivo específicas. 

- **Extensión**. La extensión de archivo que se va a gestionar, por ejemplo, *.php o handler.fcgi.
- **Ruta de acceso del procesador de script**. La ruta absoluta del procesador de script. El procesador de script procesará las solicitudes a archivos que coincidan con esta extensión de archivo. Utilice la ruta de acceso <code>D:\home\site\wwwroot</code> para hacer referencia al directorio raíz de su sitio.
- **Argumentos adicionales**. Argumentos opcionales de la línea de comandos para el procesador de script


### Directorios y aplicaciones virtuales 

Para configurar aplicaciones y directorios virtuales asociados con su sitio web, especifique cada directorio virtual y su ruta de acceso física correspondiente relacionada con la raíz del sitio. De manera opcional, puede activar la casilla <strong>Aplicación</strong> para marcar un directorio virtual como una aplicación en la configuración del sitio.

	

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A SQL DATABASE -->
##<a name="howtoconfigSQL"></a>Configuración de un sitio web para utilizar una base de datos SQL

Siga estos pasos para vincular un sitio web a una base de datos SQL:

1. En el [Portal de administración](http://manage.windowsazure.com), seleccione **Sitios web** para ver la lista de sitios web creados por la cuenta que actualmente inició sesión.

2. Seleccione un sitio web de la lista de sitios web para abrir las páginas de **Administración**.

3. Haga clic en la pestaña **Recursos vinculados** y aparecerá un mensaje en la página **Recursos vinculados** que indica que **no tiene recursos vinculados**.

4. Haga clic en **Vincular un recurso** para abrir el asistente para **vincular un recurso**.

5. Haga clic en **Crear un nuevo recurso** para mostrar una lista de los tipos de recursos que se pueden vincular a su sitio web.

6. Haga clic en **Base de datos SQL** para mostrar el asistente para **vincular una base de datos**.

7. Complete los campos requeridos en las páginas 3 y 4 del asistente para **vincular una base de datos** y luego haga clic en la marca de verificación **Finalizar** de la página 4.

Azure creará una base de datos SQL con los parámetros especificados y vinculará la base de datos al sitio web.

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A MYSQL DATABASE -->
##<a name="howtoconfigMySQL"></a>Configuración del sitio web para utilizar una base de datos MySQL##
Para configurar un sitio web para que se utilice una base de datos MySQL, siga los mismos pasos que para utilizar una base de datos SQL, pero en el asistente para **vincular un recurso**, elija **Base de datos MySQL** en lugar de **Base de datos SQL**. 

De manera alternativa, puede crear el sitio web con la opción **Creación personalizada**. En la lista desplegable **Base de datos**, elija **Crear una nueva base de datos MySQL** o **Usar una base de datos MySQL existente**. 

##<a name="howtodomain"></a>Direccionamiento del nombre de dominio personalizado

Para obtener información sobre la configuración del sitio web para que utilice un nombre de dominio personalizado, consulte [Configuración de un nombre de dominio personalizado para un sitio web de Azure](http://www.windowsazure.com/es-es/documentation/articles/web-sites-custom-domain-name/).

##<a name="howtoconfigSSL"></a>Configuración del sitio web para utilizar SSL##

Para obtener información acerca de la configuración de SSL para un dominio personalizado en Azure, consulte [Habilitación de HTTPS para un sitio web de Azure](http://www.windowsazure.com/es-es/documentation/articles/web-sites-configure-ssl-certificate/). 

##<a name="next"></a>Pasos siguientes

* [Escalación de sitios web](http://www.windowsazure.com/es-es/documentation/articles/web-sites-scale/)

* [Supervisión de sitios web](http://www.windowsazure.com/es-es/documentation/articles/web-sites-monitor/)

