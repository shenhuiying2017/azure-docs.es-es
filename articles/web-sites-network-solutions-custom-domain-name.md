<properties 
	pageTitle="Configuración de un nombre de dominio de Network Solutions para un sitio web de Azure" 
	description="Aprenda a usar un nombre de dominio desde Network Solutions con Sitios web Azure" 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr, jroth"/>

#Configuración de un nombre de dominio personalizado para un sitio web de Azure (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/web-sites-custom-domain-name" title="Dominio personalizado">Dominio personalizado</a><a href="/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Sitios web" class="current">Sitio web</a> | <a href="/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Sitio web mediante el Administrador de tráfico">Sitio web mediante el Administrador de tráfico</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Network Solutions](https://www.networksolutions.com) con Sitios web Azure.

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

En este artículo:

-   [Descripción de los registros DNS](#understanding-records)
-   [Configuración de los sitios web para el modo básico, compartido o estándar](#bkmk_configsharedmode)
-   [Incorporación de un registro DNS para el dominio personalizado](#bkmk_configurecname)
-   [Habilitación del dominio en su sitio web](#enabledomain)

<h2><a name="understanding-records"></a>Descripción de los registros DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Configuración de los sitios web para el modo básico, compartido o estándar</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Incorporación de un registro DNS al dominio personalizado</h2>

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por Network Solutions. Siga estos pasos para ubicar y utilizar las herramientas DNS para networksolutions.com.

1. Inicie sesión en la cuenta en networksolutions.com y seleccione **My Account** (Mi cuenta) en la esquina superior derecha.

3. En la pestaña **My Products and Services** (Mis productos y servicios), seleccione **Edit DNS** (Editar DNS).

	![Página de edición de DNS](./media/web-sites-custom-domain-name/ns-editdns.png)

2. En la sección **Manage <yourdomainname>** (Administrar) de la página **Domain Names** (Nombres de dominio), seleccione **Edit Advanced DNS Records** (Editar registros de DNS avanzado).

	![Página de nombres de dominio con edición de registros DNS avanzados resaltada](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4. La página **Update Advanced DNS** (Actualizar DNS avanzado) contiene una sección para cada tipo de registro con un botón **Edit** (Editar) debajo de cada sección.
	
	* Para los registros D, use la sección **IP Address (A Records)** (Dirección IP (registros D)).
	* Para los registros CNAME, use la sección **Host Alias (CNAME Records)** (Alias de host (registros CNAME)).

	![Página de actualización de DNS avanzada](./media/web-sites-custom-domain-name/ns-updateadvanced.png)

5. Cuando haga clic en el botón **Edit**, se mostrará un formulario que puede usar para modificar los registros existentes o agregar nuevos.

	> [AZURE.NOTE]Antes de agregar nuevas entradas, tenga en cuenta que Network Solutions ya ha creado algunos registros DNS para elementos como el dominio raíz ("@") y un registro de comodín ("*") para subdominios. Si ya existe el registro que desea usar, modifíquelo en lugar de crear uno nuevo.

	* Cuando agregue un registro CNAME, debe establecer el campo **Alias** en el subdominio que desee usar. Por ejemplo, **www**. Debe seleccionar el campo del círculo junto al campo **Other host** (Otro host) y establecer **Other host** en el nombre de dominio **.azurewebsites.net** de Sitio web Azure. Por ejemplo, **contoso.azurwebsites.net**. Deje **Refers to Host Name** (Hace referencia al nombre de host) como **Select** (Seleccionar), ya que este campo no es obligatorio cuando se crea un registro CNAME para usarlo con Sitios web Azure.
	
		![Formulario CNAME](./media/web-sites-custom-domain-name/ns-cname.png)

		> [AZURE.NOTE]Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
		> 
		> * Un valor para **Alias** de **www** con un valor para **Other host** (Otro host) de **&lt;nombredesusitioweb&gt;.azurewebsites.net**.
		> 
		> OR
		> 
		> * Un valor para **Alias** de **awverify.www** con un valor para **Other host** (Otro host) de **awverify.&lt;nombredesusitioweb&gt;.azurewebsites.net**.
		> 
		> Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

	* Al agregar un registro D, se debe establecer el campo **Host** en **@** (esto representa el nombre de dominio raíz, como **contoso.com**), * (un comodín para que se corresponda con varios subdominios) o el subdominio que desea utilizar (por ejemplo, **www**). Debe establecer el campo **Numeric IP** (IP numérica) en la dirección IP de Sitio web Azure.

		![Formulario de registro D](./media/web-sites-custom-domain-name/ns-arecord.png)

5. Cuando haya acabado de agregar o modificar registros, haga clic en **Continue** (Continuar) para revisar los cambios. Seleccione **Save changes only** (Guardar solo los cambios) para guardar los cambios.

<h2><a name="enabledomain"></a>Habilitación del nombre de dominio en su sitio web</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

<!--HONumber=54-->