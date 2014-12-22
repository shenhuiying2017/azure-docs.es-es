<properties title="Learn how to configure an Azure website to use a domain name registered with Directnic" pageTitle="Configuración de un nombre de dominio de Directnic para un sitio Web de Azure" metaKeywords ="Azure, Sitios Web Azure, Directnic" description="Learn how to configure an Azure website to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#Configuración de un nombre de dominio personalizado para un sitio web de Azure (Directnic).

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Soluciones de red</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name/" title="Websites" class="current">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sitio web con el Administrador de tráfico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]


[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [DirectNic.com](https://directnic.com) con Sitios web Azure.

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

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por Directnic. Utilice los pasos siguientes para encontrar las herramientas DNS de Directnic.com:

1. Inicie sesión en su cuenta con Directnic.com, seleccione **My Services** (Mis servicios) y luego **Domains** (Dominios).

    ![Directnic Services Menu](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2. Haga clic en el nombre del dominio que desee usar con el sitio web de Azure.

2. En la página de administración del dominio, haga clic en el botón **Manage** (Administrar) para **DNS** en el panel **Services** (Servicios).

    ![Services Panel](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4. Agregue registros DNS; para ello, rellene los campos **Type** (Tipo), **Name** (Nombre) y **Data** (Datos). Una vez terminado, haga clic en el botón **Add Record**.   

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png)

    * Al agregar un registro CNAME, debe configurar el campo **Name** (Nombre) con el subdominio que desea usar. Por ejemplo, **www**. Debe definir el campo **Data** con el nombre del dominio **.azurewebsites.net** de su sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**.

    * Cuando agregue un registro D, debe establecer el campo **Name** (Nombre) en **@** (esto representa el nombre del dominio raíz, como **contoso.com**) o el subdominio que desea usar (por ejemplo, **www**). Debe configurar el campo **Data** (Datos) con la dirección IP del sitio web de Azure.

		> [WACOM.NOTE] Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
		> 
		> * Un valor de **Name** (Nombre) de **www** con un valor de **Data** (Datos) de **&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> O BIEN
		> 
		> * Un valor de **Name** (Nombre) de **awverify.www** con un valor de **Data** (Datos) de **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.


<h2><a name="enabledomain"></a>Habilitación del nombre de dominio en su sitio web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
