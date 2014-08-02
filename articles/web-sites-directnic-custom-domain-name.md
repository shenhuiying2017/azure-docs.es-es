<properties title="Learn how to configure an Azure web site to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, Directnic" description="Learn how to configure an Azure web site to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configuración de un nombre de dominio personalizado para un sitio web de Azure (Directnic)
==========================================================================================

[Dominio personalizado](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizado")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Sitios web](/en-us/documentation/articles/web-sites-directnic-custom-domain-name/ "Sitios web") | [Sitio web utilizando el Administrador de tráfico](/en-us/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/ "Sitio web utilizando el Administrador de tráfico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [DirectNic.com](https://directnic.com) con Sitios web Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

En este artículo:

-   [Descripción de los registros DNS](#understanding-records)
-   [Configuración de los sitios web para el modo básico, compartido o estándar](#bkmk_configsharedmode)
-   [Incorporación de un registro DNS para el dominio personalizado](#bkmk_configurecname)
-   [Habilitación del dominio en su sitio web](#enabledomain)

Descripción de los registros DNS
--------------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

Configuración de los sitios web para el modo básico, compartido o estándar
--------------------------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

## Incorporación de un registro DNS para el dominio personalizado

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por Directnic. Utilice los pasos siguientes para encontrar las herramientas DNS de Directnic.com

1.  Inicie sesión en su cuenta con Directnic.com, seleccione **My Services** y, a continuación, **Domains**.

    ![Menú de servicios de Directnic](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2.  Haga clic en el nombre del dominio que desee usar con el sitio web de Azure.

3.  En la página de administración del dominio, haga clic en el botón **Manage** para **DNS** en el panel **Services**.

    ![Panel de servicios](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4.  Agregue registros DNS rellenando los campos **Type**, **Name** y **Data**. Una vez terminado, haga clic en el botón **Add Record**.

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png)

    -   Al agregar un registro CNAME, debe configurar el campo **Name** con el subdominio que desee usar. Por ejemplo, **www**. Debe definir el campo **Data** con el nombre del dominio **.azurewebsites.net** de su sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**.

    -   Cuando agregue un registro D, debe establecer el campo **Name** en **@** (esto representa un nombre de dominio raíz, como **contoso.com**) o el subdominio que desea usar (por ejemplo, **www**). Debe configurar el campo **Data** con la dirección IP del sitio web de Azure.

        > [WACOM.NOTE] Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
        >
        > -   Un valor **Name** de **www** con un valor **Data** de **&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > O bien
        >
        > -   Un valor **Name** de **awverify.www** con un valor **Data** de **awverify.&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

Habilitación del nombre de dominio en su sitio web
--------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

