<properties linkid="manage-scenarios-how-to-manage-websites" urlDisplayName="How to manage" pageTitle="How to manage websites - Microsoft Azure service management" metaKeywords="Azure portal website management" description="A reference for the Portal website management pages in Microsoft Azure. Details are provided for each website management page." metaCanonical="" services="web-sites" documentationCenter="" title="How to Manage Websites" authors="cephalin"  solutions="" writer="mwasson" manager="wpickett" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/18/2014" ms.author="mwasson" />

# <a name="howtomanage"></a>Administre sitios web a través del Portal de administración de Azure.

Los sitios web se administran en el portal de Azure con un conjunto de páginas o "pestañas". A continuación se describen todas las páginas de administración de sitios web.

## QuickStart

La página de administración **QuickStart** incluye las secciones siguientes:

-   **Obtener herramientas**: Proporciona vínculos para instalar [WebMatrix][WebMatrix] y el [SDK de Azure][SDK de Azure].
-   **Publish your app**: Proporciona vínculos para descargar el perfil de publicación del sitio web, restablecer las credenciales de implementación para el sitio web, agregar la ranura de la publicación preconfigurada (implementación) en un sitio no preconfigurado y obtener información acerca de la publicación preconfigurada.
-   **Control de código fuente integrado**: Configuración y administración de implementaciones con las herramientas de control del código fuente o sitios web como TFS, CodePlex, GitHub, Dropbox, Bitbucket o Local Git.

## Panel

La página de administración **Panel** incluye las secciones siguientes:

Un gráfico que resume el uso del sitio web como medidas de determinadas métricas.

-   **Tiempo de CPU**: Una medida del uso de CPU del sitio web.
-   **Datos de entrada**: Una medida de los datos que el sitio web recibe de los clientes.
-   **Datos de salida**: Una medida de los datos que el sitio web envía a los clientes.
-   **Errores del servidor HTTP**: Número de mensajes de HTTP "5xx Server Error" enviados.
-   **Solicitudes**: Recuento de las solicitudes realizadas en el sitio web de todos los clientes.

**Nota:** puede agregar métricas de rendimiento adicionales en la página de administración **Supervisar** seleccionando **Agregar métricas** en la parte inferior de la página. Para obtener más información, consulte [Supervisión de sitios web][Supervisión de sitios web].

**Estado de extremo web**: Lista que incluye los extremos web configurados para la supervisión. Si no se han configurado extremos, haga clic en **Configurar supervisión de extremos web** y vaya a la sección **Supervisión** de la página de administración **Configurar**. Para obtener más información, consulte [Supervisión de sitios web][Supervisión de sitios web].

**Estado de escalado automático**: En modo estándar, puede escalar automáticamente sus recursos para emplear solo los necesarios. Para habilitar la autoescala, seleccione **Configurar escalado automático**, que le lleva a la página **Escalar**. Si su sitio web está en modo libre o compartido, tendrá que cambiarlo al modo estándar (puede hacerlo en la página **Escalar**) antes de configurar la autoescala. **Registros de operaciones de escalado automático** le lleva al portal **Servicios de administración** donde puede ver el historial de autoescala de su sitio web. La consulta predeterminada es de las últimas 24 horas, aunque se puede modificar.

**Información general del uso**: Esta sección muestra las estadísticas de uso de la CPU, el sistema de archivos y la memoria del sitio web.

**Recursos vinculados**: Esta sección muestra una lista de recursos como una base de datos SQL o MySQL, o una cuenta de almacenamiento de Azure, que estén conectados a su sitio web. Haga clic en el nombre del recurso para administrarlo. Si tiene una base de datos MySQL, si hace clic en su nombre le llevará a la página de administración de ClearDB. Allí podrá ver sus métricas de rendimiento o ir al panel de ClearDB, donde puede actualizar su base de datos MySQL, si procede. Si no se incluye ningún recurso, haga clic en **Administrar recursos vinculados** para ir la página **Recursos vinculados**, donde podrá agregar el vínculo a un recurso para su sitio web.

Una sección **Vista rápida** que incluye la siguiente información resumida y los vínculos (dependiendo de su configuración, algunas de las opciones siguientes podrían no aparecer):

