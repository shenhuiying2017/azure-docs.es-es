<properties title="Learn how to configure an Azure website to use a domain name registered with Network Solutions" pageTitle="Configuración de un nombre de dominio de Network Solutions para un sitio web de Azure" metaKeywords="Azure, Sitios web Azure, nombre de dominio" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configuración de un nombre de dominio personalizado para un sitio web de Azure (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Websites" class="current">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sitio web utilizando el Administrador de tráfico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Network Solutions](https://www.networksolutions.com) con Sitios web Azure.

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

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por Network Solutions. Siga estos pasos para ubicar y utilizar las herramientas DNS para networksolutions.com.

1. Inicie sesión en la cuenta en networksolutions.com y seleccione **My Account** en la esquina superior derecha.

3. En la pestaña **My Products and Services**, seleccione **Edit DNS**.

	![edit dns page](./media/web-sites-custom-domain-name/ns-editdns.png)

2. En la sección **Manage <sunombrededominio>** de la página **Domain Names**, seleccione **Edit Advanced DNS Records**.

	![domain names page with edit advanced dns records highlighted](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4. La página **Update Advanced DNS** contiene una sección para cada tipo de registro con un botón **Edit** debajo de cada sección.
	
	* Para los registros D, use la sección **IP Address (A Records)**.
	* Para los registros CNAME, use la sección **Host Alias (CNAME Records)**.

	![update advanced dns page](./media/web-sites-custom-domain-name/ns-updateadvanced.png)

5. Cuando haga clic en el botón **Edit**, se mostrará un formulario que puede usar para modificar los registros existentes o agregar nuevos. 

	> [WACOM.NOTE] Antes de agregar nuevas entradas, tenga en cuenta que Network Solutions ya ha creado algunos registros DNS para elementos como el dominio raíz ("@") y un registro de comodín ("*") para subdominios. Si ya existe el registro que desea usar, modifíquelo en lugar de crear uno nuevo.

	* Cuando agregue un registro CNAME, debe establecer el campo **Alias** en el subdominio que desee usar. Por ejemplo, **www**. Debe seleccionar el campo del círculo junto al campo **Other host** y establecer **Other host** en el nombre de dominio **.azurewebsites.net** del sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**. Deje **Refers to Host Name** como **Select**, ya que este campo no es obligatorio cuando se crea un registro CNAME para usarlo con Sitios web Azure.
	
		![cname form](./media/web-sites-custom-domain-name/ns-cname.png)

		> [WACOM.NOTE] Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
		> 
		> * Un valor para **Alias** de **www** con un valor para **Other host (Otro host)** de **&lt;nombredesusitioweb&gt;.azurewebsites.net**.
		> 
		> O BIEN
		> 
		> * Un valor para **Alias** de **awverify.www** con un valor para **Other host (Otro host)** de **awverify.&lt;nombredesusitioweb&gt;.azurewebsites.net**.
		> 
		> Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

	* Al agregar un registro D, se debe establecer el campo **Host** en **@** (esto representa el nombre de dominio raíz, como **contoso.com**), * (un comodín para que se corresponda con varios subdominios) o el subdominio que desea utilizar (por ejemplo, **www**). Debe establecer el campo **Numeric IP** en la dirección IP del sitio web de Azure.

		![a record form](./media/web-sites-custom-domain-name/ns-arecord.png)

5. Cuando haya acabado de agregar o modificar registros, haga clic en **Continue** para revisar los cambios. Seleccione **Save changes only** para guardar los cambios.

<h2><a name="enabledomain"></a>Habilitación del nombre de dominio en su sitio web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
