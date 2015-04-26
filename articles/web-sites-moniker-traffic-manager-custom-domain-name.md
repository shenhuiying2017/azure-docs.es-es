<properties 
	pageTitle="Configuración de un nombre de dominio de Moniker para un sitio web de Azure con el Administrador de tráfico" 
	description="Obtenga información acerca de cómo configurar un sitio web de Azure que usa el Administrador de tráfico para usar un nombre de dominio registrado con Moniker" 
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

#Configuración de un nombre de dominio personalizado para un sitio web de Azure con el Administrador de tráfico (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name/" title="Websites">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Sitio web utilizando el Administrador de tráfico</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Moniker](https://moniker.com) con Sitios web Azure.

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

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por Moniker. Use los siguientes pasos para ubicar las herramientas DNS para Moniker.com.

1. Inicie sesión en la cuenta con Moniker.com, seleccione **My Domains** y, a continuación, haga clic en **Manage Templates**.

    ![My Domains page for Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2. En la página **Zone Template Management**, seleccione **Create New Template**.

    ![Moniker Zone Template Management](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3. Complete el nombre de plantilla en **Template Name**. 

5. A continuación, cree un registro DNS seleccionando primero el tipo de registro en **Record Type**. A continuación, complete el nombre de host en **Hostname** y la dirección en **Address**. 

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png)

    * Cuando agregue un registro CNAME, debe establecer el campo **Hostname** en el subdominio que desee usar. Por ejemplo, **www**. Debe configurar el campo **Address** con el nombre de dominio **.trafficmanager.net** del perfil del Administrador de tráfico que use con el Sitio web Azure. Por ejemplo, **contoso.trafficmanager.net**.

	    > [AZURE.NOTE] Únicamente debe usar los registros CNAME al asociar el nombre de dominio personalizado a un sitio web cuya carga se equilibre usando el Administrador de tráfico.
	 
7. Haga clic en el botón **Add** para agregar la entrada.

8. Una vez que se agreguen todas las entradas, haga clic en el botón **Save**.

5. Seleccione **Domain Manager** para volver a la lista de dominios.

6. Seleccione la casilla de verificación del dominio de destino y haga clic en **Manage Templates** de nuevo.

7. Busque la nueva plantilla que creó en los pasos anteriores. A continuación, haga clic en el vínculo **place selected domains (1) into this Template**.

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

<h2><a name="enabledomain"></a>Habilitación del sitio web del Administrador de tráfico</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]





<!--HONumber=42-->
