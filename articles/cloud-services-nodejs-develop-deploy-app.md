<properties 
	pageTitle="Guía de introducción a Node.js - Tutorial de Azure" 
	description="Aprenda a crear una sencilla aplicación web de Node.js e impleméntela en un servicio en la nube de Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="hero-article" 
	ms.date="02/24/2015" 
	ms.author="mwasson"/>


# Compilación e implementación de una aplicación Node.js en un Servicio en la nube de Azure

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

En este tutorial se muestra cómo crear una aplicación de Node.js simple con Servicio en la nube de Azure. Los Servicios en la nube son los bloques de compilación de aplicaciones en la nube escalables en Azure. Permiten la separación y la administración independiente de los componentes front-end y back-end de su aplicación, así como su escalación horizontal.  Los Servicios en la nube proporcionan una máquina virtual dedicada y robusta para hospedar cada rol de forma fiable.

Para obtener más información sobre los Servicios en la nube y sobre su comparación con Sitios web Azure y Máquinas virtuales, consulte [Comparación entre Sitios web Azure, Servicios en la nube y Máquinas virtuales](http://azure.microsoft.com/documentation/articles/choose-web-site-cloud-service-vm/).

>[AZURE.TIP] ¿Desea compilar un sitio web sencillo? Si su escenario tan solo requiere un sitio web de front-end sencillo, considere <a href="/documentation/articles/web-sites-nodejs-develop-deploy-mac/">el uso de un sitio web de Azure ligero.</a> Puede actualizar a un Servicio en la nube más adelante, cuando su sitio web sea más grande y sus requisitos cambien.


Siguiendo este tutorial, podrá compilar una aplicación web sencilla hospedada en un rol web. Utilizará el emulador de proceso para probar su aplicación localmente y, a continuación, la implementará con las herramientas de línea de comandos de PowerShell.

La aplicación es una aplicación sencilla de "Hola a todos":

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="Una ventana del explorador que muestra la página Hola a todos. La dirección URL indica que la página está hospedada en Azure.">
</p>

## Requisitos previos

> [AZURE.NOTE] Este tutorial usa PowerShell de Azure, que requiere Windows.

- Instale el SDK de Azure para Node.js: <a href="http://go.microsoft.com/fwlink/?LinkId=254279">Windows Installer</a> 

- Instale y configure [Azure Powershell](install-configure-powershell.md).


## Cree un proyecto del servicio en la nube de Azure

Realice las siguientes tareas para crear un nuevo proyecto de Servicio en la nube de Azure, junto con scaffolding básico de Node.js:


1. Ejecute **PowerShell de Azure** como administrador. Desde el **menú Inicio** o la **pantalla Inicio**, busque **PowerShell de Azure**.

2.  Escriba el siguiente cmdlet de PowerShell para crear el proyecto:

        New-AzureServiceProject helloworld

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	El cmdlet de **New-AzureServiceProject** genera una estructura básica para publicar una aplicación Node.js en un servicio en la nube. Contiene archivos de configuración necesarios para la publicación en Azure. El cmdlet también cambia su directorio de trabajo al directorio del servicio.

	El cmdlet crea los siguientes archivos:

	-   **ServiceConfiguration.Cloud.cscfg**,
        **ServiceConfiguration.Local.cscfg** and **ServiceDefinition.csdef**: 
        Archivos específicos de Azure necesarios para publicar su aplicación.
		. Para obtener más información, consulte
        [Información general de la creación de un servicio hospedado para Azure][].

	-   **deploymentSettings.json**: Stores local settings that are used by
        the Azure PowerShell deployment cmdlets.

4.  Escriba el siguiente comando para agregar un rol web nuevo:

        Add-AzureNodeWebRole
	
	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	El cmdlet de **Add-AzureNodeWebRole** crea una aplicación de Node.js básica. También modifica los archivos **.csfg** y **.csdef** para agregar entradas de configuración para el nuevo rol.

	> [AZURE.NOTE] Si no especifica un nombre de rol, se usa el nombre predeterminado. Puede proporcionar un nombre como primer parámetro de cmdlet: `Add-AzureNodeWebRole MyRole`


La aplicación Node.js se define en el archivo **server.js**, que se encuentra en el directorio para el rol web (**WebRole1** de forma predeterminada). Este es el código:

	var http = require('http');
	var port = process.env.port || 1337;
	http.createServer(function (req, res) {
	    res.writeHead(200, { 'Content-Type': 'text/plain' });
	    res.end('Hello World\n');
	}).listen(port);

Este código es básicamente el mismo que el ejemplo "Hola a todos" en el sitio web de [nodejs.org][], excepto en que utiliza el número de puerto asignado por el entorno de la nube.


## Ejecución de la aplicación localmente en el emulador

Una de las herramientas que instala el SDK de Azure es el
emulador de proceso de Azure, que le permite probar localmente su aplicación El
emulador de proceso simula el entorno en que se ejecutará su aplicación
cuando se implementa en la nube. 

1.  Escriba el siguiente cmdlet de PowerShell de Azure para ejecutar su servicio en el emulador:

        Start-AzureEmulator -Launch

	Utilice el parámetro **-Launch** para abrir automáticamente una ventana del explorador cuando el rol web se está ejecutando en el emulador. La ventana del explorador debe mostrar "Hola a todos", tal y como aparece en la siguiente captura de pantalla. 

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  Para detener el emulador de proceso, utilice el cmdlet de **Stop-AzureEmulator**:
	
		Stop-AzureEmulator

## Implementación de la aplicación en Azure

	[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]


### Descarga de la configuración de publicación de Azure

Con el fin de implementar su aplicación en Azure, debe descargar primero la configuración de publicación para su suscripción de Azure. 

1.  Ejecute el siguiente cmdlet de PowerShell de Azure:

        Get-AzurePublishSettingsFile

	Este cmdlet utilizará su explorador para navegar hasta la página de descarga de la configuración de publicación. Es posible que se le solicite iniciar sesión con una cuenta de Microsoft. En ese caso, utilice una cuenta asociada a su suscripción de Azure.

	Guarde el perfil descargado en un ubicación de archivo a la que pueda tener acceso fácilmente.

2.  Ejecute el siguiente cmdlet para importar el perfil de publicación que descargó:

        Import-AzurePublishSettingsFile [ruta de acceso al archivo]


	> [AZURE.NOTE] Después de importar la configuración de publicación, considere la posibilidad de eliminar el archivo .publishSettings descargado, ya que contiene información que podría permitir que alguien obtuviera acceso a su cuenta.
    

### Publicación de la aplicación

Para publicar, ejecute el cmdlet de **Publish-AzureServiceProject** de la siguiente forma:

    Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

- **-ServiceName** especifica el nombre de la implementación. Debe ser un nombre exclusivo. De lo contrario, se producirá un error en el proceso de publicación.

- **-Location** especifica el centro de datos en el que se hospeda la aplicación. Para ver una lista de centros de datos disponibles, use el cmdlet de **Get-AzureLocation**.

- **-Launch** abre una ventana del explorador y se dirige al servicio hospedado después de que se haya completado la implementación.

Después de que finalice satisfactoriamente el proceso de publicación, verá una respuesta similar a la siguiente:

![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

> [AZURE.NOTE]
> La implementación de la aplicación puede durar entre 5 y 7 minutos y la aplicación estará disponible cuando se publique.

Una vez finalizada la implementación, se abrirá una ventana del explorador y navegará hasta el servicio en la nube.


![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

La aplicación ya se está ejecutando en Azure.

El cmdlet **Publish-AzureServiceProject** realiza los siguientes pasos:

1.  Crea un paquete de implementación. El paquete contiene todos los archivos en la carpeta de la aplicación.

2.  Crea una nueva **cuenta de almacenamiento** si no hay ninguna disponible. La cuenta de almacenamiento de Azure se utiliza para almacenar el paquete de la aplicación durante la implementación. Una vez finalizada la implementación, puede eliminar de forma segura la cuenta de almacenamiento.

3.  Crea un nuevo **servicio en la nube** si todavía no hay ninguno disponible. Un **servicio en la nube** es el contenedor en el que su aplicación se hospeda cuando se implementa en Azure. Para obtener más información, consulte [Información general de la creación de un servicio hospedado para Azure][].

4.  Publica el paquete de implementación en Azure.



## Detención y eliminación de la aplicación

Después de implementar su aplicación, es posible que desee deshabilitarla para evitar costes adicionales. Azure factura las instancias de rol web por hora consumida de tiempo de servidor. El tiempo de servidor se empieza a consumir una vez implementada su aplicación, incluso si las instancias no se están ejecutando y se encuentran detenidas.

1.  En la ventana de Windows PowerShell, detenga la implementación del servicio creado en la sección anterior con el siguiente cmdlet:

        Stop-AzureService

	La detención del servicio puede durar varios minutos. Una vez detenido el servicio, recibirá un mensaje que le avisará de su detención.

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Para eliminar el servicio, llame al siguiente cmdlet:

        Remove-AzureService

	Cuando se le solicite, escriba **Y** para eliminar el servicio.

	La eliminación del servicio puede durar varios minutos. Una vez eliminado el servicio, recibirá un mensaje que le avisará de su eliminación.

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	> [AZURE.NOTE] La eliminación del servicio no elimina la cuenta de almacenamiento que se creó al publicar por primera vez el servicio, por lo que se le seguirá facturando por el almacenamiento utilizado. Para obtener más información acerca de la eliminación de una cuenta de almacenamiento, consulte [Eliminación de una cuenta de almacenamiento desde una suscripción de Azure](http://msdn.microsoft.com/library/windowsazure/hh531562.aspx).


[Menú Inicio de Windows con la entrada de Azure SDK Node.js expandida]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[Lista de directorios de la carpeta helloworld.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Información general de la creación de un servicio hospedado para Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Lista de directorios de la carpeta WebRole1]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[Menú que se muestra al hacer clic con el botón secundario en el emulador de Azure desde la barra de tareas.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[Ventana del explorador que muestra la dirección http://www.windowsazure.com/ con el vínculo de versión de prueba gratuita resaltado]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[Ventana del explorador que muestra la página de inicio de sesión de liveID]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Ventana de Internet Explorer que muestra el cuadro de diálogo Guardar como para el archivo publishSettings.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[Resultado de estado completo del comando Publish-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Cómo eliminar una cuenta de almacenamiento de una suscripción de Azure]: https://www.windowsazure.com/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png

<!--HONumber=52-->