<properties title="Learn how to configure an Azure web site to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuración de un nombre de dominio personalizado para un sitio web de Azure (GoDaddy)
========================================================================================

[Dominio personalizado](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizado")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sitios web](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name/ "Sitios web") | [Sitio web utilizando el Administrador de tráfico](/en-us/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/ "Sitio web utilizando el Administrador de tráfico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Go Daddy](https://godaddy.com) con Sitios web Azure.

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

Incorporación de un registro DNS para el dominio personalizado
--------------------------------------------------------------

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por GoDaddy. Utilice los pasos siguientes para localizar las herramientas DNS de GoDaddy.com.

1.  Inicie sesión en la cuenta con GoDaddy.com, seleccione **My Account** y, a continuación, **Manage your domains**. Finalmente, seleccione el nombre de dominio que desea usar con el sitio web de Azure.

    ![Página de dominio personalizado para GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2.  En la página **Domain details**, seleccione la pestaña **DNS Zone File**. Esta es la sección que se utiliza para agregar y modificar los registros DNS para el nombre de dominio. Seleccione el botón **Edit** para mostrar el **Zone File Editor**.

    ![Pestaña de archivo de zona DNS](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

3.  El **Zone File Editor** se desglosa en secciones para cada tipo de registro, a partir de los registros D (que aparecen como **A (Host)** como la primera sección, seguida de los registros CNAME (que aparecen como **CNAME (Alias)**). Para agregar una entrada nueva, utilice el botón **Quick Add** debajo de la sección correspondiente. Para editar una entrada existente, seleccione dicha entrada y modifique la información existente.

    > [WACOM.NOTE] Antes de agregar entradas al archivo de zona, tenga en cuenta que GoDaddy ya ha creado registros DNS de subdominios populares (llamados **Host** en el editor), como **email**, **files**, **mail** y otros. Si el nombre que desea utilizar ya existe, modifique el registro actual en lugar de crear uno nuevo.

    -   Al agregar un registro CNAME, debe configurar el campo **Host** con el subdominio que desee usar. Por ejemplo, **www**. Debe definir el campo **Points to** con el nombre de dominio **.azurewebsites.net** de su sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**.

        ![editor de archivos de zona](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

        > [WACOM.NOTE] Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
        >
        > -   Un valor **Host** de **www** con un valor **Points to** de **&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > O bien
        >
        > -   Un valor **Host** de **awverify.www** con un valor **Points to** de **awverify.&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

    -   Al agregar un registro D, se debe establecer el campo **Host** en **@** (esto representa el nombre de dominio raíz, como **contoso.com**), \* (un comodín para que se corresponda con varios subdominios) o el subdominio que desea utilizar (por ejemplo, **www**). Debe establecer el campo **Points to** en la dirección IP de su sitio web de Azure.

        ![editor de archivo de zona para un registro](./media/web-sites-custom-domain-name/godaddy-quickaddarecord.png)

4.  Cuando haya terminado de agregar o modificar los registros, haga clic en **Save Zone File** para guardar los cambios.

Habilitación del nombre de dominio en su sitio web
--------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

