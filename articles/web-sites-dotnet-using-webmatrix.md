<properties urlDisplayName="Website with WebMatrix" pageTitle="Sitio web de .NET con WebMatrix - Tutoriales de Azure" metaKeywords="WebMatrix Azure, WebMatrix Azure, sitio web de Azure WebMatrix, Azure sitio web WebMatrix, Web Matrix Azure, WebMatrix Azure" description="Learn how to develop and deploy an Azure website with WebMatrix." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Develop and deploy a website with Microsoft WebMatrix" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/27/2014" ms.author="tomfitz" />





#Desarrollo e implementación de un sitio web con Microsoft WebMatrix
En esta guía se describe cómo usar Microsoft WebMatrix para crear e implementar un sitio web en Azure.  Utilizará una aplicación de muestra de una plantilla del sitio de WebMatrix.

Aprenderá a:

* Iniciar sesión en Azure desde WebMatrix
* Crear un sitio mediante una plantilla integrada con WebMatrix 
* Implementar el sitio web personalizado directamente desde WebMatrix hasta Azure.


[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Inicio de sesión en Azure

1. Inicie WebMatrix.
2. Si esta es la primera vez que utiliza WebMatrix 3, se le solicitará que inicie sesión en Azure.  De lo contrario, puede hacer clic en el botón **Sign In** y seleccionar **Add Account**. Seleccione **Iniciar sesión** con su cuenta Microsoft.

	![Add Account][addaccount]

3. Si se registró para obtener una cuenta de Azure, puede iniciar sesión con su cuenta de Microsoft:

	![Sign into Azure][signin]	


## Creación de un sitio con una plantilla integrada para Azure

1. En la pantalla de inicio, haga clic en el botón **Nuevo** y seleccione **Galería de plantillas** para crear un sitio nuevo a partir de la Galería de plantillas:

	![New site from Template Gallery][sitefromtemplate]

2. La Galería de plantillas mostrará una lista de plantillas disponibles que se pueden ejecutar de manera local o en Azure.  Seleccione **Panadería** en la lista de plantillas, escriba **bakerysample** para **Nombre del sitio** y haga clic en **Siguiente**.

	![Create Site from Template][sitefromtemplatedetails]

3. Si inició sesión en Azure, ahora tiene la opción de crear un sitio web de Azure para su sitio local.  Elija un nombre único y seleccione el centro de datos donde desea crear su sitio: 

	![Create site on Azure][sitefromtemplateazure]

	Después de que WebMatrix finaliza la creación del sitio web, se muestra el IDE de WebMatrix:

	![WebMatrix IDE][howtowebmatrixide] 

## Configuración del correo electrónico

El ejemplo de panadería incluye un formulario de pedido simulado que envía un mensaje de correo electrónico con el producto pedido. Utilizará el servicio de correo electrónico SendGrid en Azure para enviar correos electrónicos desde su sitio.

1. Siga los pasos del tutorial [Envío de correos electrónicos usando SendGrid con Azure][sendgridexample] para configurar una cuenta de SendGrid y recuperar la información de conexión. No es necesario realizar el tutorial completo, solo hasta el punto de obtener la información de conexión.

2. Agregue el paquete NuGet de SendGrid al proyecto WebMatrix. En primer lugar, haga clic en el botón NuGet.

    ![Add SendGrid][addsendgrid]

    Busque SendGrid e instálelo.

    ![Install SendGrid][installsendgrid]

    Después de que el paquete ha terminado de instalarse, tenga en cuenta que los ensamblados de SendGrid se han agregado a la papelera.

    ![SendGrid added][binsendgrid]

3. Abra la página *Order.cshtml* haciendo doble clic en el nombre del archivo.

	![][modify2]

4. Al principio del archivo, agregue el siguiente código:

        @using SendGrid;
        @using System.Net.Mail;

4. Busque el comentario que dice //SMTP Configuration for Hotmail y elimine o comente todo el código para usar el correo electrónico Web.

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


5. Agregue código para usar SendGrid para enviar correos electrónicos en lugar del correo electrónico web. Agregue el código siguiente en lugar del código que se eliminó en el paso anterior.

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

7. Haga clic en **Pedir ahora** en uno de los productos y envíese un pedido.

8. Compruebe su correo electrónico y asegúrese de que recibió la confirmación del pedido. Si tiene dificultades para enviar correo electrónico, consulte la sección de [problemas con el envío de correo electrónico][sendmailissues] (en inglés) en la Guía de solución de problemas de las páginas web de ASP.NET (Razor).
 

## Implementación del sitio web personalizado de WebMatrix a Azure

1. En WebMatrix, haga clic en **Publicar** desde la cinta **Página principal** para ver el cuadro de diálogo **Publicar vista previa** para el sitio web.

	![WebMatrix Publish Preview][howtopublishpreview]

2. Haga clic para activar la casilla junto a bakery.sdf y, a continuación, haga clic en **Continuar**.  Cuando se completa la publicación, la dirección URL del sitio web actualizado en Azure se muestra en la parte inferior del IDE de WebMatrix.  

	![Publishing Complete][publishcomplete]

3. Haga clic en el vínculo para abrir el sitio web en su explorador:

	![Bakery Sample Site][bakerysample]

	La dirección URL del sitio web también se puede encontrar en el portal de Azure al hacer clic en **Sitios web** para mostrar todos los sitios web de su suscripción. La dirección URL de cada sitio web se muestra en la columna URL en la página de sitios web.

## Modificación del sitio web y nueva publicación en el sitio web de Azure

Puede usar WebMatrix para modificar el sitio y volver a publicarlo en su sitio web de Azure. En el siguiente procedimiento agregará una casilla para indicar que el pedido es un regalo.

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

6. Vuelva a publicar el sitio haciendo clic en **Publicar** en la cinta **Página principal**.

7. En el cuadro de diálogo **Publicar vista previa**, asegúrese de que ambos Order.cshtml estén marcados y haga clic para continuar.

8. Haga clic en el vínculo para abrir el sitio web en su explorador y pruebe la actualización en su sitio web de Azure.

# Pasos siguientes

Ha aprendido a crear e implementar un sitio web de WebMatrix en Azure. Para obtener más información sobre WebMatrix, consulte estos recursos:

* [WebMatrix for Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Sitio web de WebMatrix (en inglés)](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





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
[sendgridexample]: http://azure.microsoft.com/es-es/documentation/articles/sendgrid-dotnet-how-to-send-email/
