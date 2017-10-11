---
title: Administrar perfiles del Azure Traffic Manager | Microsoft Docs
description: "La información contenida en este artículo le ayuda a crear, deshabilitar, habilitar y eliminar un perfil de Azure Traffic Manager."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f06e0365-0a20-4d08-b7e1-e56025e64f66
ms.service: traffic-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: kumud
ms.openlocfilehash: a5164282264124835692bc72a4ab61891aa7af9d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="manage-an-azure-traffic-manager-profile"></a>Administrar un perfil del Administrador de tráfico de Azure

Los perfiles de Traffic Manager usan métodos de enrutamiento de tráfico para controlar la distribución del tráfico a los puntos de conexión de servicios en la nube o de sitios web. En este artículo se explica cómo crear y administrar estos perfiles.

## <a name="create-a-traffic-manager-profile"></a>Crear un perfil de Traffic Manager

Puede crear un perfil de Traffic Manager mediante el portal de Azure. Después de crear el perfil, puede configurar puntos de conexión, supervisión y otras opciones en el portal de Azure. El Administrador de tráfico admite un máximo de 200 extremos por perfil. Sin embargo, la mayoría de los escenarios de uso tan solo requieren unos pocos puntos de conexión.

### <a name="to-create-a-traffic-manager-profile"></a>Para crear un perfil de Traffic Manager

1. En un explorador, inicie sesión en [Azure Portal](http://portal.azure.com). Si aún no tiene cuenta, puede registrarse para obtener [una evaluación gratuita durante un mes](https://azure.microsoft.com/free/). 
2. En el menú **Concentrador**, haga clic en **Nuevo** > **Redes** > **Ver todo**, haga clic en el perfil de **Traffic Manager** para abrir la hoja **Crear perfil de Traffic Manager** y, luego, haga clic en **Crear**.
3. En la hoja **Crear perfil de Traffic Manager**, complete los campos de la manera siguiente:
    1. En **Nombre**, proporcione un nombre para el perfil. Este nombre debe ser único en la zona trafficmanager.net y generará el nombre DNS <name>,trafficmanager.net que se usa para acceder al perfil de Traffic Manager.
    2. En **Método de enrutamiento**, seleccione el método de enrutamiento por **prioridad**.
    3. En **Suscripción**, seleccione la suscripción en la que desea crear este perfil.
    4. En **Grupo de recursos**, cree un grupo de recursos nuevo en el cual colocar este perfil.
    5. En **Ubicación del grupo de recursos**, seleccione la ubicación del grupo de recursos. Esta configuración se refiere a la ubicación del grupo de recursos y no tiene efecto alguno sobre el perfil de Traffic Manager que se implementará globalmente.
    6. Haga clic en **Crear**.
    7. Una vez que se complete la implementación global del perfil de Traffic Manager, aparece en el grupo de recursos respectivo como uno de los recursos.

## <a name="disable-enable-or-delete-a-profile"></a>Deshabilitar, habilitar o eliminar un perfil

Puede deshabilitar un perfil existente para que Traffic Manager no refiera solicitudes de usuario a los puntos de conexión configurados. Cuando deshabilite un perfil de Traffic Manager, dicho perfil y la información que contiene se mantienen intactos y se pueden editar en la interfaz de Traffic Manager.  Las referencias se reanudarán cuando vuelva a habilitar el perfil. Cuando se crea un perfil de Traffic Manager en Azure Portal, este se habilita automáticamente. Si decide que un perfil ha dejado de ser necesario, puede eliminarlo.

### <a name="to-disable-a-profile"></a>Para deshabilitar un perfil

1. Si usa un nombre de dominio personalizado, cambie el registro CNAME en el servidor DNS de Internet para que deje de apuntar a su perfil de Traffic Manager.
2. El tráfico dejará de dirigirse a los puntos de conexión por medio de la configuración del perfil de Traffic Manager.
3. En un explorador, inicie sesión en [Azure Portal](http://portal.azure.com).
2. En la barra de búsqueda, busque el nombre del **perfil de Traffic Manager** que desea modificar y haga clic en él en los resultados que se muestran.
3. En la hoja **Perfil de Traffic Manager**, haga clic en **Información general**, en la hoja Información general, haga clic en **Deshabilitar** y luego confirme la deshabilitación del perfil de Traffic Manager.

### <a name="to-enable-a-profile"></a>Para habilitar un perfil

1. En un explorador, inicie sesión en [Azure Portal](http://portal.azure.com).
2. En la barra de búsqueda, busque el nombre del **perfil de Traffic Manager** que desea modificar y haga clic en él en los resultados que se muestran.
3. En la hoja **Perfil de Traffic Manager**, haga clic en **Información general** y, luego, en la hoja Información general, haga clic en **Habilitar**.
5. Si usa un nombre de dominio personalizado, cree un registro de recursos CNAME en el servidor DNS de Internet para que apunte al nombre de dominio de su perfil de Traffic Manager.
6. El tráfico se dirige a los puntos de conexión de nuevo.

### <a name="to-delete-a-profile"></a>Para eliminar un perfil

1. Asegúrese de que el registro de recursos DNS del servidor DNS de Internet ya no usa un registro de recursos CNAME que señale al nombre de dominio del perfil del Administrador de tráfico.
2. En la barra de búsqueda, busque el nombre del **perfil de Traffic Manager** que desea modificar y haga clic en él en los resultados que se muestran.
3. En la hoja **Perfil de Traffic Manager**, haga clic en **Información general**, en la hoja Información general, haga clic en **Eliminar** y luego confirme la eliminación del perfil de Traffic Manager.

## <a name="next-steps"></a>Pasos siguientes

* [Agregación de un extremo](traffic-manager-endpoints.md)
* [Configuración del método de enrutamiento por prioridad](traffic-manager-configure-priority-routing-method.md)
* [Configuración del método de enrutamiento geográfico](traffic-manager-configure-geographic-routing-method.md) 
* [Configuración del método de enrutamiento ponderado](traffic-manager-configure-weighted-routing-method.md)
* [Configuración del método de enrutamiento de rendimiento](traffic-manager-configure-performance-routing-method.md)
