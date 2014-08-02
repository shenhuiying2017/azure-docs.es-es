<properties title="Learn how to configure an Azure web site to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="Learn how to configure an Azure web site to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configuración de un nombre de dominio personalizado para un sitio web de Azure (Moniker)
========================================================================================

[Dominio personalizado](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizado")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sitios web](/en-us/documentation/articles/web-sites-moniker-custom-domain-name/ "Sitios web") | [Sitio web utilizando el Administrador de tráfico](/en-us/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/ "Sitio web utilizando el Administrador de tráfico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Moniker.com](https://moniker.com) con Sitios web Azure.

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

<h2>Incorporación de un registro DNS para el dominio personalizado</h2>

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por Moniker. Use los siguientes pasos para ubicar las herramientas DNS para Moniker.com.

1.  Inicie sesión en la cuenta con Moniker.com, seleccione **My Domains** y, a continuación, haga clic en **Manage Templates**.

    ![Página Mis dominios de Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2.  En la página **Zone Template Management**, seleccione **Create New Template**.

    ![Administración de plantillas de la zona de Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3.  Complete el nombre de plantilla en **Template Name**.

4.  A continuación, cree un registro DNS seleccionando primero el tipo de registro en **Record Type**. A continuación, complete el nombre de host en **Hostname** y la dirección en **Address**.

    ![Plantilla de zona de creación de Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png)

    -   Cuando agregue un registro CNAME, debe establecer el campo **Hostname** en el subdominio que desee usar. Por ejemplo, **www**. Debe definir el campo **Address** con el nombre del dominio **.azurewebsites.net** de su sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**.

    -   Cuando agregue un registro D, debe establecer el campo **Hostname** en **@** (esto representa un nombre de dominio raíz, como **contoso.com**) o el subdominio que desea usar (por ejemplo, **www**). Debe configurar el campo **Address** con la dirección IP del sitio web de Azure.

        > [WACOM.NOTE] Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
        >
        > -   Un valor **Hostname** de **www** con un valor **Address** de **&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > O bien
        >
        > -   Un valor **Hostname** de **awverify.www** con un valor **Address** de **awverify.&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

5.  Haga clic en el botón **Add** para agregar la entrada.

6.  Una vez que se agreguen todas las entradas, haga clic en el botón **Save**.

7.  Seleccione **Domain Manager** para volver a la lista de dominios.

8.  Seleccione la casilla de verificación del dominio de destino y haga clic en **Manage Templates** de nuevo.

9.  Busque la nueva plantilla que creó en los pasos anteriores. A continuación, haga clic en el vínculo **place selected domains (1) into this Template**.

    ![Plantilla de zona de creación de Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

Habilitación del nombre de dominio en su sitio web
--------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

