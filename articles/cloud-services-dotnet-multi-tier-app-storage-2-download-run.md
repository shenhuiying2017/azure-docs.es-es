<properties linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" urlDisplayName="Step 2: Download and Run" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 2: Download and run" metaKeywords="Azure tutorial, deploying email service app, publishing email service" description="The second tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Configuring and Deploying the Azure Email Service application - 2 of 5" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

Configuración e implementación de la aplicación Servicio de correo electrónico de Azure: tutorial 2 de 5
========================================================================================================

Este es el segundo tutorial de la serie de cinco que muestra cómo crear e implementar la aplicación de ejemplo de Servicio de correo electrónico de Azure. Para obtener información sobre la serie de tutoriales y la aplicación, consulte el [primer tutorial de la serie](/es-es/develop/net/tutorials/multi-tier-web-site/1-overview/).

En este tutorial se muestra cómo configurar su equipo para la implementación de Azure y cómo implementar la aplicación Servicio de correo electrónico de Azure en el Servicio de nube de Azure mediante uno de los siguientes productos:

-   Visual Studio 2012
-   Visual Studio 2012 Express para Web
-   Visual Studio 2010
-   Visual Web Developer Express 2010

> [WACOM.NOTE] Una vez escrito este tutorial, se publicó Visual Studio 2013 y se actualizaron el SDK y el Portal de administración de Azure. Se han agregado notas como esta en momentos en los que tiene que realizar un procedimiento distinto si usa Visual Studio 2013 y el SDK más actualizado. Las notas se escribieron en marzo de 2014 y los procedimientos revisados se han probado con la versión 2.3 del SDK.

Puede abrir una cuenta de Azure de manera gratuita y, si todavía no tiene Visual Studio 2013, el SDK instala automáticamente Visual Studio 2013 Express para Web. De este modo, puede empezar a desarrollar contenido para Azure sin coste alguno.

En este tutorial, aprenderá a realizar las siguientes tareas:

-   Configuración de su equipo para desarrollar contenido de Azure mediante la instalación del SDK de Azure
-   Configuración y prueba de la aplicación Servicio de correo electrónico de Azure en su máquina local
-   Publicación de la aplicación en Azure
-   Visualización y edición de tablas, colas y blobs de Azure mediante Visual Studio o el Explorador de almacenamiento de Azure
-   Configuración del seguimiento y visualización de los datos de seguimiento
-   Escalado de la aplicación aumentando el número de instancias de rol de trabajo

**Nota:**

Necesita una cuenta de Azure para completar este tutorial. Puede crear una [cuenta de evaluación gratuita](/en-us/pricing/free-trial/) o [activar los beneficios de suscripción a MSDN](/en-us/pricing/member-offers/msdn-benefits/).

### Secciones del tutorial

