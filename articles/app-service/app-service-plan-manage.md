---
title: "Administración de un plan de App Service en Azure | Microsoft Docs"
description: "Aprenda cómo realizan los planes de App Service distintas tareas para administrar un plan de App Service."
keywords: "servicio de aplicaciones, azure app service, escalar, plan de app service, cambiar, crear, administrar, administración"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 61179c5bf29ed2c338b45ba909ec01237806cf26
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="manage-an-app-service-plan-in-azure"></a>Administración de un plan de App Service en Azure

Un [plan de App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) proporciona los recursos que debe ejecutar una aplicación de App Service. Esta guía muestra cómo administrar un plan de App Service.

## <a name="create-an-app-service-plan"></a>Creación de un plan de App Service

> [!TIP]
> Si no cuenta con App Service Environment, consulte [Creación de un plan de App Service en App Service Environment](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Puede crear un plan de App Service vacío o como parte de la creación de la aplicación.

En [Azure Portal](https://portal.azure.com), haga clic en **Nuevo** > **Web y móvil** y, a continuación, seleccione **Aplicación web** u otro tipo de aplicación de App Service.

![Cree una aplicación en Azure Portal.][createWebApp]

A continuación, puede seleccionar un plan de App Service existente o crear un plan para la nueva aplicación.

 ![Creación de un plan de App Service.][createASP]

Para crear un plan de App Service, haga clic en **[+] Crear nuevo**, escriba el nombre del **plan de App Service** y luego seleccione una **ubicación** adecuada. Haga clic en **Plan de tarifa**y seleccione un plan de tarifa adecuado para el servicio. Seleccione **Ver todos** para ver más opciones de precios, como **Gratis** y **Compartido**. 

Una vez haya seleccionado el plan de tarifa, haga clic en el botón **Seleccionar** .

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Cambio de una aplicación a otro plan de App Service

Puede mover una aplicación a otro plan de App Service siempre que el plan de origen y el plan de destino se encuentren en el _mismo grupo de recursos y región geográfica_.

Para mover una aplicación a otro plan, vaya a la aplicación que desea mover en [Azure Portal](https://portal.azure.com).

En el **Menú**, busque la sección **plan de App Service**.

Seleccione **Cambiar plan de App Service** para iniciar el proceso.

**Cambiar plan de App Service** abre el selector **plan de App Service**. Seleccione un plan existente al que mover esta aplicación. 

> [!IMPORTANT]
> La página **Select App Service plan** (Seleccionar plan de App Service) se filtra por los siguientes criterios: 
> - Existe en el mismo grupo de recursos 
> - Existe en la misma región geográfica 
> - Existe en el mismo espacio web  
> 
> Un _espacio web_ es una construcción lógica en App Service que define una agrupación de recursos del servidor. Una región geográfica (por ejemplo, Oeste de EE. UU.) contiene muchos espacios web para poder asignar a los clientes que usan App Service. En la actualidad, no se pueden mover los recursos de App Service entre los espacios web. 
> 

![Selector de plan de App Service.][change]

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Cada plan tiene su propio plan de tarifa. Por ejemplo, al mover un sitio de un plan **Gratis** a un plan **Estándar**, todas las aplicaciones asignadas puedan usar las características y los recursos del plan **Estándar**. Sin embargo, mover una aplicación de un plan de un nivel superior a un plan de un nivel inferior significa que ya no tiene acceso a determinadas características. Si la aplicación utiliza una característica que no está disponible en el plan de destino, obtendrá un error que muestra qué característica está en uso que no está disponible. Por ejemplo, si una de las aplicaciones usa certificados SSL, es posible que vea el mensaje de error: `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.` En este caso, debe escalar verticalmente el plan de tarifa del plan de destino a **Básico** o superior, o debe quitar todas las conexiones SSL con la aplicación, antes de poder mover la aplicación al plan de destino.

## <a name="move-an-app-to-a-different-region"></a>Cambio de una aplicación a una región diferente

La región en la que se ejecuta la aplicación es la región del plan de App Service en la que se encuentra. Sin embargo, no se puede cambiar la región de un plan de App Service. Si desea ejecutar la aplicación en una región diferente, una alternativa es clonar la aplicación. La clonación hará una copia de su aplicación en un plan de App Service nuevo o existente en cualquier región.

Puede encontrar **Clonar aplicación** en la sección **Herramientas de desarrollo** del menú.

> [!IMPORTANT]
> La clonación presenta algunas limitaciones sobre las que puede leer en [Clonación de aplicaciones de Azure App Service](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Escalación de un plan de App Service

Para escalar verticalmente un plan de tarifa del plan de App Service, consulte [Escalado vertical de aplicaciones en Azure](web-sites-scale.md).

Para escalar horizontalmente el recuento de instancias de una aplicación, consulte [Escalado del recuento de instancias de forma manual o automática](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Eliminación de un plan de App Service

Para evitar cargos inesperados, al eliminar la última aplicación en un plan de App Service, App Service también elimina el plan de forma predeterminada. Si, en su lugar, elige mantener el plan, debe cambiar el plan a **Gratis** para que no le cobren.

> [!IMPORTANT]
> Los **planes de App Service** que no tienen aplicaciones asociadas a ellos seguirán generando cargos, ya que siguen reservando las instancias de máquinas virtuales configuradas.

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Escalado vertical de aplicaciones en Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
