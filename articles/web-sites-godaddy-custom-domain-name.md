<properties title="Learn how to configure an Azure website to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Configuración de un nombre de dominio personalizado para un sitio web de Azure (GoDaddy).

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Dominio personalizado">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Sitios web" class="current">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Sitio web mediante el Administrador de tr&aacute;fico">Sitio web mediante el Administrador de tr&aacute;fico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][]]

[WACOM.INCLUDE [intro][]]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Go Daddy][] con Sitios web Azure.

[WACOM.INCLUDE [introfooter][]]

En este artículo:

-   [Descripción de los registros DNS][]
-   [Incorporación de un registro DNS para el dominio personalizado][]
-   [Habilitación del dominio en su sitio web][]

## <a name="understanding-records"></a>Descripción de los registros DNS

[WACOM.INCLUDE [understandingdns][]]

## <a name="bkmk_configurecname"></a>Incorporación de un registro DNS para el dominio personalizado

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por GoDaddy. Utilice los pasos siguientes para localizar las herramientas DNS de GoDaddy.com.

1.  Inicie sesión en la cuenta con GoDaddy.com, seleccione **My Account** y, a continuación, **Manage my domains**. Finalmente, seleccione el nombre del dominio que desee usar con el sitio web de Azure en el menú desplegable y elija **Manage DNS**..

    ![Página de dominio personalizado para GoDaddy][]

2.  En la página **Domain details**, desplácese hasta la pestaña **DNS Zone File**. Esta es la sección que se utiliza para agregar y modificar los registros DNS para el nombre de dominio.

    ![Pestaña de archivo de zona DNS][]

    Seleccione **Add Record** para agregar un registro existente.

    Seleccione el icono de lápiz y papel junto al registro para **editar** un registro existente.

    > [WACOM.NOTE] Antes de agregar registros nuevos, tenga en cuenta que GoDaddy ya ha creado registros DNS de subdominios populares (llamados **Host** en el editor), como **email**, **files**, **mail** y otros. Si el nombre que desea utilizar ya existe, modifique el registro actual en lugar de crear uno nuevo.

3.  Al agregar un registro, primero debe seleccionar el tipo de registro.

    ![seleccione el tipo de registro][]

    A continuación, proporcione el **Host** (dominio o subdominio personalizado) y a qué **Points to**.

    ![agregue un registro de zona.][]

    -   Al agregar un registro **A (host)**, se debe establecer el campo **Host** en [@][] (esto representa el nombre de dominio raíz, como **contoso.com**), \* (un comodín para que se corresponda con varios subdominios) o el subdominio que desea utilizar (por ejemplo, **www**). Debe establecer el campo **Points to** en la dirección IP de su sitio web de Azure.

        > [WACOM.NOTE] Si utiliza registros A (host), tiene que agregar también un registro CNAME con una de las configuraciones siguientes:
        >
        > -   Un valor **Host** de **awverify** con un valor **Points to** de **awverify.\<yourwebsitename\>.azurewebsites.net**.
        >
        > Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

    -   Al agregar un **registro CNAME (alias)** debe configurar el campo **Host** con el subdominio que desee usar. Por ejemplo, **www**. Debe definir el campo **Points to** con el nombre de dominio **.azurewebsites.net** de su sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**.

4.  Cuando haya terminado de agregar o modificar los registros, haga clic en **Finish** para guardar los cambios.

## <a name="enabledomain"></a>Habilitación del nombre de dominio en su sitio web

[WACOM.INCLUDE [modes][]]

  [Dominio personalizado]: /es-es/documentation/articles/web-sites-custom-domain-name "Dominio personalizado"
  [GoDaddy]: /es-es/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /es-es/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /es-es/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /es-es/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /es-es/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /es-es/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Sitio web]: /es-es/documentation/articles/web-sites-godaddy-custom-domain-name/ "Sitios web"
  [Sitio web mediante el Administrador de tráfico]: /es-es/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/ "Sitio web mediante el Administrador de tráfico"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [Go Daddy]: https://godaddy.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Descripción de los registros DNS]: #understanding-records
  [Incorporación de un registro DNS para el dominio personalizado]: #bkmk_configurecname
  [Habilitación del dominio en su sitio web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [Página de dominio personalizado para GoDaddy]: ./media/web-sites-custom-domain-name/godaddy-customdomain.png
  [Pestaña de archivo de zona DNS]: ./media/web-sites-custom-domain-name/godaddy-zonetab.png
  [seleccione el tipo de registro]: ./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png
  [agregue un registro de zona.]: ./media/web-sites-custom-domain-name/godaddy-addzonerecord.png
  [@]: mailto:**@*
  [modes]: ../includes/custom-dns-web-site-enable-on-web-site.md
