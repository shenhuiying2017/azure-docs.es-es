<properties title="Learn how to configure an Azure website to use a domain name registered with Register.com" pageTitle="Configuración de un nombre de dominio de Register.com para un sitio web de Azure" metaKeywords="Azure, Sitios web Azure, nombre de dominio" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configuración de un nombre de dominio personalizado para un sitio web de Azure (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Websites" class="current">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sitio web utilizando el Administrador de tráfico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Register.com](https://www.register.com) con Sitios web Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

En este artículo:

-   [Descripción de los registros DNS](#understanding-records)
-   [Configuración de los sitios web para el modo básico, compartido o estándar](#bkmk_configsharedmode)
-   [Incorporación de un registro DNS para el dominio personalizado](#bkmk_configurecname)
-   [Habilitación del dominio en su sitio web](#enabledomain)

<h2><a name="understanding-records"></a>Descripción de los registros DNS</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Configuración de los sitios web para el modo básico, compartido o estándar</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Incorporación de un registro DNS para el dominio personalizado</h2>

Para asociar su dominio personalizado con un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por Register.com. Siga estos pasos para ubicar y usar las herramientas DNS.

1. Inicie sesión en la cuenta en register.com y seleccione **Your Account** en la esquina superior derecha para ver los dominios. A continuación, seleccione el nombre de dominio personalizado.

	![the my account page](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

3. Desplácese por la página hasta que vea **Advanced Technical Settings**. Los vínculos de esta sección le permiten administrar los registros para su dominio.

	* Para los registros D, use el vínculo **Edit IP Address Records**.
	* Para los registros CNAME, use el vínculo **Edit Domain Aliases Records**.

	![Advanced technical settings](./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png)

5. Cuando haga clic en el botón **Edit**, se mostrará un formulario que puede usar para modificar los registros existentes o agregar nuevos. El formulario es similar a los registros D y CNAME.

	* Cuando agregue un registro CNAME, debe establecer el campo **.mydomainname.com** en el subdominio que desee usar. Por ejemplo, **www**. Debe seleccionar el valor **points to** en el nombre de dominio **.azurewebsites.net** de su sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**. Deje **Refers to Host Name** como **Select**, ya que este campo no es obligatorio cuando se crea un registro CNAME para usarlo con Sitios web Azure.
	
		![cname form](./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png)

		> [WACOM.NOTE] Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
		> 
		> * Un valor para **Alias** de **www** con un valor para **Other host (Otro host)** de **&lt;nombredesusitioweb&gt;.azurewebsites.net**.
		> 
		> O BIEN
		> 
		> * Un valor para **Alias** de **awverify.www** con un valor para **Other host (Otro host)** de **awverify.&lt;nombredesusitioweb&gt;.azurewebsites.net**.
		> 
		> Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

	* Cuando agregue un registro A, debe establecer el campo **.mydomainname.com** en el subdominio que desee usar (por ejemplo, **www**). Deje el campo en blanco para establecer el dominio raíz. También puede usar **\** para crear una asignación de comodín. Debe establecer el campo **points to** en la dirección IP de su sitio web de Azure.

		![a record form](./media/web-sites-custom-domain-name/rdotcom-editarecord.png)

5. Cuando haya acabado de agregar o modificar registros, haga clic en **Continue** para revisar los cambios. Seleccione **Continue** de nuevo para guardar los cambios.

<h2><a name="enabledomain"></a>Habilitación del nombre de dominio en su sitio web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
