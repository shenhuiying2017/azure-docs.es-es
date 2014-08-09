<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure web site that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuración de un nombre de dominio personalizado para un sitio web de Azure con el Administrador de tráfico (eNom)
=====================================================================================================================

[Dominio personalizado](/es-es/documentation/articles/web-sites-custom-domain-name "Dominio personalizado")[GoDaddy](/es-es/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/es-es/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/es-es/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/es-es/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/es-es/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sitios web](/es-es/documentation/articles/web-sites-enom-custom-domain-name/ "Sitios web") | [Sitio web utilizando el Administrador de tráfico](/es-es/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/ "Sitio web utilizando el Administrador de tráfico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [eNom](https://enom.com) con Sitios web Azure.

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

<h2>Incorporación de un registro DNS para el dominio personalizado</h2>

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por eNom. Utilice los pasos siguientes para localizar las herramientas DNS de enom.com

1.  Inicie sesión en su cuenta con eNom, seleccione **Dominios** y, a continuación, **My Domains**. De este modo, aparecerán los nombres de sus dominios.

2.  En la página **My Domains**, utilice el campo **Administrar dominio** para seleccionar **Host Records**. De este modo, aparecerán los campos de registros de host.

    ![Pestaña de archivo de zona DNS](./media/web-sites-custom-domain-name/e-hostrecords.png)

3.  El editor de registros de host permite seleccionar un tipo de registro concreto usando el campo **Record Type**. Para los sitios web de Azure que utilizan el Administrador de tráfico, solo se debe utilizar la selección **CNAME (alias)**, debido a que el Administrador de tráfico solo funciona con registros CNAME.

    ![editor de archivos de zona](./media/web-sites-custom-domain-name/e-editrecordstm.png)

    -   Al agregar un registro CNAME, debe configurar el campo **Host name** con el subdominio que desee usar. Por ejemplo, **www**. Debe configurar el campo **Address** con el nombre de dominio **.trafficmanager.net** del perfil del Administrador de tráfico que use con el sitio web de Azure. Por ejemplo, **contoso.trafficmanager.net**.

4.  Cuando termine de agregar o modificar los registros, haga clic en **Save** para guardar los cambios.

Habilitación del Administrador de tráfico para el sitio web
-----------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

