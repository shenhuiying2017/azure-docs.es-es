<properties
	pageTitle="Cómo comprar un nombre de dominio personalizado en aplicaciones web del servicio de aplicaciones de Azure"
	description="Obtenga información sobre cómo comprar un nombre de dominio personalizado con una aplicación web en el servicio de aplicaciones de Azure."
	services="app-service\web"
	documentationCenter=""
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="robmcm"/>

# Comprar y configurar un nombre de dominio personalizado en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Cuando crea una aplicación web, Azure la asigna a un subdominio de azurewebsites.net. Por ejemplo, si la aplicación web se denomina **contoso**, la dirección URL es **contoso.azurewebsites.net**. Azure también asigna una dirección IP virtual.

Para un sitio web de producción, probablemente quiera que los usuarios vean un nombre de dominio personalizado. En este artículo se explica cómo comprar y configurar un dominio personalizado con [aplicaciones web del servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## Información general

Si no tiene un nombre de dominio para su aplicación web, puede comprar uno fácilmente en el [Portal de Azure](https://portal.azure.com/). Durante el proceso de compra puede elegir tener asignados registros DNS del dominio raíz y WWW a su aplicación web de manera automática. También puede administrar su derecho de dominio dentro del Portal de Azure.


Siga los pasos siguientes para adquirir nombres de dominio y asignarlos a su aplicación web.

1. En el explorador, abra el [Portal de Azure](https://portal.azure.com/).

2. En la pestaña **Aplicaciones web**, haga clic en el nombre de la aplicación web, seleccione **Configuración** y elija **Dominios personalizados**.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. En la hoja **Dominios personalizados**, haga clic en **Comprar dominios**.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. En la hoja **Comprar dominios**, use el cuadro de texto para escribir el nombre de dominio que quiere comprar y presione Entrar. Los dominios disponibles sugeridos se mostrarán justo debajo del cuadro de texto. Seleccione el dominio que quiere comprar. Puede elegir adquirir varios dominios a la vez.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. Haga clic en **Información de contacto** y rellene el formulario de información de contacto del dominio.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

    > [AZURE.NOTE] It is very important that you fill out all required fields with as much accuracy as possible, especially the email address. In case of purchasing the domain without "Privacy protection", you might be asked to verify your email before the domain becomes active. In some cases, incorrect data for contact information will result in failure to purchase domains. 

6. Ahora puede elegir:

	a) "Renovar automáticamente" su dominio todos los años
	
	b) Participar en la "Protección de privacidad" que se incluye en el precio de compra de forma gratuita (excepto los TLD cuyo registro no es compatible con la privacidad. Por ejemplo: .co.in, .co.uk, etc.)
	
	c) "Asignar nombres de host predeterminado" para WWW y dominio raíz a la aplicación web actual.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
    > [AZURE.NOTE] Option C configures DNS bindings and Hostname bindings automatically for you.  This way, your Web App can be accessed using custom domain as soon as the purchase is complete (baring DNS propagation delays in few cases). In case, your Web App is behind Azure Traffic Manager, you will not see an option to assign root domain, as A-Records do not work with the Traffic Manager. 
    You can always assign the domains/sub-domains purchased through one Web App to another Web App and vice-versa. See step 8 for more details. 
	
