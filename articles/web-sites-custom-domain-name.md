<properties
	pageTitle="Configurar un nombre de dominio personalizado en el servicio de aplicaciones de Azure"
	description="Obtenga información sobre cómo usar un nombre de dominio personalizado con una aplicación web en el servicio de aplicaciones de Azure."
	services="app-service\web"
	documentationCenter=""
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="mwasson"/>

# Configurar un nombre de dominio personalizado en el servicio de aplicaciones de Azure

<div class="dev-center-tutorial-selector sublanding">
  <a href="/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Dominio personalizado</a>
  <a href="/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a>
  <a href="/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a>
  <a href="/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a>
  <a href="/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a>
  <a href="/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a>
  <a href="/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a>
  <a href="/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a>
  <a href="/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a>
</div>
<div class="dev-center-tutorial-subselector">
  <a href="/documentation/articles/web-sites-custom-domain-name/" title="Websites" class="current">Aplicaciones web</a> | <a href="/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Web Apps using Traffic Manager">Aplicaciones web con Administrador de tráfico</a>
</div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

Cuando crea una aplicación web, Azure la asigna a un subdominio de azurewebsites.net. Por ejemplo, si la aplicación web se denomina **contoso**, la dirección URL es **contoso.azurewebsites.net**. Azure también asigna una dirección IP virtual.

<!--todo:![contoso.azurewebsites.net subdomain][subdomain]-->

Para un sitio web de producción, probablemente quiera que los usuarios vean un nombre de dominio personalizado. En este artículo se explica cómo configurar un dominio personalizado con [aplicaciones web del servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714). (En este artículo proporciona instrucciones generales para cualquier registrador de dominios. Las pestañas que aparecen en la parte superior de este articulo se vinculan a artículos para registradores específicos).

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

En este artículo:


-   [Información general]
-   [Tipos de registro DNS]
-   [Búsqueda de la dirección IP virtual]
-   [Creación de registros DNS]
-   [Creación de un registro "awverify" (solo registros A)](#awverify)
-   [Habilitación del nombre de dominio en su aplicación web]


## Información general

Aquí se describen los pasos generales para configurar un nombre de dominio personalizado:

1. Reserve el nombre de dominio. En este artículo no se cubre ese proceso. Hay varios registradores de dominios entre los que elegir. Al registrar, el sitio le guiará por el proceso:
1. Cree registros DNS que asignen el dominio a su aplicación web de Azure.
1. Agregue el nombre de dominio en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

En esta descripción básica, hay algunos casos específicos que se deben tener en cuenta:

- Asignación de su dominio raíz. El dominio raíz es el dominio que reservó con el registrador de dominios. Por ejemplo, **contoso.com**.
- Asignación de un subdominio. Por ejemplo, **blogs.contoso.com**.  Puede asignar distintos subdominios a aplicaciones web diferentes.
- Asignación de un comodín. Por ejemplo, ***.contoso.com**. Una entrada comodín se aplica a todos los subdominios del dominio.

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]


## Tipos de registro DNS

El sistema de nombres de dominio (DNS) usa registros de datos para asignar nombres de dominio en direcciones IP. Existen varios tipos de registros DNS: En las aplicaciones web, podrá crear un registro  *A* o un registro  *CNAME*.

- Un registro A **(Dirección)** asigna un nombre de dominio a una dirección IP.
- Un registro **CNAME (Nombre canónico)** asigna un nombre de dominio a otro nombre de dominio. DNS usa el segundo nombre para buscar la dirección. Los usuarios ven el primer nombre de dominio en su explorador. Por ejemplo, podría asignar contoso.com a *&lt;yourwebapp&gt;*.azurewebsites.net.

Si cambia la dirección IP, una entrada CNAME aún es válida, mientras que un registro A se debe actualizar. Sin embargo, algunos registradores de dominios no permiten registros CNAME para el dominio raíz o para los dominios comodín. En dicho caso, debe usar un registro A.

> [AZURE.NOTE] La dirección IP puede cambiar si elimina y vuelve a crear la aplicación web o si vuelve a cambiar el modo de la aplicación web a libre.


## Búsqueda de la dirección IP virtual

Si está creando un registro CNAME, omita este paso. Para crear un registro A, necesita la dirección IP virtual de su aplicación web. Para obtener la dirección IP:

