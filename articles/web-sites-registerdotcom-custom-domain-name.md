<properties title="Learn how to configure an Azure website to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# Configuración de un nombre de dominio personalizado para un sitio web de Azure (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Dominio personalizado">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Sitios web" class="current">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Sitio web mediante el Administrador de tr&aacute;fico">Sitio web mediante el Administrador de tr&aacute;fico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Register.com][1] con Sitios web Azure.

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
Para asociar su dominio personalizado con un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por Register.com. Siga estos pasos para ubicar y usar las herramientas DNS.

1.  Inicie sesión en la cuenta en Register.com y seleccione **Your Account** en la esquina superior derecha para ver los dominios. A continuación, seleccione el nombre de dominio personalizado.

    ![Página Mi cuenta][Página Mi cuenta]

2.  Desplácese por la página hasta que vea **Advanced Technical Settings**. Los vínculos de esta sección le permiten administrar los registros para su dominio.

    -   Para los registros D, use el vínculo **Edit IP Address Records**.
    -   Para los registros CNAME, use el vínculo **Edit Domain Aliases Records**.

    ![Configuración técnica avanzada][Configuración técnica avanzada]

3.  Cuando haga clic en el botón **Edit**, se mostrará un formulario que puede usar para modificar los registros existentes o agregar nuevos. El formulario es similar a los registros D y CNAME.

    -   Cuando agregue un registro CNAME, debe establecer el campo **.mydomainname.com** en el subdominio que desee usar. Por ejemplo, **www**. Debe seleccionar el valor **points to** en el nombre de dominio **.azurewebsites.net** de su sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**. Deje **Refers to Host Name** como **Select**, ya que este campo no es obligatorio cuando se crea un registro CNAME para usarlo con Sitios web Azure.

        ![Formulario CNAME][Formulario CNAME]

        > [WACOM.NOTE] Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
        >
        > -   Un valor **Alias** de **www** con un valor **Other host** de **\<yourwebsitename\>.azurewebsites.net**.
        >
        > O bien
        >
        > -   Un valor **Alias** de **awverify.www** con un valor **Other host** de **awverify.\<yourwebsitename\>.azurewebsites.net**.
        >
        > Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

    -   Cuando agregue un registro D, debe establecer el campo **.mydomainname.com** en el subdominio que desee usar (por ejemplo, **www**). Deje el campo en blanco para establecer el dominio raíz. También puede usar **\*\* para crear una asignación de comodín. Debe establecer el campo** Points to\*\* en la dirección IP de su sitio web de Azure.

        ![Formulario de registro D][Formulario de registro D]

4.  Cuando haya acabado de agregar o modificar registros, haga clic en **Continue** para revisar los cambios. Seleccione **Continue** de nuevo para guardar los cambios.

## <a name="enabledomain"></a>Habilitación del nombre de dominio en su sitio web

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

  [Register.com]: /es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [1]: https://www.register.com
  [Descripción de los registros DNS]: #understanding-records
  [Configuración de los sitios web para el modo básico, compartido o estándar]: #bkmk_configsharedmode
  [Incorporación de un registro DNS para el dominio personalizado]: #bkmk_configurecname
  [Habilitación del dominio en su sitio web]: #enabledomain
  [Página Mi cuenta]: ./media/web-sites-custom-domain-name/rdotcom-myaccount.png
  [Configuración técnica avanzada]: ./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png
  [Formulario CNAME]: ./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png
  [Formulario de registro D]: ./media/web-sites-custom-domain-name/rdotcom-editarecord.png
