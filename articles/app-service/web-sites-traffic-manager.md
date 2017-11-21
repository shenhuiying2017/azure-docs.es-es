---
title: "Control del tráfico de aplicaciones web de Azure con el Administrador de tráfico de Azure"
description: "En este artículo se proporciona información resumida acerca de Azure Traffic Manager en su relación con aplicaciones web de Azure."
services: app-service\web
documentationcenter: 
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.openlocfilehash: 5bf687afa8f42292a3b21b19a572c76926fef1cd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Control del tráfico de aplicaciones web de Azure con el Administrador de tráfico de Azure
> [!NOTE]
> En este artículo se proporciona información resumida acerca de Microsoft Azure Traffic Manager en su relación con Azure Web Apps. Puede encontrar más información sobre el Administrador de tráfico de Azure en los vínculos que aparecen al final de este artículo.
> 
> 

## <a name="introduction"></a>Introducción
Puede utilizar el Administrador de tráfico de Azure para controlar la manera en que se distribuyen solicitudes de clientes web a aplicaciones web de Azure App Service. Cuando se agregan puntos de conexión de una aplicación web a un perfil de Azure Traffic Manager, este hace un seguimiento del estado de las aplicaciones web (en ejecución, detenidas o eliminadas) para decidir cuáles de esos puntos de conexión debe recibir tráfico.

## <a name="routing-methods"></a>Métodos de enrutamiento
Azure Traffic Manager utiliza tres métodos de enrutamiento distintos. Estos métodos se describen en la siguiente lista según su relación con Azure Web Apps.

* **[Prioridad](#priority):** usa una aplicación web principal para todo el tráfico y proporciona copias de seguridad en caso de que las aplicaciones web principal o de copia de seguridad no estén disponibles.
* **[Ponderado](#weighted):** el tráfico se distribuye entre un conjunto de aplicaciones web, de manera uniforme o según los pesos definidos.
* **[Rendimiento](#performance):** cuando haya aplicaciones web en diferentes ubicaciones geográficas, use la aplicación web "más cercana" en cuanto a menor latencia de red.
* **[Geográfico](#geographic):** dirige a los usuarios a aplicaciones web específicas en función de la ubicación geográfica de origen de su consulta de DNS. 

Para más información, consulte [Métodos de enrutamiento de Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="web-apps-and-traffic-manager-profiles"></a>Web Apps y perfiles de Traffic Manager
Para configurar el control del tráfico de aplicación web, puede crear un perfil en el Administrador de tráfico de Azure que utilice uno de los tres métodos de equilibrio de carga anteriormente descritos y, a continuación, agregar los extremos (en este caso, las aplicaciones web) para los que desea controlar el tráfico al perfil. El estado de la aplicación web (en ejecución, detenido o eliminado) se comunica con frecuencia al perfil para que Azure Traffic Manager dirija el tráfico en consecuencia.

Cuando utilice el Administrador de tráfico de Azure con Azure, tenga en cuenta los siguientes puntos:

* Para las implementaciones de solo aplicación web dentro de la misma región, Azure Web Apps ya proporciona la funcionalidad de conmutación por error y de round-robin independientemente del modo de la aplicación web.
* En el caso de las implementaciones en la misma región que utilizan Web Apps en conjunto con otro servicio en la nube de Azure, puede combinar ambos tipos de extremos para habilitar escenarios híbridos.
* Solo puede especificar un extremo de aplicación web por región en un perfil. Cuando selecciona una aplicación web como un extremo para una región, las aplicaciones web restantes de esa región dejan de estar disponibles para su selección para ese perfil.
* Los puntos de conexión de aplicación web que especifica en un perfil del Azure Traffic Manager aparecen en la sección **Nombres de dominio** de la página Configuración de la aplicación web del perfil, pero no se configuran ahí.
* Después de agregar una aplicación web a un perfil, la **URL del sitio** del panel de la página del portal de la aplicación web muestra la dirección URL del dominio personalizado de la aplicación web si ha configurado alguna. De lo contrario, muestra la del perfil de Traffic Manager (por ejemplo, `contoso.trafficmanager.net`). Tanto el nombre de dominio directo de la aplicación web como la dirección URL de Traffic Manager se ven en la página Configuración de la aplicación web, en la sección **Nombres de dominio**.
* Los nombres de dominio personalizado funcionan según lo previsto, pero además de agregarlos a las aplicaciones web, también debe configurar la asignación de DNS para que apunte a la dirección URL de Traffic Manager. Para información sobre cómo configurar un dominio personalizado para un sitio web de Azure, consulte el artículo sobre la [configuración de un nombre de dominio personalizado para un sitio web de Azure](app-service-web-tutorial-custom-domain.md).
* Solo puede agregar aplicaciones web que estén en modo estándar o premium a un perfil de Azure Traffic Manager.

## <a name="next-steps"></a>Pasos siguientes
Si desea obtener información general de carácter técnico y conceptual del Administrador de tráfico de Azure, consulte [Información general sobre el Administrador de tráfico](../traffic-manager/traffic-manager-overview.md).

Para más información sobre el uso de Traffic Manager con Web Apps, consulte las publicaciones del blog [Using Azure Traffic Manager with Azure Web Sites](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) (Uso de Azure Traffic Manager con Azure Web Sites) y [Azure Traffic Manager can now integrate with Azure Web Sites](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/) (Azure Traffic Manager ya se puede integrar con Azure Web Sites).

