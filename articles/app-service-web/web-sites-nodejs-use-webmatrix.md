---
title: "Creación e implementación de una aplicación web Node.js en Azure con WebMatrix"
description: "Un tutorial que le enseña a usar WebMatrix para desarrollar una aplicación Node.js e implementarla en aplicaciones web del Servicio de aplicaciones de Azure."
services: app-service\web
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 42986058-57b8-42ea-af76-d6c6ba508608
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 54ae907409bd860f26216e85e52932bc4bd825c1


---
# <a name="build-and-deploy-a-nodejs-web-app-to-azure-using-webmatrix"></a>Creación e implementación de una aplicación web Node.js en Azure con WebMatrix
Este tutorial muestra la forma de usar WebMatrix para desarrollar una aplicación node.js e implementarla en aplicaciones web del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) . WebMatrix es una herramienta gratuita de desarrollo web de Microsoft que incluye todo lo que se necesita para el desarrollo de sitios o aplicaciones web. WebMatrix incluye varias características que facilitan el uso de Node.js, incluida la finalización de código, plantillas pregeneradas y compatibilidad del editor para Jade, LESS y CoffeeScript. Obtenga más información sobre [WebMatrix](https://www.microsoft.com/web/webmatrix/).

Una vez completada esta guía, dispondrá una aplicación web Node.js que se ejecuta en el Servicio de aplicaciones de Azure.

A continuación se muestra una captura de pantalla de la aplicación completada:

![Sitio web node de Azure][webmatrix-node-completed]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](https://azure.microsoft.com/try/app-service/), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

## <a name="sign-into-azure"></a>Inicio de sesión en Azure
Siga estos pasos para crear una aplicación web en el Servicio de aplicaciones de Azure.

1. Inicie WebMatrix
2. Si esta es la primera vez que utiliza WebMatrix, se le solicitará que inicie sesión en Azure.  De lo contrario, puede hacer clic en el botón **Iniciar sesión** y seleccionar **Agregar cuenta**.  Seleccione **Sign in** utilizando su cuenta de Microsoft.
   
    ![Add Account][addaccount]
3. Si se registró para obtener una cuenta de Azure, puede iniciar sesión con su cuenta de Microsoft:
   
    ![Inicio de sesión en Azure][signin]    

## <a name="create-a-site-using-a-built-in-template-for-azure"></a>Creación de un sitio con una plantilla integrada para Azure
1. En la pantalla de inicio, haga clic en el botón **Nuevo** y seleccione **Galería de plantillas** para crear un sitio nuevo a partir de la Galería de plantillas:
   
    ![Sitio nuevo desde la galería de plantillas][sitefromtemplate]
2. En el cuadro de diálogo **Sitio desde plantilla**, seleccione **Nodo** y, a continuación, **Sitio rápido**. Finalmente, haga clic en **Siguiente**. Si falta algún requisito previo para la plantilla **Express Site** , se pedirá que lo instale.
   
    ![Selección plantilla Express][webmatrix-templates]
3. Si inició sesión en Azure, ahora tiene la opción de crear una aplicación web en el Servicio de aplicaciones para su sitio local.  Elija un nombre único y seleccione el centro de datos donde desea crear la aplicación web del Servicio de aplicaciones: 
   
    ![Creación de sitio en Azure][nodesitefromtemplateazure]
4. Una vez que WebMatrix finaliza la creación del sitio local y de la aplicación web del Servicio de aplicaciones, se muestra el IDE de WebMatrix.
   
    ![IDE de Webmatrix][webmatrix-ide]

## <a name="publish-your-application-to-azure"></a>Publicación de su aplicación en Azure
1. En WebMatrix, haga clic en **Publicar** en la cinta **Inicio** para ver el cuadro de diálogo **Vista previa de publicación** del sitio web.
   
    ![Publish Preview][webmatrix-node-publishpreview]
2. Haga clic en **Continue**. Cuando se completa la publicación, la dirección URL de la aplicación web del Servicio de aplicaciones se muestra en la parte inferior del IDE de WebMatrix.
   
    ![Publicación completa][webmatrix-publish-complete]
3. Haga clic en el vínculo para abrir la aplicación web del Servicio de aplicaciones en su explorador.
   
    ![Aplicación web Express][webmatrix-node-express-site]

## <a name="modify-and-republish-your-application"></a>Modificación y nueva publicación de la aplicación
Puede modificar y volver a publicar su aplicación fácilmente. En este caso, realizará un cambio sencillo en el título del archivo **index.jade** y volverá a publicar la aplicación.

1. En WebMatrix, seleccione **Archivos** y, a continuación, expanda la carpeta de **vistas**. Abra el archivo **index.jade** haciendo doble clic en él.
   
    ![Visualización de index.jade de webmatrix][webmatrix-modify-index]
2. Cambie la línea del párrafo a lo siguiente:
   
        p Welcome to #{title} with WebMatrix on Azure!
3. Guarde los cambios y, a continuación, haga clic en el icono de publicación. Finalmente, haga clic en **Continue** in the **Publish Preview** y espere a que la actualización se publique.
   
    ![Publish Preview][webmatrix-republish]
4. Cuando se haya completado la publicación, use el vínculo que recibió cuando finalizó el proceso de publicación para ver actualizada la aplicación web del Servicio de aplicaciones.
   
    ![Aplicación web node de Azure][webmatrix-node-completed]

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de las versiones de Node.js que se proporcionan con Azure y la forma de especificar la versión que se debe utilizar con su aplicación, consulte [Especificación de una versión de Node.js en una aplicación Azure](../nodejs-specify-node-version-azure-apps.md).

Si tiene problemas con la aplicación después de la implementación en Azure, consulte [Cómo depurar una aplicación web Node.js en los Servicios de aplicaciones de Azure](web-sites-nodejs-debug.md) para obtener información acerca del diagnóstico del problema.

## <a name="whats-changed"></a>Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png



<!--HONumber=Jan17_HO3-->


