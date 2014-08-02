<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure web site that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuración de un nombre de dominio personalizado para un sitio web de Azure con el Administrador de tráfico (GoDaddy)
========================================================================================================================

[Dominio personalizado](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizado")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sitios web](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name/ "Sitios web") | [Sitio web utilizando el Administrador de tráfico](/en-us/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/ "Sitio web utilizando el Administrador de tráfico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Go Daddy](https://godaddy.com) con Sitios web Azure.

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

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por GoDaddy. Utilice los pasos siguientes para localizar las herramientas DNS de GoDaddy.com.

1.  Inicie sesión en la cuenta con GoDaddy.com, seleccione **My Account** y, a continuación, **Manage your domains**. Finalmente, seleccione el nombre de dominio que desea usar con el sitio web de Azure.

    ![Página de dominio personalizado para GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2.  En la página **Domain details**, seleccione la pestaña **DNS Zone File**. Esta es la sección que se utiliza para agregar y modificar los registros DNS para el nombre de dominio. Seleccione el botón **Edit** para mostrar el **Zone File Editor**.

    ![Pestaña de archivo de zona DNS](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

3.  El **Zone File Editor** se desglosa en secciones para cada tipo de registro, a partir de los registros D (que aparecen como **A (Host)** como la primera sección, seguida de los registros CNAME (que aparecen como **CNAME (Alias)**). Para agregar una entrada nueva, utilice el botón **Quick Add** debajo de la sección correspondiente. Para editar una entrada existente, seleccione dicha entrada y modifique la información existente.

    ![editor de archivos de zona](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

    > [WACOM.NOTE] Antes de agregar entradas al archivo de zona, tenga en cuenta que GoDaddy ya ha creado registros DNS de subdominios populares (llamados **Host** en el editor), como **email**, **files**, **mail** y otros. Si el nombre que desea utilizar ya existe, modifique el registro actual en lugar de crear uno nuevo.

    Al agregar un registro CNAME, debe configurar el campo **Host** con el subdominio que desee usar. Por ejemplo, **www**. Debe configurar el campo **Points to** con el nombre de dominio **.trafficmanager.net** del perfil del Administrador de tráfico que use con el sitio web de Azure. Por ejemplo, **contoso.trafficmanager.net**.

    > [WACOM.NOTE] Únicamente debe usar los registros CNAME al asociar el nombre de dominio personalizado a un sitio web cuya carga se equilibre usando el Administrador de tráfico.

4.  Cuando haya terminado de agregar o modificar los registros, haga clic en **Save Zone File** para guardar los cambios.

Habilitación del Administrador de tráfico para el sitio web
-----------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

