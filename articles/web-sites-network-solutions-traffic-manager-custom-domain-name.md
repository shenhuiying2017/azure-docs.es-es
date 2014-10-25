<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure website that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Configuración de un nombre de dominio personalizado para un sitio web de Azure con el Administrador de tráfico (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Dominio personalizado">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Sitios web">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Sitio web mediante el Administrador de tr&aacute;fico" class="current">Sitio web mediante el Administrador de tr&aacute;fico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][]]

[WACOM.INCLUDE [intro][]]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Network Solutions][1] con Sitios web Azure.

[WACOM.INCLUDE [tmwebsitefooter][]]

[WACOM.INCLUDE [introfooter][]]

En este artículo:

-   [Descripción de los registros DNS][]
-   [Configuración de los sitios web para el modo estándar][]
-   [Incorporación de un registro DNS para el dominio personalizado][]
-   [Habilitación del Administrador de tráfico para el sitio web][]

## <a name="understanding-records"></a>Descripción de los registros DNS

[WACOM.INCLUDE [understandingdns][]]

## <a name="bkmk_configsharedmode"></a>Configuración de los sitios web para el modo estándar

[WACOM.INCLUDE [modes][]]

<a name="bkmk_configurecname"></a>

## Incorporación de un registro DNS para el dominio personalizado

</p>
Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por Network Solutions. Siga estos pasos para ubicar y utilizar las herramientas DNS.

1.  Inicie sesión en la cuenta en networksolutions.com y seleccione **My Account** en la esquina superior derecha.

2.  En la pestaña **My Products and Services**, seleccione **Edit DNS**.

    ![Página de edición de DNS][]

3.  En la sección **Manage <yourdomainname>** de la página **Domain Names**, seleccione **Edit Advanced DNS Records**.

    ![Página de nombres de dominio con edición de registros DNS avanzados resaltada][]

4.  La página **Update Advanced DNS** contiene una sección para cada tipo de registro con un botón **Edit** debajo de cada sección. Para los registros CNAME, use la sección **Host Alias (CNAME Records)**.

    ![Página de actualización de DNS avanzada][]

5.  Cuando haga clic en el botón **Edit**, se mostrará un formulario que puede usar para modificar los registros existentes o agregar nuevos.

    Cuando agregue un registro CNAME, debe establecer el campo **Alias** en el subdominio que desee usar. Por ejemplo, **www**. Debe seleccionar el campo del círculo junto al campo **Other host** y establecer **Other host** en el nombre de dominio **.trafficmanager.net** del perfil del Administrador de tráfico usado con el sitio web de Azure. Por ejemplo, **contoso.trafficmanager.net**. Deje **Refers to Host Name** como **Select**, ya que este campo no es obligatorio cuando se crea un registro CNAME para usarlo con Sitios web Azure.

    ![Formulario CNAME][]

6.  Cuando haya acabado de agregar o modificar registros, haga clic en **Continue** para revisar los cambios. Seleccione **Save changes only** para guardar los cambios.

## <a name="enabledomain"></a>Habilitación del Administrador de tráfico para el sitio web

[WACOM.INCLUDE [modes][2]]

  [Dominio personalizado]: /es-es/documentation/articles/web-sites-custom-domain-name "Dominio personalizado"
  [GoDaddy]: /es-es/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /es-es/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /es-es/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /es-es/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /es-es/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /es-es/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Sitio web]: /es-es/documentation/articles/web-sites-network-solutions-custom-domain-name/ "Sitios web"
  [Sitio web mediante el Administrador de tráfico]: /es-es/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/ "Sitio web mediante el Administrador de tráfico"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [1]: https://www.networksolutions.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Descripción de los registros DNS]: #understanding-records
  [Configuración de los sitios web para el modo estándar]: #bkmk_configsharedmode
  [Incorporación de un registro DNS para el dominio personalizado]: #bkmk_configurecname
  [Habilitación del Administrador de tráfico para el sitio web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Página de edición de DNS]: ./media/web-sites-custom-domain-name/ns-editdns.png
  [Página de nombres de dominio con edición de registros DNS avanzados resaltada]: ./media/web-sites-custom-domain-name/ns-editadvanced.png
  [Página de actualización de DNS avanzada]: ./media/web-sites-custom-domain-name/ns-updatecnameadvanced.png
  [Formulario CNAME]: ./media/web-sites-custom-domain-name/ns-cnametm.png
  [2]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