7. Haga clic en la hoja **Seleccionar** en **Comprar dominios** y verá la información de compra en la hoja **Confirmación de compra**. Si acepta los términos legales y hace clic en **Comprar**, se enviará su pedido y podrá supervisar el proceso de compra en **Notificación**. La compra de dominio puede tardar algunos minutos en completarse.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. Si ha solicitado correctamente un dominio, puede administrar el dominio y asignarlo a la aplicación web. Haga clic en **"..."** en el lado derecho de su dominio. Puede **Cancelar compra** o **Administrar dominio**. Haga clic en **Administrar dominio** para que podamos enlazar el **subdominio** a nuestra aplicación web en la hoja **Administrar dominio**. Si quiere enlazar un **subdominio** a otra aplicación web, realice este paso desde dentro del contexto de la aplicación web correspondiente. Aquí puede elegir esta opción para asignar el dominio al extremo del Administrador de tráfico (si la aplicación web está detrás del Administrador de tráfico) con tan solo seleccionar el nombre del Administrador de tráfico en el menú desplegable. Al hacerlo, el dominio o subdominio se asignará automáticamente a todas las aplicaciones web que se encuentran detrás de ese extremo del Administrador de tráfico.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

    > [AZURE.NOTE] Puede elegir la opción "Cancelar compra" en un plazo de 5 días para obtener el reembolso completo. Tras cinco días, no podrá elegir "Cancelar compra"; en su lugar, verá una opción para "Eliminar" el dominio. Si se elimina el dominio, se liberará de la suscripción sin reembolso y se convertirá en un dominio disponible.

Una vez completada la configuración, el nombre de dominio personalizado aparecerá en la sección **Enlaces de nombres de host** de la aplicación web.

En este punto, debería poder escribir el nombre de dominio personalizado en el explorador y ver que le lleva sin problemas a la aplicación web.
 
## ¿Qué ocurre con el dominio personalizado que compró?

El dominio personalizado que compró en la hoja **Dominios personalizados y SSL** está vinculado a la suscripción de Azure. Como recurso de Azure, este dominio personalizado es diferente e independiente de la aplicación del Servicio de aplicaciones para la que compró por primera vez el dominio. Esto significa que:

- En el Portal de Azure, puede usar el dominio personalizado que compró en más de una aplicación del Servicio de aplicaciones y no solo en la aplicación para la que compró por primera vez el dominio personalizado.
- Puede administrar todos los dominios personalizados que haya comprado en la suscripción de Azure; para ello, vaya a la hoja **Dominios personalizados y SSL** de *cualquier* aplicación del Servicio de aplicaciones de dicha suscripción.
- Puede asignar cualquier aplicación del Servicio de aplicaciones de la misma suscripción de Azure a un subdominio dentro de ese dominio personalizado.
- Si decide eliminar una aplicación del Servicio de aplicaciones, puede optar por no eliminar el dominio personalizado al que está enlazado si desea seguir utilizándolo para otras aplicaciones.

## Si no puede ver el dominio personalizado que compró

Si ha comprado el dominio personalizado desde la hoja **Dominios personalizados y SSL**, pero no puede verlo en **Dominios administrados**, compruebe lo siguiente:

- Puede que el dominio personalizado no haya terminado de crearse. Compruebe la campana de notificación en la parte superior del Portal de Azure para ver el progreso.
- Puede que, por algún motivo, el dominio personalizado no se haya creado. Compruebe la campana de notificación en la parte superior del Portal de Azure para ver el progreso.
- Puede que el dominio personalizado se haya creado, pero que la hoja no se haya actualizado todavía. Trate de volver a abrir la hoja **Dominios personalizados y SSL**.
- Puede que haya eliminado el dominio personalizado en algún momento. Compruebe los registros de auditoría haciendo clic en **Configuración** > **Registros de auditoría** desde la hoja principal de la aplicación.
- Puede que la hoja **Dominios personalizados y SSL** en la que busca pertenezca a una aplicación creada en una suscripción de Azure diferente. Cambie a otra aplicación en una suscripción diferente y compruebe su hoja **Dominios personalizados y SSL**. En el portal, no podrá ver ni administrar dominios personalizados creados en una suscripción de Azure diferente de la aplicación. Sin embargo, si hace clic en **Administración avanzada**, la hoja **Administrar dominio** del dominio, se le redirigirá al sitio web del proveedor de dominio, donde podrá [configurar manualmente el dominio personalizado como cualquier dominio externo personalizado](web-sites-custom-domain-name.md) para aplicaciones creadas en una suscripción de Azure diferente.

<!---HONumber=AcomDC_0921_2016-->