-   [Configuración del entorno de desarrollo](#setupdevenv)
-   [Configuración de una cuenta de Azure gratuita](#setupwindowsazure)
-   [Creación de una cuenta de almacenamiento de Azure](#createWASA)
-   [Instalación del Explorador de almacenamiento de Azure](#installASE)
-   [Creación de un servicio en la nube](#createcloudsvc)
-   [Descarga y ejecución de la solución completa](#downloadcnfg)
-   [Visualización del almacenamiento del desarrollador en Visual Studio](#StorageExpVS)
-   [Configuración de la aplicación para el almacenamiento de Azure](#conf4azureStorage)
-   [Implementación de la aplicación en Azure](#deployAz)
-   [Aumento de nivel de la aplicación de ensayo a producción](#swap)
-   [Configuración de la aplicación para usar SendGrid](#sendGrid)
-   [Configuración y visualización de datos de seguimiento](#trace)
-   [Incorporación de otra instancia de rol de trabajo para gestionar una carga aumentada](#addRole)

> [WACOM.NOTE] En la siguiente sección sobre la instalación de SDK, el vínculo correcto si usa Visual Studio 2013 es <http://go.microsoft.com/fwlink/?LinkID=324322>.

[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]

<a name="createWASA"></a>Creación de una cuenta de almacenamientoCreación de una cuenta de almacenamiento de Azure
-----------------------------------------------------------------------------------------

Cuando ejecuta la aplicación de ejemplo en Visual Studio, puede obtener acceso a tablas, colas y blobs en el almacenamiento de desarrollo de Azure o en una cuenta de almacenamiento de Azure en la nube. El almacenamiento de desarrollo usa una base de datos LocalDB de SQL Server Express para emular la forma en la que el almacenamiento de Azure funciona en la nube. En este tutorial comenzará usando el almacenamiento de desarrollo y, a continuación, conocerá cómo configurar la aplicación para usar una cuenta de almacenamiento en la nube cuando se ejecute en Visual Studio. En esta sección del tutorial, creará la cuenta de almacenamiento de Azure que configurará en Visual Studio para usarla más tarde en el tutorial.

1.  En el explorador, abra el [Portal de administración de Azure](http://manage.windowsazure.com).

2.  En el [Portal de administración de Azure](http://manage.windowsazure.com), haga clic en **Almacenamiento** y, a continuación, en **New**.

    ![Nuevo almacenamiento](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png)

3.  Haga clic en **Quick Create**.

    ![Creación rápida](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png)

4.  En el cuadro de entrada de la dirección URL, especifique un prefijo de la dirección URL.

    Este prefijo y el texto que verá debajo de la casilla formarán la dirección URL exclusiva para la cuenta de almacenamiento. Si alguien ya ha usado el prefijo establecido, verá un mensaje que le indica que ya está en uso ("The storage name is already in use") sobre el cuadro de texto y tendrá que seleccionar un prefijo distinto.

5.  Establezca la región en el área en el que desee implementar la aplicación.

6.  Establezca el cuadro desplegable **Replication** en **Locally redundant**.

    Cuando se habilita la replicación geográfica para una cuenta de almacenamiento, el contenido almacenado se replica en una ubicación secundaria para habilitar la conmutación por error en caso de un desastre importante en la ubicación principal. La replicación geográfica puede suponer costes adicionales. Lo normal es que no quiera pagar por el servicio de replicación geográfica para las cuentas de prueba y desarrollo. Para obtener más información, consulte [Administración de cuentas de almacenamiento](/es-es/manage/services/storage/how-to-manage-a-storage-account/).

7.  Haga clic en **Create Storage Account**.

    En la imagen siguiente, se crea una cuenta de almacenamiento con la dirección URL `aestest3.core.windows.net`.

    ![creación de almacenamiento con prefijo de la dirección URL](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png)

    Este paso puede tardar varios minutos en completarse. Mientras espera, puede repetir estos pasos y crear una cuenta de almacenamiento de producción. A veces es recomendable disponer de una cuenta de almacenamiento de prueba para el desarrollo local y otra para el proceso de prueba en Azure, y una cuenta de almacenamiento de producción.

8.  Haga clic en la cuenta de prueba que creó en el paso anterior y, a continuación, haga clic en el icono **Manage Access Keys**.

    ![Administrar claves](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png)

    ![GUID de las claves](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG)

    Necesitará la clave de acceso **Clave de acceso primaria** o **Clave de acceso secundaria** en todo este tutorial. Puede usar una de estas claves en una cadena de conexión de almacenamiento.

    Existen dos claves, por lo que puede cambiar periódicamente la clave que usa sin que se interrumpa el servicio de una aplicación activa. Vuelva a generar la clave que no esté usando y, a continuación, podrá cambiar la cadena de conexión en la aplicación para usar la clave regenerada. Si existe solo una clave, la aplicación perderá la conectividad con la cuenta de almacenamiento cuando vuelva a generar la clave. Las claves que se muestran en la imagen ya no son válidas porque se volvieron a generar después de la captura de la imagen.

9.  Copie una de estas claves en el Portapapeles para usarla en la siguiente sección.

<a name="installASE"></a>Instalación de ASEInstalación del Explorador de almacenamiento de Azure
-----------------------------------------------------------------------

El **Explorador de almacenamiento de Azure** (ASE) es una herramienta que puede usar para realizar consultas y actualizar blobs, colas y tablas de almacenamiento de Azure. Lo usará a lo largo de este tutorial para comprobar que los datos se han actualizado correctamente y para crear datos de prueba.

1.  Instale el [Explorador de almacenamiento de Azure](http://azurestorageexplorer.codeplex.com/).

    Las capturas de pantalla muestran ASE 4, pero puede instalar ASE 5 si así lo prefiere.

2.  Inicie el **Explorador de almacenamiento de Azure** y haga clic en **Add Account**.

    ![Agregar cuenta de ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add.png)

3.  Especifique el nombre de la cuenta de almacenamiento de prueba y pegue la clave que copió anteriormente.

4.  Haga clic en **Add Storage Account**.

    ![Agregar cuenta de ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add2.png)

Existen otras herramientas disponibles que funcionan con el almacenamiento de Azure. Para obtener más información, consulte [Windows Azure Storage Explorers (2014)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx).

Creación de un servicio en la nubeCreación de un servicio en la nube
--------------------------------------------------------------------

1.  En el explorador, abra el [Portal de administración de Azure](http://manage.windowsazure.com).

2.  Haga clic en **Servicios en la nube** y, a continuación, haga clic en el icono **New**.

    ![Nube rápida](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png)

3.  Haga clic en **Quick Create**.

4.  En el cuadro de entrada de la dirección URL, especifique un prefijo de la dirección URL.

    Como ocurre con la dirección URL de almacenamiento, esta dirección URL tiene que ser exclusiva y se mostrará un mensaje de error si alguien ya está usando el prefijo que seleccionó.

5.  Establezca la región en el área en el que desee implementar la aplicación.

    Debe crear el servicio en la nube en la misma región en la que creó la cuenta de almacenamiento. Cuando la cuenta de almacenamiento y el servicio en la nube se encuentren en centros de datos diferentes (distintas regiones), la latencia aumentará y se le cobrará el ancho de banda no perteneciente al centro de datos. El ancho de banda del centro de datos es gratuito.

    Los grupos de afinidad de Azure proporcionan un mecanismo para minimizar la distancia entre los recursos de un centro de datos, lo que puede reducir la latencia. Este tutorial no usa grupos de afinidad. Para obtener información, consulte [Crear un grupo de afinidad en el Portal de administración](http://msdn.microsoft.com/es-es/library/jj156209.aspx).

6.  Haga clic en **Create Cloud Service**.

    En la siguiente imagen, se crea un servicio en la nube con la dirección URL aescloud.cloudapp.net.

    ![creación de almacenamiento con prefijo de la dirección URL](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png)

    Puede ir al siguiente paso sin esperar a que se complete este paso.

<a name="downloadcnfg"></a>Descarga y ejecuciónDescarga y ejecución de la solución completa
----------------------------------------------------------------

1.  Descargue y descomprima la [solución completa](http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36) (información en inglés).

2.  Inicie Visual Studio con permisos elevados.

    El emulador de proceso que habilita Visual Studio para ejecutar un proyecto de Azure localmente requiere permisos elevados.

    > [WACOM.NOTE] Con SDK 2.3 y versiones posteriores, el emulador de proceso de Azure predeterminado no requiere permisos elevados, pero este proyecto sigue estando configurado para usar el emulador de proceso original, por lo que sigue requiriendo permisos elevados.

3.  Restaure los paquetes NuGet.

    Para que el tamaño de la descarga siga siendo pequeño, la solución completa se proporciona sin ensamblados ni otros contenidos para los paquetes NuGet instalados. Cuando abre y crea la solución, NuGet obtiene automáticamente todo del contenido del paquete. Para que esto funcione, tendrá que habilitar la opción de restauración del paquete NuGet en Visual Studio. Si no ha habilitado aún la restauración del paquete NuGet, realice los siguientes pasos.

    > [WACOM.NOTE] En Visual Studio 2013, la configuración predeterminada es habilitar la restauración automática del paquete. Sin embargo, puesto que está abriendo un proyecto de Visual Studio 2012, la restauración no se realizará automáticamente. Espere hasta que se abra la solución y, a continuación, abra el cuadro de diálogo **Manage NuGet Packages** como se indica a continuación. Verá un botón **Restore** en la parte superior derecha: haga clic en ese botón para restaurar los paquetes.

		1. En el menú **Tools**, haga clic en **Library Package Manager** y, a continuación, haga clic en **Manage NuGet Packages for Solution**. 

		2. En la esquina inferior izquierda del cuadro de diálogo **Manage NuGet Packages**, haga clic en **Settings**.

		3. En el panel izquierdo del cuadro de diálogo **Options**, seleccione **General** en **Package Manager**.

		4. Seleccione **Allow NuGet to download missing packages during build**.

    ![Habilitación de la restauración del paquete NuGet](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/NuGetPkgRestore.png)

4.  En el menú **Archivo**, seleccione **Abrir proyecto**, diríjase a la ubicación donde descargó la solución y, a continuación, abra el archivo de la solución.

5.  En el **Explorador de soluciones**, asegúrese de que **AzureEmailService** se encuentra seleccionado como proyecto de inicio.

6.  Presione CTRL+F5 para ejecutar la aplicación.

    > [WACOM.NOTE] Para permitir la creación de la solución, necesitará agregar referencias a los ensamblados del SDK actuales. En el Explorador de soluciones, haga clic con el botón secundario en el proyecto MvcWebRole y, a continuación, haga clic en **Add** -- **Reference**. En **Ensamblados**, haga clic en **Extensiones**. Seleccione *Microsoft.WindowsAzure.Diagnostics* y *Microsoft.WindowsAzure.ServiceRuntime* y, a continuación, haga clic en **OK**. Haga lo mismo con los proyectos WorkerRoleA y WorkerRoleB.

    Aparecerá la página principal de la aplicación en el explorador.

    ![Ejecución de la aplicación](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png)

7.  Haga clic en **Create New**.

8.  Especifique algunos datos de prueba y, a continuación, haga clic en **Crear**.

    ![Ejecución de la aplicación](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png)

9.  Cree un par de entradas de lista de correo más.

    ![Página de índice de listas de correo](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png)

10. Haga clic en **Subscribers** y, a continuación, agregue algunos suscriptores. Establezca **Verified** en `true`.

    ![Página de índice de suscriptores](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png)

11. Prepárese para agregar mensajes creando un archivo *.txt* que contenga el cuerpo de un correo electrónico que desee enviar. A continuación, cree un archivo *.htm* que contenga el mismo texto pero con contenido HTML (por ejemplo, ponga algunas palabras del mensaje en negrita o cursiva). Usará estos archivos en el paso siguiente.

12. Haga clic en **Messages** y, a continuación, agregue algunos mensajes. Seleccione los archivos que creó en el paso anterior. No cambie la fecha programada, que es de forma predeterminada de una semana en el futuro. La aplicación no puede enviar mensajes hasta que configure SendGrid.

    ![Página de creación de mensajes](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png)
	<br/><br/>
    ![Página de índice de mensajes](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png)

Los datos que ha especificado y visualizado se almacenan en el almacenamiento de desarrollo de Azure. El almacenamiento de desarrollo usa una base de datos LocalDB de SQL Server Express para emular la forma en la que el almacenamiento de Azure funciona en la nube. La aplicación usa el almacenamiento de desarrollo porque es lo que se decidió en la configuración del proyecto en el momento de su descarga. Esta configuración se almacena en los archivos *.cscfg* en el proyecto **AzureEmailService**. El archivo *ServiceConfiguration.Local.cscfg* determina lo que se usa cuando se ejecuta la aplicación localmente en Visual Studio y el archivo *ServiceConfiguration.Cloud.cscfg* determina lo que se usa cuando implementa la aplicación en la nube. Más adelante verá cómo configurar la aplicación para usar la cuenta de almacenamiento de Azure que creó anteriormente.

<a name="StorageExpVS"></a>Almacenamiento del desarrolladorVisualización del almacenamiento del desarrollador en Visual Studio
---------------------------------------------------------------------------------------------------

El **Explorador de almacenamiento de Azure** en el **Explorador de servidores** proporciona una práctica vista de solo lectura de los recursos del almacenamiento de Azure.

1.  En el menú **View** en Visual Studio, seleccione **Explorador de servidores**.

2.  Expanda el nodo **(Development)** debajo del nodo **Almacenamiento de Azure**.

    > [WACOM.NOTE] Con el SDK actual, expanda **Azure**/**Almacenamiento**/**(Development)**.

3.  Expanda **Tablas** para ver las tablas que creó en los pasos anteriores.

    ![Explorador de servidores](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png)

4.  Haga doble clic en la tabla **MailingList**.

    ![Explorador de almacenamiento de VS](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png)

    Observe cómo la ventana muestra los distintos esquemas de la tabla. Las entidades `MailingList` disponen de una propiedad `Description` y `FromEmailAddress` y las entidades `Subscriber` disponen de la propiedad `Verified` (más `SubscriberGUID` que no se muestra porque la imagen no es lo suficientemente ancha). La tabla cuenta con columnas para todas las propiedades y, si una fila de la tabla determinada es para una entidad que no dispone de una propiedad determinada, la celda aparece en blanco.

No puede usar el explorador de almacenamiento en Visual Studio para actualizar o eliminar recursos de almacenamiento de Azure. Puede usar el [Explorador de almacenamiento de Azure](http://azurestorageexplorer.codeplex.com/) para actualizar o eliminar los recursos de almacenamiento de desarrollo. Para configurar el Explorador de almacenamiento de Azure para usar el almacenamiento de desarrollo, haga clic en la casilla **Developer Storage** del cuadro de diálogo **Add Storage Account**.

> [WACOM.NOTE] Con la versión más reciente del SDK, puede actualizar el almacenamiento de desarrollo en el **Explorador de servidores**.

<a name="conf4azureStorage"></a>Uso de la cuenta de almacenamientoConfiguración de la aplicación para usar la cuenta de almacenamiento de Azure
---------------------------------------------------------------------------------------------------------------

A continuación, verá cómo configurar la aplicación de manera que use la cuenta de almacenamiento de Azure cuando se ejecute en Visual Studio en lugar del almacenamiento de desarrollo. Existe una nueva forma de hacer esto en Visual Studio que se introdujo en la versión 1.8 del SDK, y una forma más antigua que implica copiar y pegar la configuración del Portal de administración de Azure. Los siguientes pasos muestran la nueva forma de configurar los valores de la cuenta de almacenamiento.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en **MvcWebRole** debajo de **Roles** en el proyecto **AzureEmailService** y, a continuación, haga clic en **Propiedades**.

    ![Clic con el botón secundario en Propiedades](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)

2.  Haga clic en la pestaña **Settings**. En el cuadro desplegable **Configuración de servicio**, seleccione **Local**.

3.  Seleccione la entrada **StorageConnectionString** y verá un botón con unos puntos suspensivos (**...**) en el extremo derecho de la línea. Haga clic en el botón con los puntos suspensivos para abrir el cuadro de diálogo **Cadena de conexión de cuenta de almacenamiento**.

    ![Clic con el botón secundario en Propiedades](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png)

4.  En el cuadro de diálogo **Create Storage Connection String**, haga clic en **Your subscription** y, a continuación, haga clic en **Download Publish Settings**.

    > [WACOM.NOTE] Con el SDK más reciente, este diálogo le permite iniciar sesión en Azure y seleccionar de forma sencilla la cuenta de almacenamiento que desea usar.

    ![Clic con el botón secundario en Propiedades](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png)

    Visual Studio inicia una nueva instancia del explorador predeterminado con la dirección URL para la página de configuración de publicación de descargas del portal de Azure. Si no ha iniciado sesión en el portal, se le solicitará que lo haga. Una vez que haya iniciado sesión, el explorador le solicitará que guarde la configuración de publicación. Anote el lugar en el que guarda la configuración.

    ![configuración de publicación](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-3.png)

5.  En el cuadro de diálogo **Create Storage Connection String**, haga clic en **Import** y, a continuación, diríjase al archivo de configuración de publicación que guardó en el paso anterior.

6.  Seleccione la cuenta de almacenamiento y suscripción que desee usar y, a continuación, haga clic en **OK**.

    ![selección de la cuenta de almacenamiento](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-5.png)

7.  Siga el mismo procedimiento que usó para la cadena de conexión `StorageConnectionString` para configurar la cadena de conexión `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

    No tiene que descargar de nuevo el archivo de configuración de la publicación. Cuando haga clic en los puntos suspensivos de la cadena de conexión `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`, verá que el cuadro de diálogo **Create Storage Connection String** recuerda la información de suscripción. Si hace clic en el botón de radio **Your subscription**, todo lo que tiene que hacer es seleccionar los mismos valores de **Suscripción** y **Nombre de cuenta** que ha seleccionado anteriormente y, a continuación, hacer clic en **OK**.

8.  Siga el mismo procedimiento que usó para las dos cadenas de conexión para el rol MvcWebRole para configurar las cadenas de conexión para los roles WorkerRoleA y WorkerRoleB.

### Método manual para la configuración de credenciales de cuenta de almacenamiento

El siguiente procedimiento muestra la forma manual de configurar los valores de la cuenta de almacenamiento. Si usó el método automático que se mostró en el procedimiento anterior, puede omitir este procedimiento o continuar leyendo para ver las tareas que realizó el método automático en segundo plano.

1.  En el explorador, abra el [Portal de administración de Azure](http://manage.windowsazure.com).

2.  Haga clic en la pestaña **Almacenamiento** y, a continuación, haga clic en la cuenta de prueba que creó en el paso anterior y en el icono **Manage Keys**.

    ![Administrar claves](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png)

    ![GUID de las claves](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG)

3.  Copie la clave de acceso primaria y secundaria.

4.  En el **Explorador de soluciones**, haga clic con el botón secundario en **MvcWebRole** debajo de **Roles** en el proyecto **AzureEmailService** y, a continuación, haga clic en **Propiedades**.

    ![Clic con el botón secundario en Propiedades](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)

5.  Haga clic en la pestaña **Settings**. En el cuadro desplegable **Configuración de servicio**, seleccione **Local**.

6.  Seleccione la entrada **StorageConnectionString** y verá un botón con unos puntos suspensivos (**...**) en el extremo derecho de la línea. Haga clic en el botón con los puntos suspensivos para abrir el cuadro de diálogo **Cadena de conexión de cuenta de almacenamiento**.

    ![Clic con el botón secundario en Propiedades](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png)

7.  En el cuadro de diálogo **Create Storage Connection String**, seleccione el botón de radio **Manually entered credentials**. Especifique el nombre de la cuenta de almacenamiento y la clave de acceso primaria y secundaria que copió en el portal.

8.  Haga clic en **OK**.

Puede usar el mismo procedimiento para configurar los valores de los roles de trabajo o puede propagar la configuración del rol web para los roles de trabajo editando el archivo de configuración. En los siguientes pasos se explica cómo editar el archivo de configuración. Esto sigue formando parte del método manual para el establecimiento de credenciales de almacenamiento, que no tiene que hacer si ya ha propagado la configuración para los roles de trabajo mediante el método automático.

1.  Abra el archivo **ServiceConfiguration.Local.cscfg** que se encuentra en el proyecto **AzureEmailService**.

    En el elemento `Role` para `MvcWebRole` verá un elemento `ConfigurationSettings` con la configuración que actualizó mediante la interfaz de usuario de Visual Studio.

           <Role name="MvcWebRole">
             <Instances count="1" />
             <ConfigurationSettings>
               <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
               <Setting name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
             </ConfigurationSettings>
           </Role>

    En los elementos `Role` de los dos roles de trabajo verá las dos mismas cadenas de conexión.

2.  Elimine los elementos `Setting` para esas dos cadenas de conexión de los elementos `WorkerRoleA` y `WorkerRoleB` y, a continuación, copie y pegue en su lugar los elementos `Setting` del elemento `MvcWebRole`.

Para obtener más información sobre los archivos de configuración, consulte [Configurar un proyecto de Azure](http://msdn.microsoft.com/es-es/library/windowsazure/ee405486.aspx).

### Prueba de la aplicación configurada para usar la cuenta de almacenamiento

1.  Presione CTRL+F5 para ejecutar la aplicación. Especifique algún dato haciendo clic en los vínculos **Mailing Lists**, **Subscribers** y **Messages** como hizo anteriormente en este tutorial.

Ahora puede usar el **Explorador de almacenamiento de Azure** o el **Explorador de servidores** para ver los datos que la aplicación especificó en las tablas de Azure.

### Uso del Explorador de almacenamiento de Azure para ver los datos especificados en la cuenta de almacenamiento

1.  Abra el **Explorador de almacenamiento de Azure**.

2.  Seleccione la cuenta de almacenamiento para la que especificó las credenciales anteriormente.

3.  En **Storage Type**, seleccione **Tables**.

4.  Seleccione la tabla `MailingList` y, a continuación, haga clic en **Query** para ver que los datos que especificó en las páginas **Mailing List** y **Subscriber** de la aplicación.

  ![ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase1.png)

### Uso del Explorador de servidores para ver los datos especificados en la cuenta de almacenamiento

1.  En el **Explorador de servidores** (o en el **Explorador de bases de datos**), haga clic con el botón secundario en **Almacenamiento de Azure** y haga clic en **Agregar nueva cuenta de almacenamiento**.

2.  Siga el mismo procedimiento que usó antes para configurar las credenciales de la cuenta de almacenamiento.

3.  Expanda el nuevo nodo en **Almacenamiento de Azure** para ver los datos almacenados en la cuenta de almacenamiento de Azure.

    ![ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se3.png)

### Pasos opcionales para deshabilitar el inicio automático del emulador de almacenamiento de Azure

Si no usa el emulador de almacenamiento, puede disminuir el tiempo de inicio del proyecto y usar menos recursos locales deshabilitando el inicio automático para el emulador de almacenamiento de Azure.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto en la nube **AzureEmailService** y seleccione **Propiedades**.

    ![Selección de las propiedades del proyecto en la nube](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png)

2.  Seleccione la pestaña **Development**.

3.  Establezca **Start Azure storage emulator** en **False**.

    ![Deshabilitación del inicio automático del emulador de almacenamiento](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png)

    **Nota**: solo debe establecer la opción en false si no va a usar el emulador de almacenamiento.

    Esta ventana también proporciona una forma de cambiar el archivo **Configuración de servicio** que se usó cuando ejecutó localmente la aplicación desde **Local** a **Cloud** (desde *ServiceConfiguration.Local.cscfg* a *ServiceConfiguration.Cloud.cscfg*).

4.  En la bandeja del sistema de Windows, haga clic con el botón secundario en el icono del emulador de proceso y haga clic en **Shutdown Storage Emulator**.

    ![ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png)

<a name="sendGrid"></a>SendGridConfiguración de la aplicación para usar SendGrid
---------------------------------------------------------

La aplicación de ejemplo usa SendGrid para enviar correos electrónicos. Para enviar correos electrónicos mediante SendGrid, tiene que configurar una cuenta de SendGrid y, a continuación, actualizar un archivo de configuración con las credenciales de SendGrid.

**Nota:** si no quiere o no puede usar SendGrid, puede sustituirlo fácilmente por su propio servicio de correo electrónico. El código que usa SendGrid se aísla en dos métodos en el rol de trabajo B. [Tutorial 5][tut5] explica lo que tiene que cambiar para implementar un método distinto de envío de correos electrónicos. Si desea hacerlo, puede omitir este procedimiento y continuar con este tutorial; todo lo demás en la aplicación funcionará (las páginas web, la programación de correo electrónico, etc.) excepto para el envío real de correos electrónicos.

### Creación de una cuenta de SendGrid

1.  Siga las instrucciones en [Envío de correos electrónicos con SendGrid y Azure](http://www.windowsazure.com/es-es/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid") para registrarse y conseguir una cuenta gratuita.

### Actualización de las credenciales de SendGrid en las propiedades de rol de trabajo

Anteriormente en el tutorial, cuando estableció las credenciales de la cuenta de almacenamiento para el rol web y los dos roles de trabajo, es posible que se diera cuenta de que el rol de trabajo B tenía tres valores que no aparecían en el rol web o en el rol de trabajo A. Puede usar la misma interfaz de usuario ahora para configurar esos tres valores (seleccione **Cloud** en la lista desplegable **Configuración de servicio**).

Los siguientes pasos muestran un método alternativo para el establecimiento de propiedades mediante la edición del archivo de configuración.

1.  Edite el archivo *ServiceConfiguration.Cloud.cscfg* del proyecto `AzureEmailService` y especifique los valores de la contraseña y el nombre de usuario de SendGrid que obtuvo en el paso anterior en el elemento `WorkerRoleB` que contenía estos ajustes. El siguiente código muestra el elemento WorkerRoleB.

    ![SendGridSettings](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png)

2.  También existe una configuración de AzureMailServiceURL. Establezca este valor en la dirección URL que seleccionó cuando creó el servicio en la nube de Azure, por ejemplo: "http://aescloud.cloudapp.net".

Si actualiza el archivo de configuración en la nube, está configurando los valores que se usarán cuando se ejecute la aplicación en la nube. Si quería que la aplicación enviara correos electrónicos mientras se ejecutaba localmente, también tendría que actualizar el archivo *ServiceConfiguration.Local.cscfg*.

<a name="deployAz"></a>Implementación de AzureImplementación de la aplicación en Azure
---------------------------------------------------------------

Para implementar la aplicación, puede crear un paquete en Visual Studio y cargarlo usando el Portal de administración de Azure o realizar la publicación directamente desde Visual Studio. En este tutorial usará el método de publicación.

Publicará primero la aplicación en el entorno de ensayo y después aumentará su nivel pasando a producción.

### Implementación de restricciones de IP

Cuando realice la implementación de ensayo, la aplicación será accesible públicamente para cualquier persona que conozca la dirección URL. Por lo tanto, el primer paso es implementar las restricciones de IP para garantizar que no se permita el uso a personas no autorizadas. En una aplicación de producción habría que implementar un mecanismo de autenticación y autorización, como el sistema de suscripción de ASP.NET, pero estas funciones se han omitido en la aplicación de ejemplo para que siga siendo fácil de configurar, implementar y probar.

1.  Abra el archivo *Web.Release.config* que se encuentra en la carpeta raíz del proyecto `MvcWebRole` y reemplace el valor del atributo **ipAddress** 127.0.0.1 por la dirección IP. Para ver el archivo **Web.Release.config** en **Solution Explorer** tiene que expandir el archivo *Web.config*.

    Puede buscar la dirección IP utilizando la ecuación de búsqueda "Find my IP" en [Bing](http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "find my IP") o en otro motor de búsqueda.

    Cuando se publica la aplicación, se aplican las transformaciones especificadas en el archivo *Web.release.config* y se actualizan los elementos de restricción de IP en el archivo *web.config* que se implementa en la nube. Puede ver el archivo *web.config* transformado en la carpeta *AzureEmailService\\MvcWebRole\\obj\\Release\\TransformWebConfig\\transformed* una vez que se ha creado el paquete.

### Configuración de la aplicación para usar la cuenta de almacenamiento cuando se ejecuta en la nube

Anteriormente en el tutorial, cuando estableció las credenciales de la cuenta de almacenamiento para el rol web y los dos roles de trabajo, estableció las credenciales que se deben usar al ejecutar la aplicación localmente. Ahora necesita establecer las credenciales de la cuenta de almacenamiento que se usarán cuando ejecute la aplicación en la nube.

Para esta ejecución de prueba, usará las mismas credenciales para la nube que utilizó para la ejecución local. Si ha implementado una aplicación de producción, normalmente usará una cuenta distinta para la producción que la que usa para la prueba. También es recomendable para la producción usar una cuenta distinta para la cadena de conexión de diagnóstico que para la cadena de conexión de almacenamiento, pero para esta ejecución de prueba usará la misma cuenta.

Puede usar la misma interfaz de usuario para configurar las cadenas de conexión (asegúrese de que selecciona **Cloud** en la lista desplegable **Configuración de servicio**). También puede editar el archivo de configuración como se explica en los pasos siguientes.

1.  Abra el archivo *ServiceConfiguration.Local.cscfg* en el proyecto **AzureEmailService** y copie los elementos `Setting` para `StorageConnectionString` y `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

2.  Abra el archivo *ServiceConfiguration.Cloud.cscfg* en el proyecto **AzureEmailService** y pegue los elementos copiados en el elemento `Configuration Settings` para `MvcWebRole`, `WorkerRoleA` y `WorkerRoleB` reemplazando los elementos `Setting` que ya aparecen.

3.  Compruebe que el rol web y los dos elementos de rol de trabajo definen las mismas cadenas de conexión.

### Publicación de la aplicación

1.  Si aún no se ha abierto, inicie Visual Studio como administrador y abra la solución **AzureEmailService**.

2.  Haga clic con el botón secundario en el proyecto en la nube **AzureEmailService** y seleccione **Publicar**.

    ![Paquete](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png)

    Aparecerá el cuadro de diálogo **Publish Azure Application**.

    ![Paquete en la nube](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png)

3.  Si ha usado el método automático para la importación de las credenciales de la cuenta de almacenamiento anteriormente, la suscripción a Azure se encuentra en la lista desplegable y puede seleccionarla y, a continuación, hacer clic en **Next**. De lo contrario, haga clic en **Sign in to download credentials** y siga las instrucciones en [Configuración de la aplicación para usar la cuenta de almacenamiento de Azure](#conf4azureStorage) para descargar e importar la configuración de publicación.

4.  En la pestaña **Common Settings**, compruebe la configuración de la lista desplegable **Servicio en la nube**.

5.  En el cuadro desplegable **Entorno**, cambie **Production** por **Ensayo**.

    ![Panel](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png)

6.  Mantenga el valor predeterminado de **Release** para **Configuración de compilación** y **Nube** para **Configuración de servicio**.

    La configuración predeterminada de la pestaña **Advanced** es correcta para este tutorial. En la pestaña **Advanced** existen un par de valores que son útiles para el desarrollo y la realización de pruebas. Para obtener más información sobre la pestaña Advanced, consulte [Asistente para publicar aplicación de Azure](http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "pub wiz").

7.  Haga clic en **Next**.

8.  En el paso **Resumen** del asistente, haga clic en el icono para **guardar** (el icono del disquete que aparece a la derecha de la lista desplegable del perfil de destino) para guardar la configuración de publicación.

    La próxima vez que publique la aplicación, se usará la configuración guardada y no tendrá que utilizar el asistente de publicación de nuevo.

9.  Revise la configuración y, a continuación, haga clic en **Publicar**.

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png)

    Se abrirá la ventana **Registro de actividad de Azure** en Visual Studio.

10. Haga clic en el icono de la flecha derecha para expandir la información de implementación.

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png)
	<br/><br/>
    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png)

    La implementación puede tardar 5 minutos o más en completarse.

11. Cuando se haya completado el estado de implementación, haga clic en la **URL de sitio web** para iniciar la aplicación.

    ![Panel](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png)

12. Especifique algún dato en las páginas web de **Mailing List**, **Subscriber** y **Message** para probar la aplicación.

    **Nota**: elimine la aplicación una vez que haya terminado de probarla para evitar pagar por recursos que no está usando. Si usa una [cuenta de evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/ "free-trial account"), los tres roles implementados se usarán hasta el límite mensual en un par de semanas. Para eliminar una implementación mediante el Portal de administración de Azure, seleccione el servicio en la nube y haga clic en **DELETE** en la parte inferior de la página y, a continuación, seleccione la implementación de ensayo o producción.

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png)

13. En Registro de actividad de Azure en Visual Studio, seleccione **Abrir en el Explorador de servidores**.

    En **Proceso de Azure** en **Explorador de servidores** puede supervisar la implementación. Si seleccionó **Enable Remote Desktop for all roles** en el **asistente para publicar aplicación de Azure**, puede hacer clic con el botón secundario en una instancia de rol y seleccionar **Connect using Remote Desktop**.

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png)

ProducciónAumento de nivel de la aplicación de ensayo a producción
------------------------------------------------------------------

1.  En el [Portal de administración de Azure](http://manage.windowsazure.com), haga clic en el icono **Servicios en la nube** del panel izquierdo y, a continuación, seleccione su servicio en la nube.

2.  Haga clic en **Swap**.

3.  Haga clic en **Yes** para completar el intercambio VIP (IP virtual). Este paso puede tardar varios minutos en completarse.

    ![Panel](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png)

4.  Cuando se haya completado el intercambio, haga clic en el icono **Servicios en la nube** del panel izquierdo y, a continuación, seleccione el servicio en la nube.

5.  Desplácese a la pestaña **Panel** para la implementación **Production** en la sección **quick glance** de la parte inferior derecha de la página. Tenga en cuenta que **Site URL** ha cambiado de un prefijo del GUID al nombre del servicio en la nube.

    ![Panel](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png)

6.  Haga clic en el vínculo en **Site URL** o cópielo y péguelo en un explorador para probar la aplicación en producción.

    Si no ha cambiado la configuración de la cuenta de almacenamiento, los datos que especificó mientras probaba la versión de ensayo de la aplicación se muestran cuando ejecuta la aplicación en la nube.

<a name="trace"></a>SeguimientoConfiguración y visualización de los datos de seguimiento
--------------------------------------------------------------------

El seguimiento es una herramienta inestimable para la depuración de una aplicación en la nube. En esta sección del tutorial, verá cómo visualizar datos de seguimiento.

1.  Compruebe que la cadena de conexión de diagnóstico esté configurada para usar su cuenta de almacenamiento de Azure y no el almacenamiento de desarrollo.

    Si ha seguido las instrucciones anteriores del tutorial, serán las mismas. Puede comprobar que son las mismas usando la interfaz de usuario de Visual Studio (la pestaña **Settings** en **Propiedades** para los roles) o visualizando los archivos *ServiceConfiguration. * .cscfg*.

    **Nota:** es recomendable usar una cuenta de almacenamiento diferente para el seguimiento de datos de la cuenta de almacenamiento usada para los datos de producción, pero para simplificar este tutorial, se ha configurado la misma cuenta para el seguimiento.

2.  En Visual Studio, abra *WorkerRoleA.cs* en el proyecto **WorkerRoleA**, busque `ConfigureDiagnostics` y examine el método `ConfigureDiagnostics`.

         private void ConfigureDiagnostics()
         {
             DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
             config.ConfigurationChangePollInterval = TimeSpan.FromMinutes(1d);
             config.Logs.BufferQuotaInMB = 500;
             config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
             config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

             DiagnosticMonitor.Start(
                 "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
                 config);
         }

    En este código, `DiagnosticMonitor` se configura para almacenar hasta 500 MB de información de seguimiento (después de 500 MB, se sobrescriben los datos más antiguos) y para almacenar todos los mensajes de seguimiento (LogLevel.Verbose). `ScheduledTransferPeriod` transfiere los datos de seguimiento al almacenamiento cada minuto. Debe establecer `ScheduledTransferPeriod` para guardar los datos de seguimiento.

    El método `ConfigureDiagnostics` de cada uno de los roles web y de trabajo configura la escucha de seguimiento para registrar datos cuando llame a la API de seguimiento. Para obtener más información, consulte [Using Trace in Windows Azure Cloud Applications](http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "Using Trace in Windows Azure").

3.  En **Explorador de servidores**, haga doble clic en **WADLogsTable** (expanda **Almacenamiento**/**yourstorageaccountname**/**Tables**) para la cuenta de almacenamiento que agregó anteriormente. Puede especificar un [filtro para los servicios de datos de WCF](http://msdn.microsoft.com/es-es/library/windowsazure/ff683669.aspx "WCF filter") para limitar las entidades mostradas. En la siguiente imagen, solo se muestran los mensajes de advertencia y error.

    ![Panel](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png)

<a name="addRole"></a>Incorporación de una instancia de rolIncorporación de otra instancia de rol de trabajo para gestionar la carga mejorada
-----------------------------------------------------------------------------------------------------------------------

Existen dos enfoques para realizar la escalación de recursos informáticos en los roles de Azure: la especificación del [tamaño de la máquina virtual](http://msdn.microsoft.com/es-es/library/windowsazure/ee814754.aspx "VM sizes") o la especificación del recuento de instancias de las máquinas virtuales en ejecución.

El tamaño de la máquina virtual (VM) se especifica en el atributo `vmsize` del elemento `WebRole` o `WorkerRole` en el archivo *ServiceDefinition.csdef*. La configuración predeterminada es `Small`, que le ofrece un núcleo y 1,75 GB de RAM. Para aplicaciones multiproceso y que usen una gran cantidad de memoria, disco y ancho de banda, puede aumentar el tamaño de la VM para que el rendimiento sea mayor. Por ejemplo, una VM `ExtraLarge` cuenta con 8 núcleos de CPU y 14 GB de RAM. El aumento de memoria, núcleos de CPU, disco y ancho de banda en una máquina única se conoce como *escalación vertical*. Entre los candidatos a los que mejor se puede aplicar la escalación vertical se encuentran las aplicaciones web ASP.NET que usan [métodos asincrónicos](http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "Async MVC") (información en inglés). Consulte [Configurar los tamaños de los Servicios en la nube](http://msdn.microsoft.com/es-es/library/windowsazure/ee814754.aspx "VM sizes") para obtener una descripción de los recursos proporcionados por cada tamaño de la VM.

El rol de trabajo B en esta aplicación es el componente de limitación en una carga alta, ya que realiza el trabajo del envío de correos electrónicos. El rol de trabajo A solo crea mensajes de cola, lo que no consume una gran cantidad de recursos. Puesto que el rol de trabajo B no es multiproceso y no tiene un gran impacto en la memoria, no es un buen candidato para la escalación vertical. El rol de trabajo B puede escalarse linealmente (es decir, prácticamente duplicar el rendimiento cuando se duplican las instancias) mediante el aumento del recuento de instancias. El aumento del número de instancias de proceso se denomina *escalación horizontal*. Existe un coste para cada instancia, por lo que debe escalar horizontalmente solo cuando la aplicación lo requiera.

Escale horizontalmente un rol de trabajo o web actualizando la configuración en la interfaz de usuario de Visual Studio mediante la edición directa de los archivos *ServiceConfiguration. * .cscfg*. El recuento de instancias se especifica en la pestaña **Configuración** de la ventana **Propiedades** del rol en el elemento `Instances` de los archivos *.cscfg*. Cuando actualice la configuración, tendrá que implementar el archivo de configuración actualizado para que el cambio surta efecto. Para el aumento transitorio de la carga, también puede cambiar el número de instancias de rol en el Portal de administración de Azure. También puede configurar el número de instancias mediante la API de administración de Azure. Para finalizar, puede usar [el bloque de escalado automático de la aplicación](/es-es/develop/net/how-to-guides/autoscaling/) para realizar la escalación horizontal automáticamente a fin de cumplir con la carga aumentada. Para obtener más información acerca del escalado automático, consulte los vínculos que aparecen al final del [último tutorial de esta serie](/es-es/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

En esta sección del tutorial, realizará la escalación horizontal del rol de trabajo B mediante el portal de administración, pero primero verá cómo se realiza en Visual Studio.

Para realizar este procedimiento en Visual Studio, haga clic con el botón secundario en el rol en **Roles** en el proyecto en la nube y seleccione **Propiedades**.

![Clic con el botón secundario en Propiedades](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)

A continuación, seleccione la pestaña **Configuration** de la izquierda y seleccione **Cloud** en el menú desplegable **Configuración de servicio**.

![Recuento de instancias](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png)

Tenga en cuenta que también puede configurar el tamaño de la VM en esta pestaña.

En los siguientes pasos se explica cómo escalar horizontalmente mediante el Portal de administración de Azure.

1.  En el Portal de administración de Azure, seleccione el servicio en la nube y, a continuación, haga clic en **Scale**.

2.  Aumente el número de instancias del rol de trabajo B y, a continuación, haga clic en **Save**.

    ![Aumento de instancias](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png)

    El aprovisionamiento de las nuevas VM puede tardar algunos minutos.

3.  Seleccione la pestaña **Instances** para ver cada instancia de rol en su aplicación.

    ![Visualización de instancias](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png)

<a name="nextsteps"></a>Pasos siguientesPasos siguientes
--------------------------------

Ya ha visto cómo configurar, implementar y escalar la aplicación completa. Los siguientes tutoriales muestran cómo crear la aplicación desde el principio. En el [próximo tutorial](/es-es/develop/net/tutorials/multi-tier-web-site/3-web-role/), creará un rol web.

Podrá encontrar vínculos a recursos adicionales acerca de cómo utilizar las tablas, colas y blobs de almacenamiento de Azure al final del [último tutorial de la serie](/es-es/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).
[Tutorial 3](/es-es/develop/net/tutorials/multi-tier-web-site/3-web-role/)


[Set up the development environment]: #setupdevenv
[Set up a free Azure account]: #setupwindowsazure
[Create an Azure Storage account]: #createWASA
[Install Azure Storage Explorer]: #installASE
[Create a Cloud Service]: #createcloudsvc
[Download and run the completed solution]: #downloadcnfg
[View developer storage in Visual Studio]: #StorageExpVS
[Configure the application for Azure Storage]: #conf4azureStorage
[Deploy the application to Azure]: #deployAz
[Promote the application from staging to production]: #swap
[Configure the application to use SendGrid]: #sendGrid
[Configure and view trace data]: #trace
[Add another worker role instance to handle increased load]: #addRole

[firsttutorial]: /es-es/develop/net/tutorials/multi-tier-web-site/1-overview/

[tut3]: /es-es/develop/net/tutorials/multi-tier-web-site/3-web-role/
[tut5]: /es-es/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
[NewPortal]: http://manage.windowsazure.com
[managestorage]: /es-es/manage/services/storage/how-to-manage-a-storage-account/
[autoscalingappblock]: /es-es/develop/net/how-to-guides/autoscaling/


[mtas-portal-new-storage]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png
[mtas-storage-quick]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png
[mtas-create-storage-url-test]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png
[mtas-manage-keys]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png
[mtas-guid-keys]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG
[mtas-new-cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png
[mtas-create-cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png
[mtas-ase-add]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add.png
[mtas-ase-add2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add2.png

[mtas-rt-prop]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png
[mtas-mailinglist1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png
[mtas-create1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png
[mtas-mailing-list-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png
[mtas-subscribers-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png
[mtas-message-create-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png
[mtas-message-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png
[mtas-serverExplorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png
[mtas-wasVSdata]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png
[mtas-elip]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png
[mtas-enter]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png
[mtas-ase1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase1.png


[mtas-se3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se3.png
[mtas-aesp]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png
[mtas-1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png
[mtas-se4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png









[mtas-c6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png
[mtas-c7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png

[mtas-sg]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png
[mtas-trc]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png
[mtas-instanceCnt]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png

[mtas-in3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png
[mtas-in2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png
[mtas-3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-3.png
[mtas-5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-5.png
[mtas-6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png
[mtas-16]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png
[mtas-7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png
[mtas-8]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png
[mtas-9]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png
[mtas-11]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png
[mtas-12]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png
[mtas-c55]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png

[mtas-19]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png

