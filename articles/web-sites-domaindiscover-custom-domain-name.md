<properties 
	pageTitle="Configuración de un nombre de dominio de DomainDiscover para un sitio web de Azure" 
	description="Obtenga información acerca de cómo configurar un sitio web de Azure para usar un nombre de dominio registrado con DomainDiscover - TierraNet" 
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

#Configuración de un nombre de dominio personalizado para un sitio web de Azure (DomainDiscover / TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Websites" class="current">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sitio web con Administrador de tráfico</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [DomainDiscover.com](https://domaindiscover.com) con Sitios web Azure. DomainDiscover.com forma parte de TierraNet.

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

<a name="bkmk_configurecname"></a><h2>Incorporación de un registro DNS para el dominio personalizado</h2>

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por DomainDiscover. Utilice los pasos siguientes para encontrar las herramientas DNS de DomainDiscover.com

1. Inicie sesión en su cuenta con DomainDiscover.com (TierraNet), para ello seleccione **Panel de control** en el menú **Inicio de sesión**.

    ![Menú de inicio de sesión de DomainDiscover](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2. En la página **Domain Services** (Servicios de dominio), seleccione el dominio que desea usar para su sitio web de Azure.

    ![Página de administración de dominios](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3. En la configuración del dominio, haga clic en el botón **Edit** (Editar) para **DNS Service** (Servicio DNS).

    ![Botón de edición de DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4. En la ventana **Manage DNS** (Administrar DNS), seleccione el tipo de registro DNS para agregar en la lista **Add Records** (Agregar registros). A continuación, haga clic en el botón **Add** (Agregar).

    ![Botón de edición de DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5. En la página siguiente, especifique los valores de registro DNS. A continuación, haga clic en el botón **Add** (Agregar).

    ![Botón de edición de DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png)

    * Al agregar un registro CNAME, primero debe seleccionar **CNAME (Alias)** en la página **Manage DNS** (Administrar DNS). A continuación, configure el campo **Host** con el subdominio que desee usar. Por ejemplo, **www**. Debe configurar el campo **Alias Hostname** con el nombre del dominio **.azurewebsites.net** del sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**. A continuación, proporcione un valor de período de vida (TTL); por ejemplo, 1800 segundos.

    * Al agregar un registro A, primero debe seleccionar **A** en la página **Manage DNS** (Administrar DNS). A continuación, configure el campo **Host** como **@** (esto representa el nombre de dominio raíz, como **contoso.com**) o el subdominio que desee usar (por ejemplo, **www**). Debe configurar el campo **IP Address** con la dirección IP del sitio web de Azure. A continuación, proporcione un valor de período de vida (TTL); por ejemplo, 1800 segundos.

		> [AZURE.NOTE] Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
		> 
		> * Un valor de **Host** de **www** con un valor de **Alias Hostname** de **&lt;nombredesusitioweb&gt;.azurewebsites.net**.
		> 
		> O bien
		> 
		> * Un valor de **Host** de **awverify.www** con un valor de **Alias Hostname** de **awverify.&lt;nombredesusitioweb&gt;.azurewebsites.net**.
		> 
		> Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.


5. Si ha agregado un registro D, debería ver un aviso que indica que el registro D existente en su dominio no está activo. Este utiliza el registro modificado más recientemente y DomainDirect ya cuenta con un registro D para el nombre de dominio raíz. Puede basarse en esta precedencia, o puede eliminar el registro A predeterminado presionando el botón **DELETE** (Eliminar).

<h2><a name="enabledomain"></a>Habilitación del nombre de dominio en su sitio web</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]





<!--HONumber=42-->
