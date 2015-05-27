<properties 
	pageTitle="Desarrollo e implementación de una aplicación web con Microsoft WebMatrix" 
	description="Aprenda a desarrollar e implementar una aplicación web ASP.NET en las aplicaciones web de Servicio de aplicaciones de Azure con Microsoft WebMatrix." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="tomfitz"/>


# Desarrollo e implementación de una aplicación web con Microsoft WebMatrix

## Información general

En esta guía se describe cómo usar Microsoft WebMatrix para crear e implementar un sitio web de .NET en aplicaciones web de [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Utilizará una aplicación de muestra de una plantilla del sitio de WebMatrix.

Aprenderá a:

* Iniciar sesión en Azure desde WebMatrix
* Crear un sitio mediante una plantilla integrada con WebMatrix 
* Implementar el sitio web personalizado directamente desde WebMatrix hasta Azure.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Inicio de sesión en Azure

1. Inicie WebMatrix.
2. Si esta es la primera vez que utiliza WebMatrix 3, se le solicitará que inicie sesión en Azure. De lo contrario, puede hacer clic en el botón **Iniciar sesión** y seleccionar **Agregar cuenta** Seleccione **Iniciar sesión** con su cuenta Microsoft.

	![Add account][addaccount]

3. Si se registró para obtener una cuenta de Azure, puede iniciar sesión con su cuenta de Microsoft:

	![Inicio de sesión en Azure][signin]


## Creación de un sitio con una plantilla integrada para Azure

1. En la pantalla de inicio, haga clic en el botón **Nuevo** y seleccione **Galería de plantillas** para crear un sitio nuevo a partir de la Galería de plantillas:

	![Sitio nuevo desde la galería de plantillas][sitefromtemplate]

2. La Galería de plantillas mostrará una lista de plantillas disponibles que se pueden ejecutar de manera local o en Azure. Seleccione **Bakery** en la lista de plantillas, escriba **bakerysample** para **Nombre de sitio** y haga clic en **Siguiente**.

	![Crear un sitio a partir de una plantilla][sitefromtemplatedetails]

3. Si inició sesión en Azure, ahora tiene la opción de crear una instancia de Aplicaciones web del Sitio de aplicaciones para su sitio local. Elija un nombre único y seleccione el centro de datos donde desea crear su instancia de aplicaciones web:

	![Creación de sitio en Azure][sitefromtemplateazure]

	Una vez que WebMatrix finaliza la creación del sitio local y la instancia de aplicaciones web en Azure, se muestra el IDE de WebMatrix.

	![IDE de WebMatrix][howtowebmatrixide]

## Configurar el correo electrónico

El ejemplo de la panadería incluye un formulario de pedido simulado que envía un mensaje de correo electrónico con el producto pedido. Utilizará el servicio de correo electrónico SendGrid en Azure para enviar correos electrónicos desde su sitio.

1. Siga los pasos del tutorial sobre [cómo enviar correo electrónico con SendGrid y Azure][sendgridexample] para configurar una cuenta de SendGrid y recuperar la información de conexión. No es necesario realizar el tutorial completo; solo hasta el punto para obtener información de conexión.

2. Agregue el paquete NuGet de SendGrid al proyecto de WebMatrix. En primer lugar, haga clic en el botón de NuGet.

    ![Adición de SendGrid][addsendgrid]

    Busque SendGrid e instálelo.

    ![Instalación de SendGrid][installsendgrid]

    Una vez instalado el paquete, tenga en cuenta que los ensamblados de SendGrid se han agregado a la Papelera.

    ![SendGrid agregado][binsendgrid]

3. Abra la página *Order.cshtml* haciendo doble clic en el nombre del archivo.

	![][modify2]

4. Al principio del archivo, agregue el código siguiente:

        @using SendGrid;
        @using System.Net.Mail;

4. Busque el comentario que dice //Configuración SMTP para Hotmail y elimine o comente todo el código para usar el correo electrónico web.

        /*
        //SMTP Configuration for Hotmail
        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort = 25;
        WebMail.EnableSsl = true;

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

        if (WebMail.UserName.IsEmpty() || WebMail.Password.IsEmpty() || WebMail.From.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        } 
        else {
            try {
                WebMail.Send(to: customerEmail, subject: "Fourth Coffee - New Order", body: body);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }*/


5. Agregue código para usar SendGrid para enviar correos electrónicos en lugar de correo electrónico web. Agregue el código siguiente en lugar del código que ha eliminado en el paso anterior.

		 if (email.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        }
        else {
            // Create the email object first, then add the properties.
            SendGridMessage myMessage = new SendGridMessage();
            myMessage.AddTo(email);
            myMessage.From = new MailAddress("FourthCoffee@example.com", "Fourth Coffee");
            myMessage.Subject = "Fourth Coffee - New Order";
            myMessage.Text = body;

            // Create credentials, specifying your user name and password.
            var credentials = new NetworkCredential("[your user name", "[your password]");

            // Create an Web transport for sending email.
            var transportWeb = new Web(credentials);

            // Send the email.
            try {
                transportWeb.Deliver(myMessage);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }


6. En la cinta de WebMatrix, haga clic en **Ejecutar** para probar el sitio.

	![][modify4]

7. Haga clic en **Solicitar ahora** en uno de los productos y envíese un pedido.

8. Compruebe su correo electrónico y asegúrese de que recibió la confirmación del pedido. Si tiene dificultades para enviar correo electrónico, consulte la sección de [problemas con el envío de correo electrónico][sendmailissues] en la Guía de solución de problemas de las páginas web de ASP.NET (Razor).
 

## Implementación del sitio web personalizado de WebMatrix a Azure

1. En WebMatrix, haga clic en **Publicar** desde la cinta **Inicio** para ver el cuadro de diálogo **Vista previa de publicación** para el sitio web.

	![WebMatrix Publish Preview][howtopublishpreview]

2. Haga clic para activar la casilla junto a bakery.sdf y, a continuación, haga clic en **Continuar**. Cuando se completa la publicación, la dirección URL de la aplicación web actualizada en Servicio de aplicaciones de Azure se muestra en la parte inferior del IDE de WebMatrix.

	![Publicación completa][publishcomplete]

3. Haga clic en el vínculo para abrir el sitio web (una instancia de aplicaciones web en Azure) en su explorador:

	![Sitio de muestra de panadería][bakerysample]

	La dirección URL de la instancia de aplicaciones web también puede encontrarse en el [Portal Azure](https://portal.azure.com) haciendo clic en **Examinar** > **Aplicaciones web** para mostrar todas las instancias de aplicaciones web para la suscripción. A continuación, seleccione una aplicación web. La dirección URL de la aplicación web se muestra en la hoja de de la aplicación web.

## Modificación del sitio web y nueva publicación en las aplicaciones web

Puede usar WebMatrix para modificar el sitio y volver a publicarlo en su instancia de aplicaciones web. En el siguiente procedimiento agregará una casilla para indicar que el pedido es un regalo.

1. Abra la página *Order.cshtml*.

2. Localice la definición de la forma de clase de "envío". Inserte el siguiente código justo después del bloque &lt;li&gt;.
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. Localice la línea "var shipping = Request["orderShipping"];" en el archivo e inserte la siguiente línea de código inmediatamente después.

		var gift = Request["isGift"];

4. Justo después del bloque de código que valida que la dirección de envío no está vacía en el siguiente fragmento de código.

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	Su archivo *order.cshtml* debe parecerse a la siguiente imagen.

	![][modify6]

5. Guarde el archivo y ejecute el sitio de manera local y envíese un pedido de prueba. Asegúrese de probar la casilla nueva.

	![][modify7]

6. Vuelva a publicar el sitio haciendo clic en **Publicar** en la cinta **Inicio**.

7. En el cuadro de diálogo **Vista previa de publicación**, asegúrese de que ambos Order.cshtml estén marcados y haga clic para continuar.

8. Haga clic en el vínculo para abrir el sitio web en su explorador y pruebe la actualización en su instancia de aplicaciones web.

## Pasos siguientes

* [Sitio web de WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)




[howtowebmatrixide]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide2.png
[howtopublishpreview]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
[addsendgrid]: ./media/web-sites-dotnet-using-webmatrix/addsendgridpackage.png
[installsendgrid]: ./media/web-sites-dotnet-using-webmatrix/installsendgrid.png
[binsendgrid]: ./media/web-sites-dotnet-using-webmatrix/sendgridbin.png

[publishcomplete]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
[bakerysample]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
[addaccount]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
[sitefromtemplatedetails]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
[sitefromtemplateazure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png

[modify1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
[modify2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
[modify3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
[modify4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
[modify5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
[modify6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
[modify7]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png



[sendmailissues]: http://go.microsoft.com/fwlink/?LinkId=253001#email
[sendgridexample]: http://azure.microsoft.com/documentation/articles/sendgrid-dotnet-how-to-send-email/

<!--HONumber=54-->