<properties title="Learn how to configure an Azure web site to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, Dotster" description="Learn how to configure an Azure web site to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configuración de un nombre de dominio personalizado para un sitio web de Azure (Dotster)
========================================================================================

[Dominio personalizado](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizado")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Sitios web](/en-us/documentation/articles/web-sites-dotster-custom-domain-name/ "Sitios web") | [Sitio web utilizando el Administrador de tráfico](/en-us/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/ "Sitio web utilizando el Administrador de tráfico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Dotster.com](https://dotster.com) con Sitios web Azure.

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

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por Dotster. Utilice los pasos siguientes para encontrar las herramientas DNS de Dotster.com.

1.  Inicie sesión en la cuenta con Dotster.com. En el menú **Domain**, seleccione **DomainCentral**.

    ![Menú de central de dominios de Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png)

2.  Seleccione el dominio para que aparezca una lista de opciones de configuración. A continuación, seleccione el vínculo **Nameservers**.

    ![Opciones de configuración de dominio de Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png)

3.  Seleccione **Use different name servers**. A fin de aprovechar los servicios DNS en Dotster, debe especificar los nombres de servidor siguientes: ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com y ns4.nameresolve.com.

    ![Opciones de configuración de dominio de Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png)

    > [WACOM.NOTE] El cambio de nombre del servidor puede tardar entre 24 y 48 horas en surtir efecto. El resto de pasos de este artículo no funcionarán hasta ese momento.

4.  En DomainCentral, seleccione el dominio y, a continuación, seleccione **DNS**. En la lista **Modify**, seleccione el tipo de registro DNS que vaya a agregar (**Alias de CNAME** o **Registro D**).

    ![Opciones de configuración de dominio de Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png)

5.  A continuación, especifique los campos **Host** y **Points to** para el registro. Una vez terminado, haga clic en el botón **Add**.

    ![Opciones de configuración de dominio de Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME.png)

    -   Al agregar un registro CNAME, debe configurar el campo **Host** con el subdominio que desee usar. Por ejemplo, **www**. Debe definir el campo **Points to** con el nombre de dominio **.azurewebsites.net** de su sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**.

    -   Cuando agregue un registro D, debe establecer el campo **Host** en **@** (esto representa un nombre de dominio raíz, como **contoso.com**) o el subdominio que desea usar (por ejemplo, **www**). Debe establecer el campo **Points to** en la dirección IP de su sitio web de Azure.

        > [WACOM.NOTE] Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
        >
        > -   Un valor **Host** de **www** con un valor **Points to** de **&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > O bien
        >
        > -   Un valor **Host** de **awverify.www** con un valor **Points to** de **awverify.&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

Habilitación del nombre de dominio en su sitio web
--------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

