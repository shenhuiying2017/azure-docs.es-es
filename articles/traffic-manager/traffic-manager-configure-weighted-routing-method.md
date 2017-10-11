---
title: "Configuración del método de enrutamiento de tráfico round-robin ponderado con Azure Traffic Manager | Microsoft Docs"
description: "En este artículo se explica cómo equilibrar la carga del tráfico con un método round-robin en Traffic Manager"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 7aa4c9120d44ff1b3e59a57090ea04e3f8021fc4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Configuración del método de enrutamiento de tráfico ponderado en Traffic Manager

Un patrón del método de enrutamiento del tráfico es proporcionar un conjunto de extremos idénticos, que incluye servicios en la nube y sitios web, y enviar tráfico a cada uno de ellos de forma equitativa (round robin). Los siguientes pasos describen cómo configurar este tipo de método de enrutamiento de tráfico.

> [!NOTE]
> Azure ya proporciona la funcionalidad de equilibrio de carga Round Robin para sitios web en un centro de datos (también denominado "región"). El Administrador de tráfico permite especificar el método de enrutamiento del tráfico round robin para sitios web en distintos centros de datos.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Para configurar el método de enrutamiento de tráfico ponderado

1. En un explorador, inicie sesión en [Azure Portal](http://portal.azure.com). Si aún no tiene cuenta, puede registrarse para obtener [una evaluación gratuita durante un mes](https://azure.microsoft.com/free/). 
2. En la barra de búsqueda del Portal, busque los **perfiles de Traffic Manager** y, luego, haga clic en el nombre del perfil para el que desea configurar el método de enrutamiento.
3. En la hoja **Perfil de Traffic Manager**, compruebe que existen los servicios en la nube y los sitios web que desea incluir en la configuración.
4. En la sección **Configuración**, haga clic en **Configuración** y, en la hoja **Configuración**, proceda de la siguiente forma:
    1. En **Configuración del método de enrutamiento del tráfico**, compruebe que dicho método sea **Ponderado**. De no ser así, haga clic en **Ponderado** en la lista desplegable.
    2. Establezca la misma **configuración de supervisión de puntos de conexión** para todos los puntos de conexión dentro de este perfil de esta forma:
        1. Seleccione el **protocolo** adecuado y especifique el número de **puerto**. 
        2. En el tipo de **ruta de acceso**, escriba una barra diagonal */*. Para supervisar los puntos de conexión, debe especificar una ruta de acceso y un nombre de archivo. Una barra diagonal "/" es una entrada válida para la ruta de acceso relativa e implica que el archivo se encuentra en el directorio raíz (valor predeterminado).
        3. Haga clic en **Guardar** en la parte superior de la página.
5. Pruebe los cambios de la configuración de la siguiente forma:
    1.  En la barra de búsqueda del Portal, busque el nombre del perfil de Traffic Manager y haga clic en él en los resultados que se muestran.
    2.  En la hoja del perfil de **Traffic Manager**, haga clic en **Información general**.
    3.  La hoja **Perfil de Traffic Manager** muestra el nombre DNS del perfil de Traffic Manager que acaba de crear. Cualquier cliente puede usar este perfil (por ejemplo, mediante un explorador web) para enrutar el tráfico al punto de conexión correcto según el tipo de enrutamiento. En este caso, todas las solicitudes se enrutan a cada punto de conexión según round-robin.
6. Una vez que el perfil de Traffic Manager esté en funcionamiento, edite el registro DNS en el servidor DNS relevante para redireccionar el nombre de dominio de la empresa al nombre de dominio del Administrador de tráfico.

![Configuración del método de enrutamiento de tráfico ponderado con Traffic Manager][1]

## <a name="next-steps"></a>Pasos siguientes

- Información sobre el [método de enrutamiento de tráfico por prioridad](traffic-manager-configure-priority-routing-method.md).
- Información sobre el [método de enrutamiento de tráfico de rendimiento](traffic-manager-configure-performance-routing-method.md).
- Información sobre el [método de enrutamiento geográfico](traffic-manager-configure-geographic-routing-method.md).
- Información sobre cómo [probar la configuración de Traffic Manager](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
