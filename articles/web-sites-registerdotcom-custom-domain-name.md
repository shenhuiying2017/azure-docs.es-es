<properties title="Learn how to configure an Azure web site to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuración de un nombre de dominio personalizado para un sitio web de Azure (Register.com)
=============================================================================================

[Dominio personalizado](/es-es/documentation/articles/web-sites-custom-domain-name "Dominio personalizado")[GoDaddy](/es-es/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/es-es/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/es-es/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/es-es/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/es-es/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sitios web](/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name/ "Sitios web") | [Sitio web utilizando el Administrador de tráfico](/es-es/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/ "Sitio web utilizando el Administrador de tráfico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Register.com](https://register.com) con Sitios web Azure.

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

Para asociar su dominio personalizado con un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por Register.com. Siga estos pasos para ubicar y usar las herramientas DNS.

1.  Inicie sesión en la cuenta en Register.com y seleccione **Your Account** en la esquina superior derecha para ver los dominios. A continuación, seleccione el nombre de dominio personalizado.

    ![Página Mi cuenta](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

2.  Desplácese por la página hasta que vea **Advanced Technical Settings**. Los vínculos de esta sección le permiten administrar los registros para su dominio.

    -   Para los registros D, use el vínculo **Edit IP Address Records**.
    -   Para los registros CNAME, use el vínculo **Edit Domain Aliases Records**.

    ![Configuración técnica avanzada](./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png)

3.  Cuando haga clic en el botón **Edit**, se mostrará un formulario que puede usar para modificar los registros existentes o agregar nuevos. El formulario es similar a los registros D y CNAME.

    -   Cuando agregue un registro CNAME, debe establecer el campo **.mydomainname.com** en el subdominio que desee usar. Por ejemplo, **www**. Debe seleccionar el valor **points to** en el nombre de dominio **.azurewebsites.net** de su sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**. Deje **Refers to Host Name** como **Select**, ya que este campo no es obligatorio cuando se crea un registro CNAME para usarlo con Sitios web Azure.

        ![Formulario CNAME](./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png)

        > [WACOM.NOTE] Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
        >
        > -   Un valor **Alias** de **www** con un valor **Other host** de **&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > O bien
        >
        > -   Un valor **Alias** de **awverify.www** con un valor **Other host** de **awverify.&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

    -   Cuando agregue un registro D, debe establecer el campo **.mydomainname.com** en el subdominio que desee usar (por ejemplo, **www**). Deje el campo en blanco para establecer el dominio raíz. También puede usar **\*\* para crear una asignación de comodín. Debe establecer el campo\*\*points to** en la dirección IP de su sitio web de Azure.

        ![Formulario de registro D](./media/web-sites-custom-domain-name/rdotcom-editarecord.png)

4.  Cuando haya acabado de agregar o modificar registros, haga clic en **Continue** para revisar los cambios. Seleccione **Continue** de nuevo para guardar los cambios.

Habilitación del nombre de dominio en su sitio web
--------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

