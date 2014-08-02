<properties linkid="manage-scenarios-how-to-manage-websites" urlDisplayName="How to manage" pageTitle="How to manage web sites - Microsoft Azure service management" metaKeywords="Azure portal website management" description="A reference for the Portal web site management pages in Microsoft Azure. Details are provided for each web site management page." metaCanonical="" services="web-sites" documentationCenter="" title="How to Manage Web Sites" authors="timamm" solutions="" writer="timamm" manager="" editor="" />

Administración de sitios web
============================

Los sitios web se administran en el portal de Azure con un conjunto de páginas o "pestañas". A continuación se describen todas las páginas de administración de sitios web.

QuickStart
----------

La página de administración **QuickStart** incluye las secciones siguientes:

-   **Get the tools**: Proporciona vínculos para instalar [WebMatrix](http://go.microsoft.com/fwlink/?LinkID=226244) y el [SDK de Azure](http://go.microsoft.com/fwlink/?LinkId=246928).
-   **Publish your app**: Proporciona vínculos para descargar el perfil de publicación del sitio web, restablecer las credenciales de implementación para el sitio web, agregar la ranura de la publicación preconfigurada (implementación) en un sitio no preconfigurado y obtener información acerca de la publicación preconfigurada.
-   **Integrate source control**: Configuración y administración de implementaciones con las herramientas de control del código fuente o sitios web como TFS, CodePlex, GitHub, Dropbox, Bitbucket o Local Git.

Panel
-----

La página de administración **Panel** incluye las secciones siguientes:

-   Un gráfico que resume el uso del sitio web como medidas de determinadas métricas.
  - **CPU Time**: Una medida del uso de CPU del sitio web.
  - **Data In**: Una medida de los datos que el sitio web recibe de los clientes.
  - **Data Out**: Una medida de los datos que el sitio web envía a los clientes.
  - **Errores del servidor HTTP**: Número de mensajes de HTTP "5xx Server Error" enviados.
  - **Requests**: Recuento de las solicitudes realizadas en el sitio web de todos los clientes.
 <br />**Nota:** puede agregar métricas de rendimiento adicionales en la página de administración **Monitor** seleccionando **Add Metrics** en la parte inferior de la página. Para obtener más información, consulte [Supervisión de sitios web](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/).

-   **Web Endpoint Status**: Lista que incluye los extremos web configurados para la supervisión. Si no se han configurado extremos, haga clic en **Configure Web Endpoint Monitoring** y vaya a la sección **Monitoring** de la página de administración **Configure**. Para obtener más información, consulte [Supervisión de sitios web](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/).

-   **Autoscale Status**: En modo estándar, puede escalar automáticamente sus recursos para emplear solo los necesarios. Para habilitar la autoescala, seleccione **Configure Autoscale**, que le lleva a la página **Scale**. Si su sitio web está en modo libre o compartido, tendrá que cambiarlo al modo estándar (puede hacerlo en la página **Scale**) antes de configurar la autoescala. **Autoscale Operation Logs** le lleva al portal **Management Services** donde puede ver el historial de autoescala de su sitio web. La consulta predeterminada es de las últimas 24 horas, aunque se puede modificar.

-   **Usage Overview**: Esta sección muestra las estadísticas de uso de la CPU, el sistema de archivos y la memoria del sitio web.
-   **Linked Resources**: Esta sección muestra una lista de recursos como una base de datos SQL o MySQL, o una cuenta de almacenamiento de Azure, que estén conectados a su sitio web. Haga clic en el nombre del recurso para administrarlo. Si tiene una base de datos MySQL, si hace clic en su nombre le llevará a la página de administración de ClearDB. Allí podrá ver sus métricas de rendimiento o ir al panel de ClearDB, donde puede actualizar su base de datos MySQL, si procede. Si no se incluye ningún recurso, haga clic en **Manage Linked Resources** para ir la página **Linked Resources**, donde podrá agregar el vínculo a un recurso para su sitio web.
-   Una sección **Quick Glance** que incluye la siguiente información resumida y los vínculos (dependiendo de su configuración, algunas de las opciones siguientes podrían no aparecer):
 - **View Applicable Add-ons**: Abre el cuadro de diálogo **Purchase from Store**, donde puede elegir los complementos que desee comprar y que ofrecen una funcionalidad adicional para su sitio web. Algunos complementos podrían no estar disponibles en su región o entorno.
 - **View connection strings**: Visualización de las cadenas de conexión de la base de datos de su sitio web.
 - **Download the publish profile**: Haga clic en este vínculo para descargar el perfil de publicación de su sitio web. El perfil de publicación contiene sus credenciales (nombre de usuario y contraseña) y las URL para cargar el contenido en su sitio web con FTP y Git. El archivo de perfil está en formato XML y se puede ver en un editor de texto.
 - **Set up deployment credentials**: Haga clic en esta opción para crear un nombre de usuario y una contraseña para cargar el contenido en su sitio web con FTP o Git. Después de crear sus credenciales de implementación por FTP/Git, puede usarlas para insertar contenido en cualquier sitio web de su suscripción usando FTP o Git. Para ver las credenciales después de haberlas creado, haga clic en **Download the publish profile**. El perfil de publicación que descargue es un archivo de texto en formato XML y se puede ver en un editor de texto. **Nota**: no es posible autenticarse en un host FTP o en un repositorio Git usando las credenciales de la cuenta Microsoft (Live ID).
 - **Reset your publish profile credentials**: Restablece el perfil de publicación de su sitio web. Dejarán de ser válidos los perfiles de publicación descargados anteriormente.
 - **Set up deployment from source control**: Muestra un cuadro de diálogo donde puede configurar la publicación continua desde Team Foundation Service, CodePlex, GitHub, Dropbox, Bitbucket o Local Git.
 - **Add a new deployment slot**: En sitios en modo estándar, utilice esta característica para crear un espacio de ensayo para el sitio. El espacio de ensayo (sitio de ensayo) le permite validar el contenido y la configuración del sitio antes de que pase a producción. También puede usar la versión de ensayo del sitio para agregar gradualmente actualizaciones y, a continuación, pasar el sitio a producción cuando las actualizaciones se hayan completado en el espacio de ensayo. (No puede agregar un espacio a un sitio que ya está en ensayo).
 - **Edit in Visual Studio Online**: Haga clic en este vínculo para editar su sitio web en línea directamente usando Visual Studio Online en el portal de Microsoft Azure. Esta opción no aparecerá a menos que la habilite en la página **Configure**.
 - **Disconnect from Dropbox**: Si ha configurado una conexión con Dropbox para la implementación, este vínculo le permite desconectarse.
 - **Delete Git repository**: Si ha configurado un repositorio Git, este vínculo le permite eliminarlo.
 - **Status**: Indica si el sitio web se está ejecutando.
 - **Management Services**: Haga clic en el vínculo **Operation Logs** para ver los registros de operaciones de su sitio web desde el Portal de servicios de administración de Microsoft Azure.
 - **Virtual IP Address**: Muestra la dirección IP virtual del sitio web si ha configurado un enlace SSL basado en IP para el sitio web en la sección **SSL Bindings** de la pestaña **Configure**.
 - **Site URL**: Especifica la dirección de acceso público del sitio web en Internet.
 - **Compute Mode**: Especifica si el sitio web se ejecuta en modo libre, compartido, básico o estándar. Para obtener más información acerca de los modos de grupo de escala web, consulte [Escalación de un sitio web](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-scale-websites).
 - **FTP Hostname**: Especifica la URL que hay que usar al publicar en el sitio web por FTP.
 - **FTPS Hostname**: Especifica la URL que hay que usar al publicar en el sitio web por FTPS.
 - **Deployment User/FTP User**: Indica la cuenta que se ha usado al implementar el sitio web en Microsoft Azure por FTP o Git.
 - **FTP Diagnostic Logs**: Especifica la ubicación de FTP de los registros de diagnóstico del sitio web, si el registro de diagnóstico está habilitado en la página de administración **Configure**.
 - **FTP Diagnostic Logs**: Especifica la ubicación de FTPS de los registros de diagnóstico del sitio web, si el registro de diagnóstico está habilitado en la página de administración **Configure**.
 - **Location**: Especifica la región del centro de datos que hospeda al sitio web.
 - **Subscription Name**: Especifica el nombre de la suscripción a la que está asociado el sitio web.
 - **Subscription ID**: Especifica el Id. de suscripción (GUID) exclusivo de la suscripción a la que está asociado el sitio web.

Implementaciones
----------------

Esta pestaña solo aparece si ha configurado la implementación desde el control del código fuente. La página de administración **Implementaciones** proporciona un resumen de todas las implementaciones realizadas en el sitio web usando el método de publicación de su elección. Si la publicación Git se ha configurado para el sitio web, pero no se han realizado implementaciones, la página de administración **Implementaciones** proporciona información que indica cómo usar GIT para implementar su aplicación web en el sitio web.

Monitor
-------

La página de administración **Monitor** proporciona un gráfico que muestra la información de uso del sitio web. De forma predeterminada, este gráfico muestra las mismas métricas que el gráfico de la página **Panel**, como se ha indicado anteriormente en la sección Panel. El gráfico también se puede configurar para que muestre las métricas Operaciones correctas de HTTP, Redirecciones de HTTP, Errores HTTP 401, Errores HTTP 403, Errores HTTP 404 y Errores HTTP 406. Para obtener más información acerca de estas métricas, consulte [Supervisión de sitios web](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/).

WebJobs
-------

La página de administración WebJobs le permite crear tareas bajo demanda, programadas o de ejecución continua para su sitio web. Para obtener más información, consulte [Utilización de la característica WebJobs en Sitios web Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-create-web-jobs/).

Configure
---------

La página de administración **Configure** se usa para configurar las opciones específicas de la aplicación, entre las que se incluyen:

-   **General**: Configura la versión de .NET framework, PHP, Python o Java que su aplicación web necesita. Para los sitios en modo estándar o básico, tiene la opción de elegir una plataforma de 64 bits. Establezca **Managed Pipeline Mode** en **Classic** solo si tiene sitios web heredados que se ejecutan exclusivamente en versiones antiguas de IIS (**Integrated** es el predeterminado). Para permitir que su sitio web utilice aplicaciones de modelos de solicitud en tiempo real como el chat, puede establecer **Web Sockets** en **On**. Para permitir la edición de su sitio web en línea directamente, establezca **Edit in Visual Studio Online** en **On**.
-   **Certificates**: Cargue un certificado SSL para un dominio personalizado. Los certificados SSL se pueden cargar solo en modo estándar o básico. Aquí se incluyen los certificados que cargue y estos se pueden asignar a cualquier sitio web de su suscripción y región. Los certificados con un carácter comodín (certificados con un asterisco) con compatibles.
-   **Domain Names**: Vea o agregue nombres de dominio personalizados adicionales para un sitio web. Los nombres de dominio personalizados solo se pueden usar en modo compartido, básico o estándar.
-   **SSL Bindings**: Los enlaces SSL a los dominios personalizados solo se pueden usar en modo básico o estándar. Seleccione un modo SSL (**SNI**, **IP** o **No SSL**) para un nombre de dominio concreto. Si elige SNI o IP, puede especificar un certificado para el dominio de entre los certificados que haya cargado.
-   **Implementaciones**: Esta sección aparece solo si ha habilitado la implementación desde el control de código fuente. Utilice estas opciones para configurar las implementaciones.
-   **Diagnóstico de aplicaciones**: Configure las opciones para recopilar información de diagnóstico de una aplicación web que admita el registro. Puede seleccionar el registro en el sistema de archivos o en una cuenta de almacenamiento de Microsoft Azure y elegir un nivel de registro para especificar la cantidad de información recopilada.
-   **Site Diagnostics**: Configure las opciones para recopilar información de diagnóstico de su sitio web o bien permitir que Visual Studio 2012 o Visual Studio 2013 depuren su sitio web de forma remota durante 48 horas como máximo.
-   **Monitoring**: Para los sitios web en modo estándar, pruebe la disponibilidad de los extremos HTTP o HTTPS desde ubicaciones geodistribuidas.
-   **Developer Analytics**: Los análisis supervisan el rendimiento de su aplicación web. Elija un complemento de análisis de la tienda de Microsoft Azure o seleccione un proveedor de análisis personalizado como New Relic.
-   **App Settings**: Especifique pares de nombre/valor que su aplicación web cargará al inicio. Para los sitios .NET, estas opciones de configuración se inyectarán en AppSettings de configuración .NET del sitio web en tiempo de ejecución y reemplazarán la configuración existente. Para los sitios web de PHP y Node, estas opciones de configuración estarán disponibles como variables de entorno en tiempo de ejecución.
-   **Connection Strings**: Vea las cadenas de conexión a los recursos vinculados. Para los sitios .NET, estas cadenas de conexión se inyectarán en connectionStrings de la configuración de .NET del sitio web en tiempo de ejecución y reemplazarán las entradas existentes donde la clave está vinculada al nombre de la base de datos. Para los sitios web de PHP y Node, estas opciones de configuración estarán disponibles como variables de entorno en tiempo de ejecución.
-   **Default Documents**: Agregue el documento predeterminado del sitio web a esta lista, si es que todavía no aparece en ella. Si en la lista de su sitio web aparece más de uno de estos archivos, asegúrese de que el documento predeterminado de su sitio web aparezca al principio de la lista cambiando el orden de los archivos en esta.
-   **Handler Mappings**: Agrega procesadores de script personalizados que controlan las solicitudes para tipos de archivo específicos (por ejemplo, \*.php).
-   **Virtual Applications and Directories**: Configure las aplicaciones y los directorios virtuales asociados a su sitio web. También tiene la opción de marcar un directorio virtual como aplicación en la configuración del sitio.

Para obtener más información acerca de cómo configurar un sitio web, consulte [Configuración de Sitios web](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-configure-websites).

Scale
-----

En la página de administración **Scale**, puede especificar el modo de grupo de escala web (**libre**, **compartido**, **básico** o **estándar**). Los modos **compartido**, **básico** y **estándar** proporcionan un rendimiento mejor. Los modos **compartido**, **básico** y **estándar** le permiten incrementar el valor del **recuento de instancia**, que es el número de máquinas virtuales que usa su sitio web y otros sitios del mismo grupo de escala web.

En el modo **estándar**, también puede incrementar la cantidad de núcleos y la capacidad de memoria de cada instancia cambiando el valor del **tamaño de instancia**. Para conseguir una mayor rentabilidad, puede elegir la opción **Autoscale** para que Microsoft Azure asigne recursos a su sitio web de forma dinámica.

Para obtener más información acerca de cómo configurar las opciones de escala para un sitio web, consulte [Escalación de un sitio web](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-scale-websites).

Linked Resources
----------------

La página de administración **Linked Resources** proporciona una lista de recursos de Microsoft Azure que usa su sitio web, incluyendo las bases de datos SQL y MySQL y las cuentas de almacenamiento de Azure. Haga clic en el nombre del recurso para administrarlo.

Backups
-------

La página de administración **Backups** le permite crear copias de seguridad automatizadas o manuales de un sitio web, restaurar su sitio web a un estado previo o crear un sitio web basado en una de sus copias de seguridad. Para obtener más información, consulte [Copias de seguridad de sitios web de Microsoft Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-backup/) y [Restauración de un sitio web de Microsoft Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-restore/).

Iconos de la página de administración
-------------------------------------

Los iconos se muestran en la parte inferior de cada página de administración del sitio web. Muchos de estos iconos aparecen en varias páginas y algunos de ellos se muestran solo en algunas. Los iconos siguientes se muestran en la parte inferior de la página de administración **Panel**:

-   **Browse**: Abre la página predeterminada de un sitio web.
-   **Stop**: Detiene el sitio web.
-   **Restart**: Reinicia el sitio web.
-   **Manage Domains**: Asigna un dominio al sitio web en cuestión. No disponible para los sitios en modo de escalado **libre**.
-   **Delete**: Elimina el sitio web.
-   **WebMatrix**: Abre los sitios web compatibles en WebMatrix, lo que le permite realizar cambios en el sitio web y publicarlos en el sitio web de Microsoft Azure.

Los iconos siguientes no se muestran en la parte inferior de la página de administración **Panel**, sino que están en la parte inferior de otras página de administración para realizar tareas concretas:

-   **Add Metrics**: Situado en la parte inferior de la página de administración **Monitor**, le permite agregar métricas al gráfico mostrado en la página de administración Monitor.
-   **Link**: Situado en la parte inferior de la página de administración **Linked Resources**, le permite crear vínculos de administración a otros recursos de Microsoft
-   Azure. Por ejemplo, si su sitio web obtiene acceso a una base de datos SQL, puede crear un vínculo de administración a esta base de datos haciendo clic en **Link**.

