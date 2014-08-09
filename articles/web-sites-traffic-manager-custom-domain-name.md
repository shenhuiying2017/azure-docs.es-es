<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a custom domain name" pageTitle="Configure a custom domain name for an Azure web site that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuración de un nombre de dominio personalizado para un sitio web de Azure con el Administrador de tráfico
==============================================================================================================

[Dominio personalizado](/es-es/documentation/articles/web-sites-custom-domain-name "Dominio personalizado")[GoDaddy](/es-es/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/es-es/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/es-es/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/es-es/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/es-es/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sitios web](/es-es/documentation/articles/web-sites-custom-domain-name/ "Sitios web") | [Sitio web utilizando el Administrador de tráfico](/es-es/documentation/articles/web-sites-traffic-manager-custom-domain-name/ "Sitio web utilizando el Administrador de tráfico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

En este artículo se ofrecen instrucciones generales para utilizar un nombre de dominio personalizado con Sitios web Azure que usan Administrador de tráfico para el equilibrio de carga. Consulte las pestañas que aparecen en la parte superior de este articulo para comprobar si aparece su registrador de dominios. Si es así, seleccione la pestaña correspondiente para ver los pasos específicos de ese registrador en particular.

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

Para asociar su dominio personalizado con un Sitio web Azure, debe agregar una nueva entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador de dominios al que ha adquirido su nombre de dominio. Siga estos pasos para ubicar y utilizar las herramientas DNS.

1.  Inicie sesión en su cuenta en el registrador de dominios y busque la página de administración de los registros DNS. Busque los vínculos o áreas del sitio etiquetadas como **Domain Name**, **DNS** o **Name Server Management**. A menudo se puede encontrar un vínculo a esta página al consultar la información de la cuenta (suele poner algo parecido a **My domains**).

2.  Cuando haya encontrado la página de administración para su nombre de dominio, busque un vínculo que le permita modificar los registros DNS. Debe aparecer como **Zone file**, **DNS Records** o un vínculo de configuración a las opciones avanzadas (**Advanced**).

    -   Esta página seguramente mostrará algunos que ya se han creado, como una entrada en la que se asocia "**@**" o "\*" a una página donde figuran los dominios. Es posible también que contenga registros para los subdominios más comunes, como **www**.
    -   Esta página mencionará los **registros CNAME**, o bien facilitará una lista desplegable donde podrá seleccionar el tipo de registro. Es posible también que mencione otros registros, como los **registros D** y los **registros MX**. En algunos casos, los registros CNAME tendrán otros nombres (como **Alias Record**).
    -   Esta página contendrá también campos que le permiten **asignar** desde un **nombre de host** o un **nombre de dominio** hasta otro nombre de dominio.

3.  Aunque los detalles varían en función del registrador que se esté utilizando, en general se asigna *desde* el nombre del dominio personalizado (como **contoso.com**) *hasta* el nombre de dominio del Administrador de tráfico (**contoso.azurewebsites.net**) usado para el sitio web de Azure.

4.  Una vez que haya terminado de agregar o modificar registros DNS en su registrador, guarde los cambios.

Habilitación del Administrador de tráfico para el sitio web
-----------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

