<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure web site that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuración de un nombre de dominio personalizado para un sitio web de Azure con el Administrador de tráfico (Register.com)
=============================================================================================================================

[Dominio personalizado](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizado")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sitios web](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name/ "Sitios web") | [Sitio web utilizando el Administrador de tráfico](/en-us/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/ "Sitio web utilizando el Administrador de tráfico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Register.com](https://register.com) con Sitios web Azure.

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

Para asociar su dominio personalizado con un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por Register.com. Siga estos pasos para ubicar y usar las herramientas DNS.

1.  Inicie sesión en la cuenta en Register.com y seleccione **Your Account** en la esquina superior derecha para ver los dominios. A continuación, seleccione el nombre de dominio personalizado.

    ![Página Mi cuenta](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

2.  Desplácese por la página hasta que vea **Advanced Technical Settings**. Los vínculos de esta sección le permiten administrar los registros para su dominio. Para los registros CNAME, use el vínculo **Edit Domain Aliases Records**.

    ![Configuración técnica avanzada](./media/web-sites-custom-domain-name/rdotcom-advancedsettingstm.png)

3.  Cuando haga clic en el botón **Edit**, se mostrará un formulario que puede usar para modificar los registros existentes o agregar nuevos. El formulario es similar a los registros D y CNAME.

    -   Cuando agregue un registro CNAME, debe establecer el campo **.mydomainname.com** en el subdominio que desee usar. Por ejemplo, **www**. Debe establecer el valor **points to** con el nombre de dominio **.trafficmanager.net** del perfil del Administrador de tráfico que use con el sitio web de Azure. Por ejemplo, **contoso.trafficmanager.net**. Deje **Refers to Host Name** como **Select**, ya que este campo no es obligatorio cuando se crea un registro CNAME para usarlo con Sitios web Azure.

        ![Formulario CNAME](./media/web-sites-custom-domain-name/rdotcom-editcnamerecordtm.png)

4.  Cuando haya acabado de agregar o modificar registros, haga clic en **Continue** para revisar los cambios. Seleccione **Continue** de nuevo para guardar los cambios.

Habilitación del Administrador de tráfico para el sitio web
-----------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