-   **Ver complementos aplicables**: Abre el cuadro de diálogo **Comprar en la Tienda**, donde puede elegir los complementos que desee comprar y que ofrecen una funcionalidad adicional para su sitio web. Algunos complementos podrían no estar disponibles en su región o entorno.
-   **Ver cadenas de conexión**: Visualización de las cadenas de conexión de la base de datos de su sitio web.
-   **Descargar el perfil de publicación**: Haga clic en este vínculo para descargar el perfil de publicación de su sitio web. El perfil de publicación contiene sus credenciales (nombre de usuario y contraseña) y las URL para cargar el contenido en su sitio web con FTP y Git. El archivo de perfil está en formato XML y se puede ver en un editor de texto.
-   **Configurar credenciales de implementación**: Haga clic para crear un nombre de usuario y una contraseña para cargar el contenido en su sitio web con FTP o Git. Puede usar estas credenciales para insertar contenido en cualquier sitio web de su suscripción. (Consulte [Credenciales FTP][Credenciales FTP]). **Nota**: no es posible autenticarse en un host FTP o en un repositorio Git usando las credenciales de la cuenta Microsoft (Live ID).
-   **Restablecer las credenciales de su perfil de publicación**: Restablece el perfil de publicación de su sitio web. Dejarán de ser válidos los perfiles de publicación descargados anteriormente.
-   **Configurar implementación desde control de código fuente**: Muestra un cuadro de diálogo donde puede configurar la publicación continua desde Team Foundation Service, CodePlex, GitHub, Dropbox, Bitbucket o Local Git.
-   **Agregar un nuevo espacio de implementación**: En sitios en modo estándar, utilice esta característica para crear un espacio de ensayo para el sitio. El espacio de ensayo (sitio de ensayo) le permite validar el contenido y la configuración del sitio antes de que pase a producción. También puede usar la versión de ensayo del sitio para agregar gradualmente actualizaciones y, a continuación, pasar el sitio a producción cuando las actualizaciones se hayan completado en el espacio de ensayo. (No puede agregar un espacio a un sitio que ya está en ensayo).
-   **Editar en Visual Studio Online**: Haga clic en este vínculo para editar su sitio web en línea directamente usando Visual Studio Online en el portal de Microsoft Azure. Esta opción no aparecerá a menos que la habilite en la página **Configurar**.
-   **Desconectar de Dropbox**: Si ha configurado una conexión con Dropbox para la implementación, este vínculo le permite desconectarse.
-   **Eliminar repositorio de Git**: Si ha configurado un repositorio Git, este vínculo le permite eliminarlo.
-   **Estado**: Indica si el sitio web se está ejecutando.
-   **Servicios de administración**: Haga clic en el vínculo **Registros de operaciones** para ver los registros de operaciones de su sitio web desde el Portal de servicios de administración de Microsoft Azure.
-   **Dirección IP virtual**: Muestra la dirección IP virtual del sitio web si ha configurado un enlace SSL basado en IP para el sitio web en la sección **Enlaces SSL** de la pestaña **Configurar**.
-   **Dirección URL del sitio**: Especifica la dirección de acceso público del sitio web en Internet.
-   **Modo de proceso**: Especifica si el sitio web se ejecuta en modo libre, compartido, básico o estándar. Para obtener más información acerca de los modos de grupo de escala web, consulte [Escalación de un sitio web][Escalación de un sitio web].
-   **Nombre de host de FTP**: Especifica la URL que hay que usar al publicar en el sitio web por FTP (consulte [FTP Credentials][Credenciales FTP]).
-   **Nombre de host de FTPS**: Especifica la URL que hay que usar al publicar en el sitio web por FTPS (consulte [FTP Credentials][Credenciales FTP]).
-   **Usuario de implementación / FTP**: Indica la cuenta que se ha usado al implementar el sitio web en Microsoft Azure por FTP o Git (consulte [Credenciales de FTP][Credenciales FTP]).
-   **Registros de diagnóstico de FTP**: Especifica la ubicación de FTP de los registros de diagnóstico del sitio web, si el registro de diagnóstico está habilitado en la página de administración **Configurar**.
-   **Registros de diagnóstico de FTPS**: Especifica la ubicación de FTPS de los registros de diagnóstico del sitio web, si el registro de diagnóstico está habilitado en la página de administración **Configurar**.
-   **Ubicación**: Especifica la región del centro de datos que hospeda al sitio web.
-   **Nombre de la suscripción**: Especifica el nombre de la suscripción a la que está asociado el sitio web.
-   **Id. de suscripción**: Especifica el Id. de suscripción (GUID) exclusivo de la suscripción a la que está asociado el sitio web.

## Implementaciones

Esta pestaña solo aparece si ha configurado la implementación desde el control del código fuente. La página de administración **Implementaciones** proporciona un resumen de todas las implementaciones realizadas en el sitio web usando el método de publicación de su elección. Si la publicación Git se ha configurado para el sitio web, pero no se han realizado implementaciones, la página de administración **Implementaciones** proporciona información que indica cómo usar GIT para implementar su aplicación web en el sitio web.

