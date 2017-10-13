---
title: Crear un perfil de Traffic Manager en Azure | Microsoft Docs
description: "En este artículo se describe cómo crear un perfil de Traffic Manager"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: kumud
ms.openlocfilehash: e9ff7947e7801a9f352a7a947b09893b8f615d88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-traffic-manager-profile"></a>Crear un perfil de Traffic Manager

En este artículo se describe se puede crear un perfil con el tipo de enrutamiento por **prioridad** para enrutar a los usuarios a dos puntos de conexión de Azure Web Apps. Mediante el uso del tipo de enrutamiento por **prioridad**, todo el tráfico se enruta al primer punto de conexión, mientras que el segundo se conserva como copia de seguridad. Como resultado, los usuarios se pueden enrutar al segundo punto de conexión si el primero tiene un estado incorrecto.

En este artículo, dos puntos de conexión de Azure Web App creados anteriormente se asocian a este perfil de Traffic Manager que se acaba de crear. Para más información sobre cómo crear puntos de conexión de Azure Web App, visite la [página de documentación de Azure Web Apps](https://docs.microsoft.com/azure/app-service/). Puede agregar cualquier punto de conexión con un nombre DNS y al que se pueda llegar a través de Internet público; usaremos los puntos de conexión de Azure Web Apps como ejemplo.

### <a name="create-a-traffic-manager-profile"></a>Crear un perfil de Traffic Manager
1. En un explorador, inicie sesión en [Azure Portal](http://portal.azure.com). Si aún no tiene cuenta, puede registrarse para obtener una [evaluación gratuita durante un mes](https://azure.microsoft.com/free/). 
2. En el menú **Concentrador**, haga clic en **Nuevo** > **Redes** > **Ver todo**, haga clic en el perfil de **Traffic Manager** para abrir la hoja **Crear perfil de Traffic Manager** y, luego, haga clic en **Crear**.
3. En la hoja **Crear perfil de Traffic Manager**, complete los campos de la manera siguiente:
    1. En **Nombre**, proporcione un nombre para el perfil. Este nombre debe ser único en la zona trafficmanager.net y generará el nombre DNS <name>,trafficmanager.net que se usa para tener acceso al perfil de Traffic Manager.
    2. En **Método de enrutamiento**, seleccione el método de enrutamiento por **prioridad**.
    3. En **Suscripción**, seleccione la suscripción en la que desea crear este perfil.
    4. En **Grupo de recursos**, cree un grupo de recursos nuevo en el cual colocar este perfil.
    5. En **Ubicación del grupo de recursos**, seleccione la ubicación del grupo de recursos. Esta configuración se refiere a la ubicación del grupo de recursos y no tiene efecto alguno sobre el perfil de Traffic Manager que se implementará globalmente.
    6. Haga clic en **Crear**.
    7. Una vez que se complete la implementación global del perfil de Traffic Manager, aparece en el grupo de recursos respectivo como uno de los recursos.

    ![Crear un perfil de Traffic Manager](./media/traffic-manager-create-profile/Create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Incorporación de puntos de conexión de Traffic Manager

1. En la barra de búsqueda del portal, busque el nombre del **perfil de Traffic Manager** que creó en la sección anterior y haga clic en el perfil de Traffic Manager en los resultados que aparecen.
2. En la hoja **Perfil de Traffic Manager**, en la sección **Configuración**, haga clic en **Puntos de conexión**.
3. En la hoja **Puntos de conexión** que aparece, haga clic en **Agregar**.
4. En la hoja **Agregar punto de conexión**, complete los campos de la siguiente manera:
    1. En **Tipo**, haga clic en **Punto de conexión de Azure**.
    2. Proporcione un **nombre** con el que desea reconocer este punto de conexión.
    3. En **Tipo de recurso de destino**, haga clic en **App Service**.
    4. En **Recurso de destino**, haga clic en **Elegir un servicio de aplicaciones** para mostrar la lista de Web Apps en la misma suscripción. En la hoja **Recursos** que aparece, elija el servicio de aplicaciones que desea agregar como el primer punto de conexión.
    5. En **Prioridad**, seleccione **1**. Esto hace que todo el tráfico vaya a este punto de conexión si funciona correctamente.
    6. No active la opción **Agregar como deshabilitado**.
    7. Haga clic en **Aceptar**
5.  Repita los pasos 3 y 4 para el próximo punto de conexión de Azure Web Apps. Asegúrese de agregarlo con el valor de **prioridad** establecido en **2**.
6.  Cuando termine de agregar ambos puntos de conexión, aparecerán en la hoja **Perfil de Traffic Manager** junto con el estado de supervisión como **En línea**.

    ![Incorporación de un punto de conexión de Traffic Manager](./media/traffic-manager-create-profile/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Uso del perfil de Traffic Manager
1.  En la barra de búsqueda del portal, busque el nombre del **perfil de Traffic Manager** que creó en la sección anterior. Haga clic en el perfil de Traffic Manager en los resultados que aparezcan.
2. En la hoja **Perfil de Traffic Manager**, haga clic en **Información general**.
3. La hoja **Perfil de Traffic Manager** muestra el nombre DNS del perfil de Traffic Manager que acaba de crear. Cualquier cliente puede usar este perfil (por ejemplo, mediante un explorador web) para enrutar el tráfico al punto de conexión correcto según el tipo de enrutamiento. En este caso, todas las solicitudes se enrutan al primer punto de conexión y si Traffic Manager detecta que no funciona correctamente, se hace conmutación por error del tráfico automáticamente al siguiente punto de conexión.

## <a name="delete-the-traffic-manager-profile"></a>Eliminar el perfil de Traffic Manager
Si ya no los necesita, elimine el grupo de recursos y el perfil de Traffic Manager que creó. Para hacerlo, seleccione el grupo de recursos en la hoja **Perfil de Traffic Manager** y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre los [tipos de enrutamiento](traffic-manager-routing-methods.md).
- Más información sobre los [tipos de punto de conexión](traffic-manager-endpoint-types.md).
- Más información sobre la [supervisión de los puntos de conexión](traffic-manager-monitoring.md).



