<properties title="Learn how to configure an Azure website to use a domain name registered with eNom" pageTitle="Configuración de un nombre de dominio eNom para un sitio Web de Azure" metaKeywords ="Microsoft Azure, Sitios web Microsoft Azure, nombre de dominio" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configuración de un nombre de dominio personalizado para un sitio web de Azure (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Soluciones de red</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name/" title="Websites" class="current">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sitio Web mediante el Administrador de tráfico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [eNom](https://enom.com) con Sitios web Azure.

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

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por eNom. Utilice los pasos siguientes para localizar las herramientas DNS de enom.com:

1. Inicie sesión en su cuenta con eNom, seleccione **Domains (Dominios)** y, a continuación, **My Domains (Mis dominios)**. De este modo, aparecerán los nombres de sus dominios.

2. En la página **My Domains (Mis dominios)**, utilice el campo **Manage Domain (Administrar dominio)** para seleccionar **Host Records (Registros de host)**. De este modo, aparecerán los campos de registros de host.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/e-hostrecords.png)

4. El editor de registros de host permite seleccionar un tipo de registro concreto usando el campo **Record Type (Tipo de registro)**. Para los sitios web de Azure, solo debe usar las opciones **CNAME (Alias) (CNAME (alias))** o **A (Address) (D (dirección))**.

	![zone file editor](./media/web-sites-custom-domain-name/e-editrecords.png)

	> [WACOM.NOTE] Antes de agregar entradas al archivo de zona, tenga en cuenta que eNom ya ha creado registros DNS para el dominio raíz ("@") y un carácter comodín para los subdominios ("\*"). Si desea redirigir el dominio raíz a su sitio web, o usar un registro A de carácter comodín, debe modificar estas entradas en lugar de crear otras nuevas.

	* Al agregar un registro CNAME, debe configurar el campo **Host Name (Nombre de host)** con el subdominio que desee usar. Por ejemplo, **www**. Debe definir el campo **Address** con el nombre del dominio **.azurewebsites.net** de su sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**.

		> [WACOM.NOTE] Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
		> 
		> * Un valor para **Alias** de **www** con un valor para **Other host (Otro host)** de **&lt;nombredesusitioweb&gt;.azurewebsites.net**.
		> 
		> O BIEN
		> 
		> * Un valor para **Alias** de **awverify.www** con un valor para **Other host (Otro host)** de **awverify.&lt;nombredesusitioweb&gt;.azurewebsites.net**.
		> 
		> Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

	* Al agregar un registro A, debe configurar el campo **Host Name (Nombre de host)** en **@** (representa el nombre de dominio raíz, como **contoso.com**), * (un carácter comodín para que se corresponda con varios subdominios) o el subdominio específico que desee usar (por ejemplo, **www**). Debe configurar el campo **Address (Dirección)** con la dirección IP del sitio web de Azure.

		> [WACOM.NOTE] Al agregar un registro A, también debe agregar un registro CNAME con un host de **awverify** y un valor en **Points to (Apunta a)** de **awverify.<sunombredesitioweb>.azurewebsites.net.

5. Cuando termine de agregar o modificar los registros, haga clic en **Save (Guardar)** para guardar los cambios.

<h2><a name="enabledomain"></a>Habilitación del nombre de dominio en su sitio web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
