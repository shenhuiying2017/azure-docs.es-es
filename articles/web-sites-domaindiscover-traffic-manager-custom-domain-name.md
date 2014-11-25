<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, DomainDiscover, TierraNet, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# Configuración de un nombre de dominio personalizado para un sitio web de Azure con el Administrador de tráfico (DomainDiscover / TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Dominio personalizado">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Sitios web">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Sitio web mediante el Administrador de tr&aacute;fico" class="current">Sitio web mediante el Administrador de tr&aacute;fico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [DomainDiscover.com][DomainDiscover.com] con Sitios web Azure. DomainDiscover.com forma parte de [TierraNet][TierraNet].

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

En este artículo:

-   [Descripción de los registros DNS][Descripción de los registros DNS]
-   [Configuración de los sitios web para el modo estándar][Configuración de los sitios web para el modo estándar]
-   [Incorporación de un registro DNS para el dominio personalizado][Incorporación de un registro DNS para el dominio personalizado]
-   [Habilitación del Administrador de tráfico para el sitio web][Habilitación del Administrador de tráfico para el sitio web]

## <a name="understanding-records"></a>Descripción de los registros DNS

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

## <a name="bkmk_configsharedmode"></a>Configuración de los sitios web para el modo estándar

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## Incorporación de un registro DNS para el dominio personalizado

</p>
Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por DomainDiscover. Utilice los pasos siguientes para encontrar las herramientas DNS de DomainDiscover.com

1.  Inicie sesión en su cuenta con DomainDiscover.com (TierraNet) seleccionando **Panel de control** en el menú **Inicio de sesión**.

    ![Menú de inicio de sesión de DomainDiscover][Menú de inicio de sesión de DomainDiscover]

2.  En la página **Domain Services**, seleccione el dominio que desee usar para el sitio web de Azure.

    ![Página de administración de dominios][Página de administración de dominios]

3.  En la configuración del dominio, haga clic en el botón **Edit** para **DNS Service**.

    ![Botón de edición de DNS][Botón de edición de DNS]

4.  En la ventana **Manage DNS**, seleccione el tipo de registro DNS que haya que agregar en la lista **Add Records**. A continuación, haga clic en el botón **Add**.

    ![Botón de edición de DNS][1]

5.  En la página siguiente, especifique los valores de registro DNS. A continuación, haga clic en el botón **Add**.

    ![Botón de edición de DNS][2]

    -   Al agregar un registro CNAME, primero debe seleccionar **CNAME (Alias)** en la página **Manage DNS**. A continuación, configure el campo **Host** con el subdominio que desee usar. Por ejemplo, **www**. Debe configurar el campo **Alias Hostname** con el nombre de dominio **.trafficmanager.net** del perfil de Administrador de tráfico que use con el sitio web de Azure. Por ejemplo, **contoso.trafficmanager.net**. A continuación, proporcione un valor de período de vida (TTL), como 1800 segundos.

        > [WACOM.NOTE] Únicamente debe usar los registros CNAME al asociar el nombre de dominio personalizado a un sitio web cuya carga se equilibre usando el Administrador de tráfico.

## <a name="enabledomain"></a>Habilitación del Administrador de tráfico para el sitio web

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

  [DomainDiscover.com]: https://domaindiscover.com
  [TierraNet]: https://www.tierra.net/
  [Descripción de los registros DNS]: #understanding-records
  [Configuración de los sitios web para el modo estándar]: #bkmk_configsharedmode
  [Incorporación de un registro DNS para el dominio personalizado]: #bkmk_configurecname
  [Habilitación del Administrador de tráfico para el sitio web]: #enabledomain
  [Menú de inicio de sesión de DomainDiscover]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png
  [Página de administración de dominios]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png
  [Botón de edición de DNS]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png
  [1]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png
  [2]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png
