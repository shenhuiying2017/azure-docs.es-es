<properties 
	pageTitle="Control del tráfico de Sitios web Azure con el Administrador de tráfico de Azure" 
	description="Este artículo proporciona información de resumen para el Administrador de tráfico de Azure en lo referente a los sitios Web de Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

# Control del tráfico de Sitios web Azure con el Administrador de tráfico de Azure

> [AZURE.NOTE] Este artículo proporciona información resumida acerca del Administrador de tráfico de Azure en su relación con Sitios web Azure. Puede encontrar más información sobre el Administrador de tráfico de Azure en los vínculos que aparecen al final de este artículo.

## Introducción
Puede utilizar el Administrador de tráfico de Azure para controlar la manera en que las solicitudes de los clientes web se distribuyen a Sitios web Azure. Cuando se agregan extremos de un sitio web de Azure a un perfil del Administrador de tráfico de Azure, este hace un seguimiento del estado de sus sitios web (en ejecución, detenidos o eliminados) para decidir cuáles de esos extremos debe recibir tráfico.

## Métodos de equilibrio de carga
El Administrador de tráfico de Azure utiliza tres métodos de equilibrio de carga distintos. Estos métodos se describen en la siguiente lista, porque pertenecen a Sitios web Azure. 

* **Conmutación por error**: Si tiene clones de sitio web en distintas regiones, puede utilizar este método para configurar un sitio web para atender todo el tráfico del cliente web y configurar otro sitio web en una región distinta para ocuparse de ese tráfico en caso de que el primer sitio web deje de estar disponible. 
	
* **Round robin**: Si tiene clones de sitio web en distintas regiones, puede utilizar este método para distribuir el tráfico de manera equitativa entre los sitios web en distintas regiones. 
	
* **Rendimiento**: El método Rendimiento distribuye el tráfico según el tiempo de ida y vuelta más breve para los clientes. El método Rendimiento también se puede utilizar para sitios web dentro de la misma región o en regiones distintas. 

Para obtener información detallada acerca del equilibrio de carga en el Administrador de tráfico de Azure, consulte [Acerca de los métodos de equilibrio de carga de Traffic Manager](http://msdn.microsoft.com/library/windowsazure/dn339010.aspx).

##Sitios web Azure y perfles de Traffic Manager 
Para configurar el control del tráfico de sitio web, puede crear un perfil en el Administrador de tráfico de Azure que utilice uno de los tres métodos de equilibrio de carga anteriormente descritos y, a continuación, agregar los extremos (en este caso, los sitios web) para los que desea controlar el tráfico al perfil. El estado del sitio web (en ejecución, detenido o eliminado) se comunica de manera regular al perfil para que el Administrador de tráfico de Azure pueda dirigir el tráfico como corresponda.

Cuando utilice el Administrador de tráfico de Azure con Azure, tenga en cuenta los siguientes puntos:

* Para las implementaciones de solo sitio web dentro de la misma región, Sitios web Azure ya proporciona la funcionalidad de conmutación por error y de round-robin independientemente del modo del sitio web.

* En el caso de las implementaciones en la misma región que utilizan Sitios web Azure en conjunto con otro servicio en la nube de Azure, puede combinar ambos tipos de extremos para habilitar escenarios híbridos.

* Solo puede especificar un extremo de sitio web por región en un perfil. Cuando selecciona un sitio web como un extremo para una región, los sitios web restantes de esa región dejan de estar disponibles para su selección para ese perfil.

* Los extremos de sitio web que especifica en un perfil del Administrador de tráfico de Azure aparecerán en la sección **Nombres de dominio** de la página de configuración de los sitios web del perfil, pero no se configurarán ahí.

* Después de agregar un sitio web a un perfil, la **Dirección URL del sitio** del panel de la página portal del sitio web mostrará la dirección URL del dominio personalizado del sitio web si ha configurado alguno. De lo contrario, mostrará la dirección URL del perfil de el Administrador de tráfico (por ejemplo,  `contoso.trafficmgr.com`). Tanto el nombre de dominio directo del sitio web como la dirección URL del Administrador de tráfico serán visibles en la página de configuración del sitio web en la sección **Nombres de dominio**.

* Los nombres de dominio personalizado funcionarán tal como se esperan, pero además de agregarlos a los sitios web, también deberá configurar la asignación de DNS para que apunte a la dirección URL del Administrador de tráfico. Para obtener información sobre cómo configurar un dominio personalizado para un sitio web de Azure, consulte [Configuración de un nombre de dominio personalizado para un sitio web de Azure](https://www.windowsazure.com/es-es/documentation/articles/web-sites-custom-domain-name/).

* Solo podrá agregar sitios web que estén en modo estándar a un perfil del Administrador de tráfico de Azure.

## Pasos siguientes

Si desea obtener información general de carácter técnico y conceptual del Administrador de tráfico de Azure, consulte [Información general sobre Traffic Manager](http://msdn.microsoft.com/library/windowsazure/hh744833.aspx). 

Para obtener información sobre cómo configurar el Administrador de tráfico de Azure, incluido para el uso de Sitios web Azure, consulte [Tareas de configuración de Traffic Manager](http://msdn.microsoft.com/library/windowsazure/hh744830.aspx).

Para obtener información detallada acerca del equilibrio de carga en el Administrador de tráfico de Azure, consulte [Acerca de los métodos de equilibrio de carga de Traffic Manager](http://msdn.microsoft.com/library/windowsazure/dn339010.aspx).

Para obtener más información sobre el uso del Administrador de tráfico con Sitios web Azure, consulte las publicaciones del blog 
[Using Azure Traffic Manager with Azure Web Sites](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) y [Azure Traffic Manager can now integrate with Azure Web sites](http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).




<!--HONumber=42-->
