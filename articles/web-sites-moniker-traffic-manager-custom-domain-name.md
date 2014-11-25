<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Websites, Moniker, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# Configuración de un nombre de dominio personalizado para un sitio web de Azure con el Administrador de tráfico (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Dominio personalizado">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name/" title="Sitios web">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Sitio web mediante el Administrador de tr&aacute;fico" class="current">Sitio web mediante el Administrador de tr&aacute;fico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Moniker][1] con Sitios web Azure.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

En este artículo:

-   [Descripción de los registros DNS][Descripción de los registros DNS]
-   [Configuración de los sitios web para el modo estándar][Configuración de los sitios web para el modo estándar]
-   [Incorporación de un registro DNS para el dominio personalizado][Incorporación de un registro DNS para el dominio personalizado]
-   [Habilitación del Administrador de tráfico para el sitio web][Habilitación del Administrador de tráfico para el sitio web]

## <a name="understanding-records"></a>Descripción de los registros DNS

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

## <a name="bkmk_configsharedmode"></a>Configuración de los sitios web para el modo estándar

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## Incorporación de un registro DNS para el dominio personalizado

</p>
Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por Moniker. Use los siguientes pasos para ubicar las herramientas DNS para Moniker.com.

1.  Inicie sesión en la cuenta con Moniker.com, seleccione **My Domains** y, a continuación, haga clic en **Manage Templates**.

    ![Página Mis dominios de Moniker][Página Mis dominios de Moniker]

2.  En la página **Zone Template Management**, seleccione **Create New Template**.

    ![Administración de plantillas de la zona de Moniker][Administración de plantillas de la zona de Moniker]

3.  Complete el nombre de plantilla en **Template Name**.

4.  A continuación, cree un registro DNS seleccionando primero el tipo de registro en **Record Type**. A continuación, complete el nombre de host en **Hostname** y la dirección en **Address**.

    ![Plantilla de zona de creación de Moniker][Plantilla de zona de creación de Moniker]

    -   Cuando agregue un registro CNAME, debe establecer el campo **Hostname** en el subdominio que desee usar. Por ejemplo, **www**. Debe configurar el campo **Address** con el nombre de dominio **.trafficmanager.net** del perfil del Administrador de tráfico que use con el sitio web de Azure. Por ejemplo, **contoso.trafficmanager.net**.

        > [WACOM.NOTE] Únicamente debe usar los registros CNAME al asociar el nombre de dominio personalizado a un sitio web cuya carga se equilibre usando el Administrador de tráfico.

5.  Haga clic en el botón **Add** para agregar la entrada.

6.  Una vez que se agreguen todas las entradas, haga clic en el botón **Save**.

7.  Seleccione **Domain Manager** para volver a la lista de dominios.

8.  Seleccione la casilla de verificación del dominio de destino y haga clic en **Manage Templates** de nuevo.

9.  Busque la nueva plantilla que creó en los pasos anteriores. A continuación, haga clic en el vínculo **place selected domains (1) into this Template**.

    ![Plantilla de zona de creación de Moniker][2]

## <a name="enabledomain"></a>Habilitación del Administrador de tráfico para el sitio web

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

  [Moniker]: /es-es/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [1]: https://moniker.com
  [Descripción de los registros DNS]: #understanding-records
  [Configuración de los sitios web para el modo estándar]: #bkmk_configsharedmode
  [Incorporación de un registro DNS para el dominio personalizado]: #bkmk_configurecname
  [Habilitación del Administrador de tráfico para el sitio web]: #enabledomain
  [Página Mis dominios de Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png
  [Administración de plantillas de la zona de Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png
  [Plantilla de zona de creación de Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png
  [2]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png
