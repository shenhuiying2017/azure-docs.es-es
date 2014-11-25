<properties title="Learn how to configure an Azure website to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, Directnic" description="Learn how to configure an Azure website to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# Configuración de un nombre de dominio personalizado para un sitio web de Azure (Directnic).

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Dominio personalizado">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name/" title="Sitios web" class="current">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Sitio web mediante el Administrador de tr&aacute;fico">Sitio web mediante el Administrador de tr&aacute;fico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [DirectNic.com][DirectNic.com] con Sitios web Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

En este artículo:

-   [Descripción de los registros DNS][Descripción de los registros DNS]
-   [Configuración de los sitios web para el modo básico, compartido o estándar][Configuración de los sitios web para el modo básico, compartido o estándar]
-   [Incorporación de un registro DNS para el dominio personalizado][Incorporación de un registro DNS para el dominio personalizado]
-   [Habilitación del dominio en su sitio web][Habilitación del dominio en su sitio web]

## <a name="understanding-records"></a>Descripción de los registros DNS

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

## <a name="bkmk_configsharedmode"></a>Configuración de los sitios web para el modo básico, compartido o estándar

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a>

## Incorporación de un registro DNS para el dominio personalizado

</p>
Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por Directnic. Utilice los pasos siguientes para encontrar las herramientas DNS de Directnic.com

1.  Inicie sesión en su cuenta con Directnic.com, seleccione **My Services** y, a continuación, **Domains**.

    ![Menú de servicios de Directnic][Menú de servicios de Directnic]

2.  Haga clic en el nombre del dominio que desee usar con el sitio web de Azure.

3.  En la página de administración del dominio, haga clic en el botón **Manage** para **DNS** en el panel **Services**.

    ![Panel de servicios][Panel de servicios]

4.  Agregue registros DNS rellenando los campos **Type**, **Name** y **Data**. Una vez terminado, haga clic en el botón **Add Record**.

    ![][0]

    -   Al agregar un registro CNAME, debe configurar el campo **Name** con el subdominio que desee usar. Por ejemplo, **www**. Debe definir el campo **Data** con el nombre del dominio **.azurewebsites.net** de su sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**.

    -   Cuando agregue un registro D, debe establecer el campo **Name** en <**@*>\*(esto representa un nombre de dominio raíz, como **contoso.com**) o el subdominio que desea usar (por ejemplo, **www**). Debe configurar el campo **Data** con la dirección IP del sitio web de Azure.

        > [WACOM.NOTE] Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
        >
        > -   Un valor **Name** de **www** con un valor **Data** de **\<yourwebsitename\>.azurewebsites.net**.
        >
        > O bien
        >
        > -   Un valor **Name** de **awverify.www** con un valor **Data** de **awverify.\<yourwebsitename\>.azurewebsites.net**.
        >
        > Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

## <a name="enabledomain"></a>Habilitación del nombre de dominio en su sitio web

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

  [DirectNic.com]: https://directnic.com
  [Descripción de los registros DNS]: #understanding-records
  [Configuración de los sitios web para el modo básico, compartido o estándar]: #bkmk_configsharedmode
  [Incorporación de un registro DNS para el dominio personalizado]: #bkmk_configurecname
  [Habilitación del dominio en su sitio web]: #enabledomain
  [Menú de servicios de Directnic]: .\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png
  [Panel de servicios]: .\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png
  [0]: .\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png
