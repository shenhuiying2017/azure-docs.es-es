<properties 
	pageTitle="Configuración de un nombre de dominio de Dotster para un sitio web de Azure con el Administrador de tráfico" 
	description="Obtenga información acerca de cómo configurar un sitio web de Azure que usa el Administrador de tráfico para usar un nombre de dominio registrado con Dotster" 
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

#Configuración de un nombre de dominio personalizado para un sitio web de Azure con el Administrador de tráfico (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name/" title="Websites">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Sitio web utilizando el Administrador de tráfico</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Dotster.com](https://dotster.com) con Sitios web Azure.

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

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por Dotster. Utilice los pasos siguientes para encontrar las herramientas DNS de Dotster.com.

1. Inicie sesión en su cuenta con Dotster.com. En el menú **Dominio**, seleccione **DomainCentral**.

    ![Domain Central Dotster Menu](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png)

2. Seleccione el dominio para que aparezca una lista de opciones de configuración. A continuación, seleccione el vínculo **Nameservers**.

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png)

3. Seleccione **Use different name servers (Servidores de nombres diferentes)**. Para aprovechar las ventajas de los servicios DNS en Dotster, debe especificar los servidores de nombres siguientes: ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com y ns4.nameresolve.com.

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png)

    > [AZURE.NOTE]  El cambio de nombre del servidor puede tardar entre 24 y 48 horas en surtir efecto. El resto de pasos de este artículo no funcionarán hasta ese momento.

4. En DomainCentral, seleccione el dominio y, a continuación, seleccione **DNS**. En la lista **Modify**, seleccione el tipo de registro DNS que vaya a agregar (**Alias de CNAME** o **Registro D**). 

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png)

5. Especifique los campos **Host** y **Points To (Apunta a)** para el registro. Una vez terminado, haga clic en el botón **Add (Agregar)**.

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME_TM.png)
 
    * Al agregar un registro CNAME, debe configurar el campo **Host** con el subdominio que desee usar. Por ejemplo, **www**. Debe configurar el campo **Apunta a** con el nombre de dominio **.trafficmanager.net** del perfil del Administrador de tráfico que use con el Sitio web Azure. Por ejemplo, **contoso.trafficmanager.net**.

	    > [AZURE.NOTE] Únicamente debe usar los registros CNAME al asociar el nombre de dominio personalizado a un sitio web cuya carga se equilibre usando el Administrador de tráfico.

<h2><a name="enabledomain"></a>Habilitación del sitio web del Administrador de tráfico</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]




<!--HONumber=42-->