## Supervisar

La página de administración **Supervisar** proporciona un gráfico que muestra la información de uso del sitio web. De forma predeterminada, este gráfico muestra las mismas métricas que el gráfico de la página **Panel**, como se ha indicado anteriormente en la sección Panel. El gráfico también se puede configurar para que muestre las métricas Operaciones correctas de HTTP, Redirecciones de HTTP, Errores HTTP 401, Errores HTTP 403, Errores HTTP 404 y Errores HTTP 406. Para obtener más información acerca de estas métricas, consulte [Supervisión de sitios web][Supervisión de sitios web].

## Trabajos web

La página de administración Trabajos web le permite crear tareas bajo demanda, programadas o de ejecución continua para su sitio web. Para obtener más información, consulte [Utilización de la característica WebJobs en Sitios web Azure][Utilización de la característica WebJobs en Sitios web Azure].

## Configurar

La página de administración **Configurar** se usa para configurar las opciones específicas de la aplicación.

Para obtener más detalles, consulte [Configuración de Sitios web][Configuración de Sitios web].

## Escala

En la página de administración **Escala**, puede especificar el modo de grupo de escala web (**libre**, **compartido**, **básico** o **estándar**). Los modos **compartido**, **básico** y **estándar** proporcionan un rendimiento mejor. Los modos **compartido**, **básico** y **estándar** le permiten incrementar el valor del **recuento de instancia**, que es el número de máquinas virtuales que usa su sitio web y otros sitios del mismo grupo de escala web.

En el modo **estándar**, también puede incrementar la cantidad de núcleos y la capacidad de memoria de cada instancia cambiando el valor del **tamaño de instancia**. Para conseguir una mayor rentabilidad, puede elegir la opción **Escalado automático** para que Microsoft Azure asigne recursos a su sitio web de forma dinámica.

Para obtener más información acerca de cómo configurar las opciones de escala para un sitio web, consulte [Escalación de un sitio web][Escalación de un sitio web].

## Recursos vinculados

La página de administración **Recursos vinculados** proporciona una lista de recursos de Microsoft Azure que usa su sitio web, incluyendo las bases de datos SQL y MySQL y las cuentas de almacenamiento de Azure. Haga clic en el nombre del recurso para administrarlo.

## Copias de seguridad

La página de administración **Copias de seguridad** le permite crear copias de seguridad automatizadas o manuales de un sitio web, restaurar su sitio web a un estado previo o crear un sitio web basado en una de sus copias de seguridad. Para obtener más información, consulte [Copias de seguridad de sitios web de Microsoft Azure][Copias de seguridad de sitios web de Microsoft Azure] y [Restauración de un sitio web de Microsoft Azure][Restauración de un sitio web de Microsoft Azure].

## Iconos de la página de administración

Los iconos se muestran en la parte inferior de cada página de administración del sitio web. Muchos de estos iconos aparecen en varias páginas y algunos de ellos se muestran solo en algunas. Los iconos siguientes se muestran en la parte inferior de la página de administración **Panel**:

-   **Examinar**: Abre la página predeterminada de un sitio web.
-   **Detener**: Detiene el sitio web.
-   **Reiniciar**: Reinicia el sitio web.
-   **Administrar dominios**: Asigna un dominio al sitio web en cuestión. No disponible para los sitios en modo de escalado **libre**.
-   **Eliminar**: Elimina el sitio web.
-   **WebMatrix**: Abre los sitios web compatibles en WebMatrix, lo que le permite realizar cambios en el sitio web y publicarlos en el sitio web de Microsoft Azure.

Los iconos siguientes no se muestran en la parte inferior de la página de administración **Panel**, sino que están en la parte inferior de otras página de administración para realizar tareas concretas:

-   **Agregar métricas**: Situado en la parte inferior de la página de administración **Supervisar**, le permite agregar métricas al gráfico mostrado en la página de administración Monitor.
-   **Vínculo**: Situado en la parte inferior de la página de administración **Recursos vinculados**, le permite crear vínculos de administración a otros recursos de Microsoft
-   Azure. Por ejemplo, si su sitio web obtiene acceso a una base de datos SQL, puede crear un vínculo de administración a esta base de datos haciendo clic en **Vínculo**.

## Credenciales FTP

Existen dos conjuntos de credenciales FTP que puede usar: credenciales de*implementación* y credenciales de *perfil de publicación*. Estas son la diferencias principales:

**Credenciales de implementación**

