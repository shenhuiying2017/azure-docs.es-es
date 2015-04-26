<properties 
	pageTitle="Configuración de un nombre de dominio de Directnic para un sitio web de Azure mediante el Administrador de tráfico" 
	description="Obtenga información acerca de cómo configurar un sitio web de Azure que usa el Administrador de tráfico para usar un nombre de dominio registrado con Directnic" 
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
	ms.author="larryfr,jroth"/>

#Configuración de un nombre de dominio personalizado para un sitio web de Azure con el Administrador de tráfico (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name/" title="Websites">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Sitio web con Administrador de tráfico</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [DirectNic.com](https://directnic.com) con Sitios web Azure.

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

En este artículo:

-   [Descripción de los registros DNS](#understanding-records)
-   [Configuración de los sitios web para el modo estándar](#bkmk_configsharedmode)
-   [Incorporación de un registro DNS para el dominio personalizado](#bkmk_configurecname)
-   [Habilitación del Administrador de tráfico para el sitio web](#enabledomain)

<h2><a name="understanding-records"></a>Descripción de los registros DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Configuración de los sitios web para el modo estándar</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Incorporación de un registro DNS para el dominio personalizado</h2>

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por Directnic. Utilice los pasos siguientes para encontrar las herramientas DNS de Directnic.com

1. Inicie sesión en su cuenta con Directnic.com, seleccione **My Services** (Mis servicios) y luego **Domains** (Dominios). 

    ![Menú de servicios de Directnic](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2. Haga clic en el nombre del dominio que desee usar con el sitio web de Azure.

2. En la página de administración del dominio, haga clic en el botón **Manage** (Administrar) para **DNS** en el panel **Services** (Servicios).

    ![Panel de servicios](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4. Agregue registros DNS; para ello, rellene los campos **Type** (Tipo), **Name** (Nombre) y **Data** (Datos). Cuando haya terminado, haga clic en el botón **Add Record** (Agregar registro).

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS_TM.png)

    * Al agregar un registro CNAME, debe configurar el campo **Name** (Nombre) con el subdominio que desea usar. Por ejemplo, **www**. Debe configurar el campo **Data** con el nombre de dominio **.trafficmanager.net** del perfil de Administrador de tráfico que use con el sitio web de Azure. Por ejemplo, **contoso.trafficmanager.net**.

	    > [AZURE.NOTE] Únicamente debe usar los registros CNAME al asociar el nombre de dominio personalizado a un sitio web cuya carga se equilibre usando el Administrador de tráfico.

<h2><a name="enabledomain"></a>Habilitación del sitio web del Administrador de tráfico</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]





<!--HONumber=42-->
