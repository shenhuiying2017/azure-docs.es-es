<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure web site using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, DomainDiscover, TierraNet, Traffic Manager" description="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configuración de un nombre de dominio personalizado para un sitio web de Azure con el Administrador de tráfico (DomainDiscover/TierraNet)
=========================================================================================================================================

[Dominio personalizado](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizado")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Sitios web](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name/ "Sitios web") | [Sitio web utilizando el Administrador de tráfico](/en-us/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/ "Sitio web utilizando el Administrador de tráfico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [DomainDiscover.com](https://domaindiscover.com) con Sitios web Azure. DomainDiscover.com forma parte de [TierraNet](https://www.tierra.net/).

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

En este artículo:

-   [Descripción de los registros DNS](#understanding-records)
-   [Configuración de los sitios web para el modo estándar](#bkmk_configsharedmode)
-   [Incorporación de un registro DNS para el dominio personalizado](#bkmk_configurecname)
-   [Habilitación del Administrador de tráfico para el sitio web](#enabledomain)

Descripción de los registros DNS
--------------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

Configuración de los sitios web para el modo estándar
-----------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

## Incorporación de un registro DNS para el dominio personalizado

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por DomainDiscover. Utilice los pasos siguientes para encontrar las herramientas DNS de DomainDiscover.com

1.  Inicie sesión en su cuenta con DomainDiscover.com (TierraNet) seleccionando **Panel de control** en el menú **Inicio de sesión**.

    ![Menú de inicio de sesión de DomainDiscover](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2.  En la página **Domain Services**, seleccione el dominio que desee usar para el sitio web de Azure.

    ![Página de administración de dominios](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3.  En la configuración del dominio, haga clic en el botón **Edit** para **DNS Service**.

    ![Botón de edición de DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4.  En la ventana **Manage DNS**, seleccione el tipo de registro DNS que haya que agregar en la lista **Add Records**. A continuación, haga clic en el botón **Add**.

    ![Botón de edición de DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5.  En la página siguiente, especifique los valores de registro DNS. A continuación, haga clic en el botón **Add**.

    ![Botón de edición de DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png)

    -   Al agregar un registro CNAME, primero debe seleccionar **CNAME (Alias)** en la página **Manage DNS**. A continuación, configure el campo **Host** con el subdominio que desee usar. Por ejemplo, **www**. Debe configurar el campo **Alias Hostname** con el nombre de dominio **.trafficmanager.net** del perfil de Administrador de tráfico que use con el sitio web de Azure. Por ejemplo, **contoso.trafficmanager.net**. A continuación, proporcione un valor de período de vida (TTL), como 1800 segundos.

        > [WACOM.NOTE] Únicamente debe usar los registros CNAME al asociar el nombre de dominio personalizado a un sitio web cuya carga se equilibre usando el Administrador de tráfico.

Habilitación del Administrador de tráfico para el sitio web
-----------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