-   Asociadas con una cuenta de Microsoft.
-   Se pueden utilizar para la implementación en cualquier sitio web, en todas las suscripciones asociadas con la cuenta.
-   Usted elige el nombre de usuario/contraseña
-   Normalmente se utilizan para la implementación de Git o FTP

**Credenciales de perfil de publicación**

-   Asociado con un solo sitio web.
-   Usted no elige el nombre de usuario/contraseña.
-   Se utiliza normalmente para Web Deploy, pero también se puede usar para FTP.

Puede usar cualquiera de los conjuntos de credenciales. Los nombres de host de FTP y FTPS se enumeran en el panel, en **Vista rápida**.

### Uso de las credenciales de implementación

Para configurar las credenciales de implementación:

1.  En el Portal de administración, vaya a la página **Panel** del sitio web.
2.  Haga clic en **Configurar las credenciales de implementación**.
3.  En el cuadro de diálogo, escriba un nombre de usuario y una contraseña.

Nota: En el paso 2, si ya tiene las credenciales de implementación, el portal mostrará **Reset deployment credentials**. Haga clic para configurar una nueva contraseña o cambiar el nombre de usuario.

Credenciales de implementación asociadas con una cuenta de Microsoft. Si cambia el nombre de usuario o la contraseña, el cambio se aplicará a todos los sitios web asociados con la cuenta. Si una cuenta de Azure tiene varios administradores, el cambio se aplicará a todos los sitios web asociados con sus propias credenciales.
El nombre de usuario de FTP completo es “website\\username”. Esto se enumera en el portal en **Vista rápida** como **Usuario de implementación / FTP**.

### Uso de las credenciales de perfil de publicación

Cada sitio web tiene sus propias credenciales de perfil de publicación. Para ver estas credenciales:

1.  En el Portal de administración, vaya a la página **Panel** del sitio web.
2.  Haga clic en **Descargar el perfil de publicación**.

El archivo del perfil de publicación es un archivo XML. Contiene dos perfiles, uno para Web Deploy y otro para FTP.

    <publishData>
      <publishProfile
        profileName="contoso - Web Deploy"
        publishMethod="MSDeploy"
        publishUrl="contoso.scm.azurewebsites.net:443"
        msdeploySite="contoso"
        userName="$contoso"
        userPWD="abc1234..."
        destinationAppUrl="http://contoso.azurewebsites.net"
        SQLServerDBConnectionString=""
        mySQLDBConnectionString=""
        hostingProviderForumLink="" 
        controlPanelLink="http://windows.azure.com">
        <databases/>
      </publishProfile>
      <publishProfile 
        profileName="contoso - FTP" 
        publishMethod="FTP" 
        publishUrl="ftp://waws-prod-bay-003.ftp.azurewebsites.windows.net/site/wwwroot" 
        ftpPassiveMode="True" 
        userName="contoso\$contoso" 
        userPWD=" abc1234..."  
        destinationAppUrl="http://contoso.azurewebsites.net" 
        SQLServerDBConnectionString="" 
        mySQLDBConnectionString="" 
        hostingProviderForumLink="" 
        controlPanelLink="http://windows.azure.com">
        <databases/>
      </publishProfile>
    </publishData>

Busque el perfil con `publishMethod="FTP"`. El nombre de usuario aparece en la lista `userName` y la contraseña aparece en la lista `userPWD`.

Para restablecer la contraseña haga clic en **Reset your publish profile credentials**. Para conseguir las nuevas credenciales, descargue el perfil de publicación otra vez. Las credenciales de implementación están asociadas con el sitio web. Cada sitio web tiene su propio perfil de publicación.

<!-- LINKS -->


  [WebMatrix]: http://go.microsoft.com/fwlink/?LinkID=226244
  [SDK de Azure]: http://go.microsoft.com/fwlink/?LinkId=246928
  [Supervisión de sitios web]: http://www.windowsazure.com/es-es/manage/services/web-sites/how-to-monitor-websites/
  [Credenciales FTP]: #ftp-credentials
  [Escalación de un sitio web]: http://www.windowsazure.com/es-es/manage/services/web-sites/how-to-scale-websites
  [Utilización de la característica WebJobs en Sitios web Azure]: http://www.windowsazure.com/es-es/documentation/articles/web-sites-create-web-jobs/
  [Configuración de Sitios web]: http://www.windowsazure.com/es-es/manage/services/web-sites/how-to-configure-websites
  [Copias de seguridad de sitios web de Microsoft Azure]: http://www.windowsazure.com/es-es/documentation/articles/web-sites-backup/
  [Restauración de un sitio web de Microsoft Azure]: http://www.windowsazure.com/es-es/documentation/articles/web-sites-restore/
