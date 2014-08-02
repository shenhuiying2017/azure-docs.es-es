<properties title="Learn how to configure an Azure web site to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure web site" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuración de un nombre de dominio personalizado para un sitio web de Azure (eNom)
=====================================================================================

[Dominio personalizado](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizado")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sitios web](/en-us/documentation/articles/web-sites-enom-custom-domain-name/ "Sitios web") | [Sitio web utilizando el Administrador de tráfico](/en-us/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/ "Sitio web utilizando el Administrador de tráfico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [eNom](https://enom.com) con Sitios web Azure.

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

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por eNom. Utilice los pasos siguientes para localizar las herramientas DNS de enom.com

1.  Inicie sesión en su cuenta con eNom, seleccione **Dominios** y, a continuación, **My Domains**. De este modo, aparecerán los nombres de sus dominios.

2.  En la página **My Domains**, utilice el campo **Administrar dominio** para seleccionar **Host Records**. De este modo, aparecerán los campos de registros de host.

    ![Pestaña de archivo de zona DNS](./media/web-sites-custom-domain-name/e-hostrecords.png)

3.  El editor de registros de host permite seleccionar un tipo de registro concreto usando el campo **Record Type**. Para los sitios web de Azure, solo debe usar las opciones **CNAME (alias)** o **D (dirección)**.

    ![editor de archivos de zona](./media/web-sites-custom-domain-name/e-editrecords.png)

    > [WACOM.NOTE] Antes de agregar entradas al archivo de zona, tenga en cuenta que eNom ya ha creado registros DNS para el dominio raíz ("@") y un carácter comodín para los subdominios ("\*"). Si desea redirigir el dominio raíz a su sitio web, o usar un registro D de carácter comodín, debe modificar estas entradas en lugar de crear otras nuevas.

    -   Al agregar un registro CNAME, debe configurar el campo **Host name** con el subdominio que desee usar. Por ejemplo, **www**. Debe definir el campo **Address** con el nombre del dominio **.azurewebsites.net** de su sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**.

        > [WACOM.NOTE] Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
        >
        > -   Un valor **Alias** de **www** con un valor **Other host** de **&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > O bien
        >
        > -   Un valor **Alias** de **awverify.www** con un valor **Other host** de **awverify.&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

    -   Al agregar un registro D, debe configurar el campo **Host Name** en **@** (representa el nombre de dominio raíz, como **contoso.com**), \* (un carácter comodín para que se corresponda con varios subdominios) o el subdominio específico que desee usar (por ejemplo, **www**). Debe configurar el campo **Address** con la dirección IP del sitio web de Azure.

        > [WACOM.NOTE] Al agregar un registro D, también debe agregar un registro CNAME con un host de **awverify** y un valor en **Points to** de \*\*awverify.&lt;yourwebsitename\>.azurewebsites.net.

4.  Cuando termine de agregar o modificar los registros, haga clic en **Save** para guardar los cambios.

Habilitación del nombre de dominio en su sitio web
--------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