1.	En el explorador, abra el [Portal de administración de Azure](https://portal.azure.com).
2.	Haga clic en la opción **Examinar** del lado izquierdo de la página.
3.	Haga clic en la hoja **Aplicaciones web**.
4.	Haga clic en el nombre de la aplicación web.
5.	En la página **Essentials**, haga clic en **Toda la configuración**.
6.	Haga clic en **Dominios personalizados y SSL**. La dirección IP se encuentra en la parte inferior de la página (justo encima de la sección **Enlaces SSL**).

## Creación de registros DNS

Inicie sesión en el registrador de dominios y use su herramienta para agregar un registro a o un registro CNAME. La aplicación web de los registradores es un poco diferente, pero a continuación se proporcionan algunas pautas generales.

1.	Busque la página de administración de registros DNS. Busque los vínculos o áreas del sitio etiquetadas como **Nombre de dominio**, **DNS** o **Administración del servidor del nombres** A menudo, el vínculo se encuentra al consultar la información de la cuenta y al buscar un vínculo como **Mis dominios**.
2.	Cuando haya encontrado la página de administración, busque un vínculo que le permita agregar o editar registros DNS. Debe aparecer como **archivo Zona**, **Registros DNS** o un vínculo de configuración a **Opciones avanzadas**.

Esta página podría indicar los registros A y los registros CNAME, o bien ofrecer una lista desplegable donde podrá seleccionar el tipo de registro. Además, es posible que use otros nombres para los tipos de registro, tal como **registros Dirección IP** en vez de registro A o **registro Alias** en lugar del registro CNAME.  Normalmente, el registrador crea los registros para usted. Por lo tanto, es posible que ya existan registros para el dominio raíz o para los subdominios comunes, tal como **www**.

Al crear o editar un registro, los campos le permitirán asignar su nombre de dominio a una dirección IP (para registros A) u otro dominio (para registros CNAME). Para un registro CNAME, asignará elementos  *from* su dominio personalizado  *to* su subdominio azurewebsites.net.

En muchas herramientas de los registradores, simplemente escribirá la parte de subdominio del dominio, no el nombre completo del dominio. Además, muchas herramientas usan '@' para representar el dominio raíz. Por ejemplo:

<table cellspacing="0" border="1">
  <tr>
    <th>Host</th>
    <th>Tipo de registro</th>
    <th>Dirección IP o dirección URL</th>
  </tr>
  <tr>
    <td>@</td>
    <td>A (dirección)</td>
    <td>127.0.0.1</td>
  </tr>
  <tr>
    <td>www</td>
    <td>CNAME (alias)</td>
    <td>contoso.azurewebsites.net</td>
  </tr>
</table>

Suponiendo que el nombre de dominio personalizado es 'contoso.com', esto crearía los registros siguientes:

- **contoso.com** asignado a 127.0.0.1.
- **www.contoso.com** asignado a **contoso.azurewebsites.net**.


<h2 id="awverify">Creación de un registro "awverify" (solo registros A)</h2>

Si crea un registro A, la aplicación web también requiere un registro CNAME especial, que se usa para comprobar es propietario del dominio que está intentando utilizar. Este registro CNAME debe tener el formato siguiente.

- *Si el registro A se asigna al dominio raíz o a un dominio comodín:* Cree un registro CNAME que asigna de **awverify.&lt;yourdomain&gt;** a **awverify.&lt;yourwebappname&gt;. azurewebsites.net**.  Por ejemplo, si el registro A es para **contoso.com**, cree un registro CNAME para **awverify.contoso.com**.
- *Si el registro A se asigna a un subdominio específico:* Cree un registro CNAME que asigna de **awverify.&lt;subdomain&gt;** a **awverify.&lt;yourwebappname&gt;. azurewebsites.net**. Por ejemplo, si el registro A es para **blogs.contoso.com**, cree un registro CNAME para **awverify.blogs.contoso.com**.

Los visitantes de su aplicación web no verán el subdominio awverify, este solo es para que Azure compruebe su dominio.

## Habilitación del nombre de dominio en su aplicación web

[AZURE.INCLUDE [modos](../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Si quiere empezar a trabajar con el servicio de aplicaciones de Azure antes de contratar una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de inicio de corta duración en el servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener instrucciones sobre el cambio de sitios web al servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener instrucciones sobre el cambio del portal antiguo al nuevo portal, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Anchors. -->
[Información general]: #overview
[Tipos de registro DNS]: #dns-record-types
[Búsqueda de la dirección IP virtual]: #find-the-virtual-ip-address
[Creación de registros DNS]: #create-the-dns-records
[Habilitación del nombre de dominio en su aplicación web]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!--HONumber=49-->