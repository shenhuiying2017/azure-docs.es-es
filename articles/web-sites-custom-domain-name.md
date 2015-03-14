<properties 
	pageTitle="Configuración de un nombre de dominio personalizado para un sitio web de Azure" 
	description="Aprenda a usar un nombre de dominio personalizado con Sitios web Azure" 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr, jroth, mwasson"/>

#Configuración de un nombre de dominio personalizado para un sitio web de Azure.

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-custom-domain-name/" title="Websites" class="current">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sitio web con Administrador de tráfico</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

Cuando se crea un sitio web, Azure lo asigna a un subdominio de azurewebsites.net. Por ejemplo, si su sitio web se denomina **contoso**, la dirección URL sería **contoso.azurewebsites.net**. Azure también asigna una dirección IP virtual.

![subdominio contoso.azurewebsites.net][subdominio]

Para un sitio web de producción, es probable que desee que los usuarios vean un nombre de dominio personalizado. Este artículo explica cómo configurar un dominio personalizado con Sitios web de Azure. (En este artículo proporciona instrucciones generales para cualquier registrador de dominios. Las pestañas que aparecen en la parte superior de este articulo se vinculan a artículos para registradores específicos).

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

En este artículo:


-   [Información general]
-   [Tipos de registro DNS]
-   [Búsqueda de la dirección IP virtual]
-   [Creación de registros DNS]
-   [Creación de un registro "awverify" (solo registros A)](#awverify)
-   [Habilitación del nombre de dominio en su sitio web]


## Información general

Aquí se describen los pasos generales para configurar un nombre de dominio personalizado:

1. Reserve el nombre de dominio. En este artículo no se cubre ese proceso. Hay varios registradores de dominios entre los que elegir. Al registrar, el sitio le guiará por el proceso.
1. Cree registros DNS que asignen el dominio a su sitio web de Azure. 
1. Agregue el nombre de dominio en el Portal de administración de Azure. 

En esta descripción básica, hay algunos casos específicos que se deben tener en cuenta:

- Asignación de su dominio raíz. El dominio raíz es el dominio que reservó con el registrador de dominios. Por ejemplo, **contoso.com**.
- Asignación de un subdominio. Por ejemplo, **blogs.contoso.com**.  Puede asignar subdominios diferentes a sitios web diferentes.
- Asignación de un comodín. Por ejemplo, ***.contoso.com**. Una entrada comodín se aplica a todos los subdominios del dominio. 
 
[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]


## Tipos de registro DNS

El sistema de nombres de dominio (DNS) usa registros de datos para asignar nombres de dominio en direcciones IP. Existen varios tipos de registros DNS: Para los sitios web, podrá crear un registro *A* o un registro *CNAME*.

- Un registro A **(Address)** asigna un nombre de dominio a una dirección IP. 
- Un registro **CNAME (Canonical Name)** asigna un nombre de dominio a otro nombre de dominio. DNS usa el segundo nombre para buscar la dirección. Los usuarios ven el primer nombre de dominio en su explorador. Por ejemplo, podría asignar contoso.com a *&lt;susitio&gt;*.azurewebsites.net. 

Si cambia la dirección IP, una entrada CNAME aún es válida, mientras que un registro A se debe actualizar. Sin embargo, algunos registradores de dominios no permiten registros CNAME para el dominio raíz o para los dominios comodín. En dicho caso, debe usar un registro A. 

> [AZURE.NOTE] La dirección IP puede cambiar si elimina y vuelve a crear el sitio web, o si vuelve a cambiar el modo de sitio web a Gratis.


## Búsqueda de la dirección IP virtual 

Si está creando un registro CNAME, omita este paso. Para crear un registro A, necesitará la dirección IP virtual de su sitio web. Para obtener la dirección IP:

1.	En el explorador, abra el [Portal de administración de Azure](https://manage.windowsazure.com).
2.	En la pestaña **Sitios web**, haga clic en el nombre del sitio y seleccione **Panel**.
3.	Seleccione **Administrar dominios** en la parte inferior de la página. (Si esta opción está deshabilitada, asegúrese de usar el modo Compartido, Básico o Estándar. Para obtener más información, consulte [Escalación de sitios web](http://azure.microsoft.com/documentation/articles/web-sites-scale/)). 

	![](media/web-sites-custom-domain-name/dncmntask-cname-6.png)

4.	La dirección IP se muestra en la parte inferior del cuadro de diálogo.

	![](media/web-sites-custom-domain-name/ipaddress.png)

## Creación de registros DNS

Inicie sesión en el registrador de dominios y use su herramienta para agregar un registro a o un registro CNAME. Los sitios web de los registradores son diferentes, pero a continuación se proporcionan algunas pautas generales. 

1.	Busque la página de administración de registros DNS. Busque los vínculos o áreas del sitio etiquetadas como **Domain Name**, **DNS** o **Name Server Management**. A menudo el vínculo se encuentra al consultar la información de la cuenta (suele poner algo parecido a **Mis dominios**).
2.	Cuando haya encontrado la página de administración, busque un vínculo que le permita agregar o editar registros DNS. Debe aparecer como **Archivo de zona****, Registros DNS** o un vínculo de configuración a las opciones avanzadas (**Opciones avanzadas**).

Esta página podría indicar los registros A y los registros CNAME, o bien ofrecer una lista desplegable donde podrá seleccionar el tipo de registro. Además, es posible que use otros nombres para los tipos de registro, tal como **IP Address record** instead of A record o **Alias Record** en lugar del registro CNAME.  Normalmente, el registrador crea los registros para usted. Por lo tanto, es posible que ya existan registros para el dominio raíz o para los subdominios comunes, tal como **www**.

Al crear o editar un registro, los campos le permitirán asignar su nombre de dominio a una dirección IP (para registros A) u otro dominio (para registros CNAME). Para un registro CNAME, tendrá que asignar *from* su dominio personalizado *to* su subdominio azurewebsites.net. 

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

Suponiendo que el nombre de dominio personalizado fuera 'contoso.com', se crearían los registros siguientes:

- **contoso.com** asignado a 127.0.0.1.
- **www.contoso.com** asignado a **contoso.azurewebsites.net**.


<h2 id="awverify">Creación de un registro "awverify" (solo registros A)</h2>

Si crea un registro A, Sitios web de Azure también requiere un registro CNAME especial, que se utiliza para verificar que se posee el dominio que está intentando utilizar. Este registro CNAME debe tener el formato siguiente. 

- *Si el registro A asigna el dominio raíz o un dominio comodín:* cree un registro CNAME que asigne desde **awverify.&lt;sudominio&gt;** a **awverify.&lt;nombredesusitioweb&gt;.azurewebsites.net**. Por ejemplo, si el registro A es para **contoso.com**, cree un registro CNAME para **awverify.contoso.com**.
- *Si el registro asigna un subdominio específico:* cree un registro CNAME que asigne desde **awverify.&lt;subdominio&gt;** a **awverify.&lt;nombredesusitioweb&gt;.azurewebsites.net**. Por ejemplo, si el registro A fuera para **blogs.contoso.com**, cree un registro CNAME para **awverify.blogs.contoso.com**.

Los visitantes a su sitio no verán el subdominio awverify, este solo es para que Azure compruebe su dominio.

## Habilitación del nombre de dominio en su sitio web

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


<!-- Anchors. -->
[Información general]: #overview
[Tipos de registro DNS]: #dns-record-types
[Búsqueda de la dirección IP virtual]: #find-the-virtual-ip-address
[Creación de registros DNS]: #create-the-dns-records
[Habilitación del nombre de dominio en su sitio web]: #enable-the-domain-name-on-your-website

<!-- Images -->
[subdominio]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png



<!--HONumber=42-->
