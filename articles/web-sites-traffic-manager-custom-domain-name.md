<properties 
	pageTitle="Configuración de un nombre de dominio personalizado para un sitio web de Azure que utiliza el Administrador de tráfico" 
	description="" 
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
	ms.author="larryfr, jroth"/>

#Configuración de un nombre de dominio personalizado para un sitio web de Azure con el Administrador de tráfico

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-custom-domain-name/" title="Websites">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Sitio web utilizando el Administrador de tráfico</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

En este artículo se ofrecen instrucciones generales para utilizar un nombre de dominio personalizado con Sitios web Azure que usan Administrador de tráfico para el equilibrio de carga. Consulte las pestañas que aparecen en la parte superior de este articulo para comprobar si aparece su registrador de dominios. Si es así, seleccione la pestaña correspondiente para ver los pasos específicos de ese registrador en particular.

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

En este artículo:

-   [Descripción de los registros DNS](#understanding-records)
-   [Configuración de los sitios web para el modo estándar](#bkmk_configsharedmode)
-   [Incorporación de un registro DNS para el dominio personalizado](#bkmk_configurecname)
-   [Habilitación del Administrador de tráfico para el sitio web](#enabledomain)

<h2><a name="understanding-records"></a>Descripción de los registros DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Configuración de los sitios web para el modo estándar</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Incorporación de un registro DNS para el dominio personalizado</h2>

Para asociar su dominio personalizado con un Sitio web Azure, debe agregar una nueva entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador de dominios al que ha adquirido su nombre de dominio. Siga estos pasos para ubicar y utilizar las herramientas DNS.

1. Inicie sesión en su cuenta en el registrador de dominios y busque la página de administración de los registros DNS. Busque los vínculos o áreas del sitio etiquetadas como **Nombre de dominio****, DNS** o **Administración del servidor de nombres**. A menudo se puede encontrar un vínculo a esta página al consultar la información de la cuenta (suele poner algo parecido a **My domains**).

4. Cuando haya encontrado la página de administración para su nombre de dominio, busque un vínculo que le permita modificar los registros DNS. Debe aparecer como **Archivo de zona****, Registros DNS** o un vínculo de configuración a las opciones avanzadas (**Opciones avanzadas**).

	* La página seguramente ya tendrá algunos registros creados, por ejemplo, una asociación de entrada '**@**' o '\*' con una página 'domain parking'. Es posible también que contenga registros para los subdominios más comunes, como **www**.
	* Esta página mencionará los **registros CNAME**, o bien facilitará una lista desplegable donde podrá seleccionar el tipo de registro. Es posible también que mencione otros registros, como los **registros D** y los **registros MX**. En algunos casos, los registros CNAME tendrán otros nombres (como **Alias Record**).
	* Esta página contendrá también campos que le permiten **asignar** desde un **nombre de host** o un **nombre de dominio** hasta otro nombre de dominio.

5. Aunque los detalles varían en función del registrador que se esté utilizando, en general se asigna *from* el nombre del dominio personalizado (como **contoso.com**) *to* el nombre de dominio del Administrador de tráfico (**contoso.azurewebsites.net**) que se usa para el Sitio web Azure.

6. Una vez que haya terminado de agregar o modificar registros DNS en su registrador, guarde los cambios.

<h2><a name="enabledomain"></a>Habilitación del sitio web del Administrador de tráfico</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]





<!--HONumber=42-->
