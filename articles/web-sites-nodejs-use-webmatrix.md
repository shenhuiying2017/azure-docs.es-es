<properties linkid="web-site-with-webmatrix" urlDisplayName="Website with WebMatrix" pageTitle="Node.js website with WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that teaches you how to WebMatrix to develop and deploy a Node.js application to an Azure website." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure using WebMatrix" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Compilación e implementación de un sitio web Node.js en Azure con WebMatrix

En este tutorial se muestra cómo usar WebMatrix para desarrollar e implementar una aplicación Node.js en un sitio web de Azure. WebMatrix es una herramienta gratuita de desarrollo web de Microsoft que incluye todo lo que necesita para el desarrollo del sitio web. WebMatrix incluye varias características que facilitan el uso de Node.js, incluida la finalización de código, plantillas pregeneradas y compatibilidad del editor para Jade, LESS y CoffeeScript. Obtenga más información sobre [WebMatrix for Azure][WebMatrix for Azure].

Una vez completada esta guía, tendrá un sitio web Node.js que se ejecuta en Azure.

A continuación se muestra una captura de pantalla de la aplicación completada:

![Sitio web node de Azure][Sitio web node de Azure]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Inicio de sesión en Azure

Siga estos pasos para crear un sitio web de Azure.

<div class="dev-callout"><strong>Nota:</strong>
<p>Para completar este tutorial, necesita una cuenta de Azure que tenga habilitada la caracter&iacute;stica Sitios web Azure.</p>
<p>En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p>
</div>

1.  Inicie WebMatrix
2.  Si esta es la primera vez que utiliza WebMatrix, se le solicitará que inicie sesión en Azure. De lo contrario, puede hacer clic en el botón **Sign In** y seleccionar **Add Account**. Seleccione **Sign in** utilizando su cuenta de Microsoft.

    ![Incorporación de cuenta][Incorporación de cuenta]

3.  Si se registró para obtener una cuenta de Azure, puede iniciar sesión con su cuenta de Microsoft:

    ![Inicio de sesión en Azure][Inicio de sesión en Azure]

## Creación de un sitio con una plantilla integrada para Azure

1.  En la pantalla de inicio, haga clic en el botón **New** y seleccione **Template Gallery** para crear un sitio nuevo a partir de la Galería de plantillas:

    ![Sitio nuevo desde la galería de plantillas][Sitio nuevo desde la galería de plantillas]

2.  En el cuadro de diálogo **Site from Template**, seleccione **Node** y, a continuación, seleccione **Express Site**. Por último, haga clic en **Next**. Si le falta algún requisito previo para la plantilla **Express Site**, se pedirá que lo instale.

    ![Selección plantilla Express][Selección plantilla Express]

3.  Si inició sesión en Azure, ahora tiene la opción de crear un sitio web de Azure para su sitio local. Elija un nombre único y seleccione el centro de datos donde desea crear su sitio:

    ![Creación de sitio en Azure][Creación de sitio en Azure]

4.  Después de que WebMatrix finaliza la creación del sitio web, se muestra IDE de WebMatrix.

    ![IDE de Webmatrix][IDE de Webmatrix]

## Publicación de su aplicación en Azure

1.  En WebMatrix, haga clic en **Publish** desde la cinta **Home** para ver el cuadro de diálogo **Publish Preview** para el sitio web.

    ![Vista previa de publicación][Vista previa de publicación]

2.  Haga clic en **Continue**. Cuando se completa la publicación, la dirección URL del sitio web en Azure se muestra en la parte inferior del WebMatrix IDE.

    ![Publicación completa][Publicación completa]

3.  Haga clic en el vínculo para abrir el sitio web en su explorador.

    ![Sitio web Express][Sitio web Express]

## Modificación y nueva publicación de la aplicación

Puede modificar y volver a publicar su aplicación fácilmente. En este caso, realizará un cambio simple en el título del archivo **index.jade** y volverá a publicar la aplicación.

1.  En WebMatrix, seleccione **Files** y, a continuación, expanda la carpeta **views**. Abra el archivo **index.jade** haciendo doble clic en él.

    ![Visualización de index.jade de webmatrix][Visualización de index.jade de webmatrix]

2.  Cambie la segunda línea a lo siguiente:

        p Welcome to #{title} with WebMatrix on Azure!

3.  Guarde los cambios y, a continuación, haga clic en el icono de publicación. Finalmente, haga clic en **Continue** en el cuadro de diálogo **Publish Preview** y espere a que la actualización se publique.

    ![Vista previa de publicación][1]

4.  Cuando se haya completado la publicación, use el vínculo que recibió cuando se completó el proceso de publicación para ver el sitio actualizado.

    ![Sitio web node de Azure][Sitio web node de Azure]

## Pasos siguientes

Para obtener más información acerca de las versiones de Node.js que se proporcionan con Azure y la forma de especificar la versión que se debe utilizar con su aplicación, consulte [Especificación de una versión de Node.js en una aplicación Azure][Especificación de una versión de Node.js en una aplicación Azure].

Si tiene problemas con su aplicación después de la implementación en Azure, consulte [Cómo depurar una aplicación Node.js en Sitios web Azure][Cómo depurar una aplicación Node.js en Sitios web Azure] para obtener información acerca del diagnóstico del problema.

  [WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Sitio web node de Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
  [Incorporación de cuenta]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
  [Inicio de sesión en Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
  [Sitio nuevo desde la galería de plantillas]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
  [Selección plantilla Express]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png
  [Creación de sitio en Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
  [IDE de Webmatrix]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
  [Vista previa de publicación]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png
  [Publicación completa]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
  [Sitio web Express]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
  [Visualización de index.jade de webmatrix]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
  [1]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
  [Especificación de una versión de Node.js en una aplicación Azure]: /es-es/documentation/articles/nodejs-specify-node-version-azure-apps/
  [Cómo depurar una aplicación Node.js en Sitios web Azure]: http://www.windowsazure.com/es-es/develop/nodejs/how-to-guides/Debug-Website/
