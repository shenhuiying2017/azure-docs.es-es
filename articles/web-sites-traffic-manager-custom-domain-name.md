<properties 
	pageTitle="Configuración de un nombre de dominio personalizado para una aplicación web en el Servicio de aplicaciones de Azure que usa el Administrador de tráfico" 
	description="Use un nombre de dominio personalizado para un una aplicación web en el Servicio de aplicaciones de Azure que incluya el Administrador de tráfico para el equilibrio de carga." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

#Configuración de un nombre de dominio personalizado para una aplicación web en el Servicio de aplicaciones de Azure que usa el Administrador de tráfico

[AZURE.INCLUDE [web-selector](../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

En este artículo se ofrecen instrucciones generales para usar un nombre de dominio personalizado con el Servicio de aplicaciones de Azure que usa el Administrador de tráfico para el equilibrio de carga.

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

En este artículo:

-   [Descripción de los registros DNS](#understanding-records)
-   [Configuración de las aplicaciones web para el modo estándar](#bkmk_configsharedmode)
-   [Incorporación de un registro DNS al dominio personalizado](#bkmk_configurecname)
-   [Habilitación del Administrador de tráfico para la aplicación web](#enabledomain)

<a name="understanding-records"></a>
## Descripción de los registros DNS

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## Configuración de las aplicaciones web para el modo estándar

[AZURE.INCLUDE [modos](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## Incorporación de un registro DNS al dominio personalizado

Para asociar su dominio personalizado con una aplicación web del Servicio de aplicaciones de Azure, debe agregar una nueva entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador de dominios al que ha adquirido su nombre de dominio. Siga estos pasos para ubicar y utilizar las herramientas DNS.

1. Inicie sesión en su cuenta en el registrador de dominios y busque la página de administración de los registros DNS. Busque los vínculos o áreas del sitio etiquetados como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**. A menudo se puede encontrar un vínculo a esta página al consultar la información de la cuenta y al buscar un vínculo como **Mis dominios**.

4. Cuando haya encontrado la página de administración para su nombre de dominio, busque un vínculo que le permita modificar los registros DNS. Debe aparecer como **archivo Zona**, **Registros DNS** o un vínculo de configuración a **Opciones avanzadas**.

	* Esta página seguramente mostrará algunos que ya se han creado, como una entrada en la que se asocia "**@**" o "*" a una página  'domain parking'. También es posible que contenga registros para los subdominios más comunes, como **www**.
	* Esta página mencionará los **registros CNAME**, o bien facilitará una lista desplegable donde podrá seleccionar un tipo de registro. También es posible que mencione otros registros, como los **registros A** y los **registros MX**. En algunos casos, los registros CNAME tendrán otros nombres (como **Registro de Alias**.
	* Esta página contendrá también campos que le permiten **asignar** desde un **nombre de host** o un **nombre de dominio** hasta otro nombre de dominio.

5. Aunque los detalles de cada registrador varían, en general se asigna  *from* su nombre de dominio personalizado (como **contoso.com**,) *to* el nombre de dominio del Administrador de tráfico (**contoso.trafficmanager.net**) que usa para la aplicación web.

6. Una vez que haya terminado de agregar o modificar registros DNS en su registrador, guarde los cambios.

<a name="enabledomain"></a>
## Habilitación del Administrador de tráfico

[AZURE.INCLUDE [modos](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

>[AZURE.NOTE] Si quiere empezar a trabajar con el servicio de aplicaciones de Azure antes de contratar una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web inicial de corta duración en el servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía para cambiar de sitios web al servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía para cambiar del portal antiguo al nuevo portal, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--HONumber=52-->