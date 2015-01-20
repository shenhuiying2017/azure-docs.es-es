<properties urlDisplayName="Cloud Service" pageTitle="Guía de introducción a Node.js - Tutorial de Azure" metaKeywords="Azure node.js getting started, Azure Node.js tutorial, Azure Node.js tutorial" description="Un tutorial completo que le ayuda a desarrollar una sencilla aplicación web Node.js e implementarla en Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build and deploy a Node.js application to an Azure Cloud Service" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />






# Compilación e implementación de una aplicación Node.js en un Servicio en la nube de Azure

Cuando finalice esta guía, tendrá una aplicación Node.js sencilla en ejecución en un Servicio en la nube de Azure. Los Servicios en la nube son los bloques de compilación de aplicaciones en la nube escalables en Azure. Permiten la separación y la administración independiente de los componentes front-end y back-end de su aplicación, así como su escalación horizontal.  Los Servicios en la nube proporcionan una máquina virtual dedicada y robusta para hospedar los roles de forma fiable.

Para obtener más información sobre los Servicios en la nube y sobre su comparación con Sitios web Azure y Maquinas virtuales, consulte [Comparación entre sitios web Azure, servicios en la nube y máquinas virtuales](http://azure.microsoft.com/es-es/documentation/articles/choose-web-site-cloud-service-vm/).

<p />

<div class="dev-callout"><strong>¿Desea compilar un sitio web sencillo?</strong>
<p>Si su escenario implica solo un simple sitio web front-end, considere la posibilidad de <a href="/es-es/documentation/articles/web-sites-nodejs-develop-deploy-mac/">usar de un sitio web ligero de Azure.</a> Puede actualizar a un Servicio en la nube más adelante, cuando su sitio web sea más grande y sus requisitos cambien.</p>
</div>
<br />

Siguiendo este tutorial, podrá compilar una aplicación web sencilla hospedada en un rol web. Utilizará el emulador de proceso para probar su aplicación localmente y, a continuación, la implementará con las herramientas de línea de comandos de PowerShell.

A continuación se muestra una captura de pantalla de la aplicación completada:

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/es-es/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="Ventana del explorador que muestra la página Hello World. La dirección URL indica que la página está hospedada en Azure.">
</p>



## Creación de una nueva aplicación Node

Realice las siguientes tareas para crear un nuevo proyecto de Servicio en la nube de Azure, junto con scaffolding básico de Node.js:

1. Desde el **menú Inicio** o la **pantalla Inicio**, busque **Azure PowerShell**. Finalmente, haga clic en el botón secundario en **Azure PowerShell** y seleccione **Ejecutar como administrador**.

	![Azure PowerShell icon][powershell-menu]

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  Cree un nuevo directorio **node** en su unidad C y cambie al directorio c:\node:
	
	![A command prompt displaying the commands 'mkdir c:\\node' and 'cd node'.][mkdir]

3.  Escriba el siguiente cmdlet para crear una nueva solución:

        PS C:\node> New-AzureServiceProject helloworld

    	Visualizará la siguiente respuesta:

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	El cmdlet **New-AzureServiceProject** genera una estructura básica para crear una nueva aplicación Node de Azure que será publicada en un Servicio en la nube. Contiene archivos de configuración necesarios para la publicación en Azure. El cmdlet también cambia su directorio de trabajo al directorio del servicio.

	Los archivos creados por el cmdlet **New-AzureServiceProject** son:

	-   **ServiceConfiguration.Cloud.cscfg**,
        **ServiceConfiguration.Local.cscfg** and **ServiceDefinition.csdef** are
        Azure-specific files necessary for publishing your
        application.
		
	For more information about these files, see
        [Overview of Creating a Hosted Service for Azure][].

	-   **deploymentSettings.json** stores local settings that are used by
        the Azure PowerShell deployment cmdlets.

4.  Escriba el siguiente comando para agregar un nuevo rol web con el **cmdlet Add-AzureNodeWebRole**:

        PS C:\node\helloworld> Add-AzureNodeWebRole

	Visualizará la siguiente respuesta:

	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	El cmdlet **Add-AzureNodeWebRole** crea un nuevo directorio para su aplicación y genera scaffolding para una aplicación Node.js básica. También modifica los archivos **ServiceConfiguration.Cloud.csfg**, **ServiceConfiguration.Local.csfg** y **ServiceDefinition.csdef** creados en el paso anterior para agregar entradas de configuración para el nuevo rol.

	<div class="dev-callout">
	<b>Nota:</b>
	<p>De forma predeterminada, si no proporciona un nombre de rol, se creará uno automáticamente. Puede proporcionar un nombre como primer parámetro para <b>Add-AzureNodeWebRole</b>. Por ejemplo: <code>Add-AzureNodeWebRole MyRole</code></p>
	</div>

5.  Utilice los siguientes comandos para navegar hasta el directorio **WebRole1** y, a continuación, abra el archivo **server.js** en el Bloc de notas. 

	PS C:\\node\\helloworld> cd WebRole1
        PS C:\node\helloworld\WebRole1> notepad server.js

	El archivo **server.js** fue creado por el cmdlet **Add-AzureNodeWebRole** y contiene el siguiente código de inicio. Este código es similar al ejemplo "Hello World" del sitio web [nodejs.org][], excepto por lo siguiente:

   	-   Se ha cambiado el puerto para permitir que la aplicación encuentre el puerto correcto que le ha asignado el entorno de la nube.
   	-   Se ha quitado el registro de la consola.

	![Notepad displaying the contents of server.js](./media/cloud-services-nodejs-develop-deploy-app/node13.png)

## Ejecución de su aplicación localmente en el emulador

Una de las herramientas instaladas por el SDK de Azure es el emulador de proceso de Azure, que le permite probar su aplicación localmente. El emulador de proceso estimula el entorno que ejecutará su aplicación cuando se implemente en la nube. Realice los siguientes pasos para probar la aplicación en el emulador.

1.  Cierre el Bloc de notas y regrese a la ventana de Windows PowerShell.
  	Escriba el siguiente cmdlet para ejecutar su servicio en el emulador:

        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch

	El parámetro **-Launch** especifica que las herramientas deben abrir automáticamente una ventana del explorador y mostrar la aplicación una vez esté en ejecución en el emulador. Un explorador abre y muestra "Hello World", tal y como se muestra en la siguiente captura de pantalla. De este modo se indica que el servicio se está ejecutando en el emulador de proceso y funciona correctamente.

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  Para detener el emulador de proceso, utilice el comando **Stop-AzureEmulator**:
	
	PS C:\node\helloworld\WebRole1> Stop-AzureEmulator

## Implementación de la aplicación en Azure

	[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]



###<a id="download_publishing_settings"></a>Descarga de la configuración de publicación de Azure

Con el fin de implementar su aplicación en Azure, debe descargar primero la configuración de publicación para su suscripción de Azure. Los siguientes pasos le guían a lo largo del proceso:

1.  Desde la ventana de Windows PowerShell, inicie la página de descarga mediante la ejecución del siguiente cmdlet:

        PS C:\node\helloworld\WebRole1> Get-AzurePublishSettingsFile

	Este cmdlet utilizará su explorador para navegar hasta la página de descarga de la configuración de publicación. Es posible que se le solicite iniciar sesión con una cuenta de Microsoft. En ese caso, utilice una cuenta asociada a su suscripción de Azure.

	Guarde el perfil descargado en un ubicación de archivo a la que pueda tener acceso fácilmente.

2.  En la ventana de Azure PowerShell, utilice el siguiente cmdlet para configurar el Windows PowerShell para los cmdlet de Node.js con el objetivo de utilizar el perfil de publicación de Azure que ha descargado:

        PS C:\node\helloworld\WebRole1> Import-AzurePublishSettingsFile [path to file]


	<div class="dev-callout">
	<b>Nota:</b>
	<p>Después de importar la configuración de publicación, podría ser conveniente eliminar el archivo .publishSettings descargado dado que contiene información que otros usuarios podrían emplear para tener acceso a su cuenta.</p>
	</div>
    

### Publicación de la aplicación

1.  Publique la aplicación con el cmdlet **Publish-AzureServiceProject**, tal y como se muestra a continuación.

        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

	- El parámetro **servicename** especifica el nombre usado en esta implementación. Este nombre debe ser único porque, de lo contrario, se producirá un error en el proceso de publicación.

	- El parámetro **location** especifica el centro de datos en el que se hospedará la aplicación. Para ver una lista de los centros de datos disponibles, utilice el cmdlet **Get-AzureLocation**.

	- El parámetro **launch** iniciará el explorador y navegará hasta el servicio hospedado una vez completada la implementación.

	Después de que finalice satisfactoriamente el proceso de publicación, verá una respuesta similar a la siguiente:

	![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

	El cmdlet **Publish-AzureServiceProject** realiza los siguientes pasos:

1.  Crea un paquete que se implementará en Azure. El paquete contiene todos los archivos en la carpeta de la aplicación Node.js.

2.  Crea una nueva **cuenta de almacenamiento** si no hay ninguna disponible. La cuenta de almacenamiento de Azure se utiliza para almacenar el paquete de la aplicación durante la implementación. Una vez finalizada la implementación, puede eliminar de forma segura la cuenta de almacenamiento.

3.  Crea un nuevo **servicio en la nube** si todavía no hay ninguno disponible. Un **servicio en la nube** es el contenedor en el que su aplicación se hospeda cuando se implementa en Azure. Para obtener más información, consulte [Overview of Creating a Hosted Service for Azure][].

4.  Publica el paquete de implementación en Azure.


	> [WACOM.NOTE]
	> La implementación de la aplicación puede durar entre 5 y 7 minutos y la aplicación estará disponible cuando se publique.

	Una vez finalizada la implementación, se abrirá una ventana del explorador y navegará hasta el servicio en la nube.


	![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

	La aplicación ya se está ejecutando en Azure.


## Detención y eliminación de su aplicación

Después de implementar su aplicación, es posible que desee deshabilitarla para evitar costes adicionales. Azure factura las instancias de rol web por hora consumida de tiempo de servidor. El tiempo de servidor se empieza a consumir una vez implementada su aplicación, incluso si las instancias no se están ejecutando y se encuentran detenidas.

1.  En la ventana de Windows PowerShell, detenga la implementación del servicio creado en la sección anterior con el siguiente cmdlet:

        PS C:\node\helloworld\WebRole1> Stop-AzureService

	La detención del servicio puede durar varios minutos. Una vez detenido el servicio, recibirá un mensaje que le avisará de su detención.

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Para eliminar el servicio, llame al siguiente cmdlet:

        PS C:\node\helloworld\WebRole1> Remove-AzureService

	Cuando se le solicite, escriba **Y** para eliminar el servicio.

	La eliminación del servicio puede durar varios minutos. Una vez eliminado el servicio, recibirá un mensaje que le avisará de su eliminación.

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	<div class="dev-callout">
	<strong>Nota:</strong>
	<p>La eliminación del servicio no elimina la cuenta de almacenamiento que se creó al publicar por primera vez el servicio, por lo que se le seguirá facturando por el almacenamiento utilizado. Para obtener más información sobre
la eliminación de cuentas de almacenamiento, consulte <a href="http://msdn.microsoft.com/es-es/library/windowsazure/hh531562.aspx">Cómo eliminar una cuenta de almacenamiento de una suscripción de Azure</a>.</p>
</div>


[Menú Inicio de Windows con la entrada de Azure SDK Node.js expandida]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[Lista de directorios de la carpeta helloworld.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Información general de la creación de un servicio hospedado para Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/jj155995.aspx
[Lista de directorios de la carpeta WebRole1]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[Menú que se muestra al hacer clic con el botón secundario en el emulador de Azure desde la barra de tareas.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[Ventana del explorador que muestra la dirección http://www.windowsazure.com/ con el vínculo de versión de prueba gratuita resaltado]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[Ventana del explorador que muestra la página de inicio de sesión de liveID]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Ventana de Internet Explorer que muestra el cuadro de diálogo Guardar como para el archivo publishSettings.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[Resultado de estado completo del comando Publish-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Cómo eliminar una cuenta de almacenamiento de una suscripción de Azure]: https://www.windowsazure.com/es-es/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png

<!--HONumber=35.2-->
