<properties title="Learn how to configure an Azure website to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure website" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Configuración de un nombre de dominio personalizado para un sitio web de Azure (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Dominio personalizado">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name/" title="Sitios web" class="current">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Sitio web mediante el Administrador de tr&aacute;fico">Sitio web mediante el Administrador de tr&aacute;fico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][]]

[WACOM.INCLUDE [intro][]]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [eNom][] con Sitios web Azure.

[WACOM.INCLUDE [introfooter][]]

En este artículo:

-   [Descripción de los registros DNS][]
-   [Configuración de los sitios web para el modo básico, compartido o estándar][]
-   [Incorporación de un registro DNS para el dominio personalizado][]
-   [Habilitación del dominio en su sitio web][]

## <a name="understanding-records"></a>Descripción de los registros DNS

[WACOM.INCLUDE [understandingdns][]]

## <a name="bkmk_configsharedmode"></a>Configuración de los sitios web para el modo básico, compartido o estándar

[WACOM.INCLUDE [modes][]]

<a name="bkmk_configurecname"></a>

## Incorporación de un registro DNS para el dominio personalizado

</p>
Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por eNom. Utilice los pasos siguientes para localizar las herramientas DNS de enom.com

1.  Inicie sesión en su cuenta con eNom, seleccione **Dominios** y, a continuación, **My Domains**. De este modo, aparecerán los nombres de sus dominios.

2.  En la página **My Domains**, utilice el campo **Administrar dominio** para seleccionar **Host Records**. De este modo, aparecerán los campos de registros de host.

    ![Pestaña de archivo de zona DNS][]

3.  El editor de registros de host permite seleccionar un tipo de registro concreto usando el campo **Record Type**. Para los sitios web de Azure, solo debe usar las opciones **CNAME (alias)** o **D (dirección)**.

    ![editor de archivos de zona][]

    > [WACOM.NOTE] Antes de agregar entradas al archivo de zona, tenga en cuenta que eNom ya ha creado registros DNS para el dominio raíz (<'@'>) y un carácter comodín para los subdominios ('\*'). Si desea redirigir el dominio raíz a su sitio web, o usar un registro D de carácter comodín, debe modificar estas entradas en lugar de crear otras nuevas.

    -   Al agregar un registro CNAME, debe configurar el campo **Host name** con el subdominio que desee usar. Por ejemplo, **www**. Debe definir el campo **Address** con el nombre del dominio **.azurewebsites.net** de su sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**.

        > [WACOM.NOTE] Si utiliza un registro D, tiene que agregar también un registro CNAME con alguna de las configuraciones siguientes:
        >
        > -   Un valor **Alias** de **www** con un valor **Other host** de **\<yourwebsitename\>.azurewebsites.net**.
        >
        > O bien
        >
        > -   Un valor **Alias** de **awverify.www** con un valor **Other host** de **awverify.\<yourwebsitename\>.azurewebsites.net**.
        >
        > Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

    -   Al agregar un registro D, debe configurar el campo **Host Name** en <**@*>\* (representa el nombre de dominio raíz, como **contoso.com**), \* (un carácter comodín para que se corresponda con varios subdominios) o el subdominio específico que desee usar (por ejemplo, **www**). Debe configurar el campo **Address** con la dirección IP del sitio web de Azure.

        > [WACOM.NOTE] Al agregar un registro D, también debe agregar un registro CNAME con un host de **awverify** y un valor en **Points to** de \*\*awverify.\<yourwebsitename\>.azurewebsites.net.

4.  Cuando termine de agregar o modificar los registros, haga clic en **Save** para guardar los cambios.

## <a name="enabledomain"></a>Habilitación del nombre de dominio en su sitio web

[WACOM.INCLUDE [modes][1]]

  [Dominio personalizado]: /es-es/documentation/articles/web-sites-custom-domain-name "Dominio personalizado"
  [GoDaddy]: /es-es/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /es-es/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /es-es/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /es-es/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /es-es/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /es-es/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Sitio web]: /es-es/documentation/articles/web-sites-enom-custom-domain-name/ "Sitios web"
  [Sitio web mediante el Administrador de tráfico]: /es-es/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/ "Sitio web mediante el Administrador de tráfico"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [eNom]: https://enom.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Descripción de los registros DNS]: #understanding-records
  [Configuración de los sitios web para el modo básico, compartido o estándar]: #bkmk_configsharedmode
  [Incorporación de un registro DNS para el dominio personalizado]: #bkmk_configurecname
  [Habilitación del dominio en su sitio web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [Pestaña de archivo de zona DNS]: ./media/web-sites-custom-domain-name/e-hostrecords.png
  [editor de archivos de zona]: ./media/web-sites-custom-domain-name/e-editrecords.png
  [1]: ../includes/custom-dns-web-site-enable-on-web-site.md
