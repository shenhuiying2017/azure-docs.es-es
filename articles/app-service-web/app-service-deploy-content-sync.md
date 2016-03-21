<properties
	pageTitle="Sincronización de contenido de una carpeta de nube al Servicio de aplicaciones de Azure"
	description="Descubra cómo implementar su aplicación en el Servicio de aplicaciones de Azure mediante una sincronización de contenido desde una carpeta de la nube."
	services="app-service"
	documentationCenter=""
	authors="dariac"
	manager="yochayk"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="dariac"/>
    
# Sincronización de contenido de una carpeta de nube al Servicio de aplicaciones de Azure

Una de las opciones de implementación en el [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) consiste en sincronizar su contenido desde servicios de almacenamiento en nube populares, como Dropbox y OneDrive.

## <a name="overview"></a>Información general sobre la implementación de la sincronización de contenido

La implementación de la sincronización de contenido a petición funciona con el [motor de implementación Kudu](https://github.com/projectkudu/kudu/wiki) integrado con el Servicio de aplicaciones. En el [Portal de Azure](https://portal.azure.com), puede designar una carpeta especial en el almacenamiento en la nube, trabajar con el código de la aplicación y el contenido de dicha carpeta, y sincronizar con el Servicio de aplicaciones con solo hacer clic en un botón. La sincronización de contenido utiliza el proceso de Kudu para la compilación e implementación.
    
## <a name="contentsync"></a>Habilitación de la implementación de la sincronización de contenido
Para habilitar la sincronización de contenido desde el Portal de Azure, siga estos pasos:

1. Desplácese hasta la hoja **Configuración** en el contexto de la aplicación y seleccione la opción **Implementación continua**.

    ![Sincronización de contenido](./media/app-service-deploy-content-sync/continous_deployment.png)

2. Seleccione **OneDrive** o **Dropbox** como origen para la implementación.

    ![Origen de la implementación](./media/app-service-deploy-content-sync/onedrive.png)

3. Complete el flujo de trabajo de autorización para permitir que el Servicio de aplicaciones acceda a una ruta designada y predefinida específica para OneDrive o Dropbox en la que se almacenará todo su contenido del Servicio de aplicaciones. Tras la autorización, la plataforma Servicio de aplicaciones le ofrecerá la opción de crear una carpeta de contenido en la ruta de contenido designada o de elegir una carpeta existente en dicha ruta. Las rutas de contenido designadas en sus cuentas de almacenamiento en nube utilizadas para la sincronización del Servicio de aplicaciones son las siguientes:
    * **OneDrive**: `Apps\Azure Web Apps` 
    * **Dropbox**: `Dropbox\Apps\Azure`

4. Después de la sincronización de contenido inicial, se puede iniciar la sincronización a petición desde el Portal de Azure. El historial de implementación está disponible en la hoja **Implementaciones**.

    ![Historial de implementaciones](./media/app-service-deploy-content-sync/onedrive_sync.png)
 
Puede obtener más información sobre la implementación de Dropbox en [Deploy from Dropbox (Implementación desde Dropbox)](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx).

<!---HONumber=AcomDC_0309_2016-->