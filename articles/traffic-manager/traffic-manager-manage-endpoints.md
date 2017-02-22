---
title: "Administración de extremos en el Azure Traffic Manager | Microsoft Docs"
description: "Este artículo le ayudará a agregar, quitar, habilitar y deshabilitar extremos del Administrador de tráfico de Azure."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: ade2bbc2-35a7-43c5-8001-4698f7254526
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 52f6d4f3e68e5eb120ee499827cc8549b8e547fd

---

# <a name="add-disable-enable-or-delete-endpoints"></a>Incorporación, deshabilitación, habilitación o eliminación de puntos de conexión

La función Aplicaciones web Servicio de aplicaciones de Azure ya proporciona la funcionalidad de enrutamiento del tráfico de conmutación por error y round robin para sitios web en un centro de datos, independientemente del modo del sitio web. El Administrador de tráfico de Azure permite especificar el enrutamiento del tráfico de conmutación por error y round robin para sitios web y servicios en la nube en distintos centros de datos. El primer paso necesario para proporcionar esa funcionalidad es agregar el extremo del sitio web o del servicio en la nube al Administrador de tráfico.

> [!NOTE]
> En este artículo se explica cómo usar el portal clásico. El Portal de Azure clásico solo admite la creación y asignación de servicios en la nube y aplicaciones web como puntos de conexión. Se prefiere la nueva interfaz [Azure Portal](https://portal.azure.com).

También puede deshabilitar los extremos individuales que forman parte de un perfil del Administrador de tráfico. Al deshabilitar un extremo, se deja como parte del perfil, pero el perfil actúa como si el extremo no estuviera incluido en él. Esta acción es útil para quitar temporalmente un punto de conexión que se encuentre en modo de mantenimiento o que se vaya a implementar. Cuando el extremo vuelva a estar en funcionamiento, se puede habilitar

> [!NOTE]
> Deshabilitar un extremo no tiene nada que ver con su estado de implementación en Azure. Sigue activo un punto de conexión en buen estado que puede recibir tráfico incluso cuando se deshabilite en Traffic Manager. Además, al deshabilitar un extremo de un perfil, no se afecta al estado en otro perfil.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Para agregar un extremo de servicio en la nube o sitio web

1. En el panel Traffic Manager del Portal de Azure clásico, localice el perfil de Traffic Manager que contiene la configuración del punto de conexión que desea modificar. Para abrir la página de configuración, haga clic en la flecha a la derecha del nombre del perfil.
2. En la parte superior de la página, haga clic en **Extremos** para ver los extremos que forman parte de la configuración.
3. En la parte inferior de la página, haga clic en **Agregar** para acceder a la página **Agregar extremos del servicio**. De manera predeterminada, la página muestra los servicios en la nube en **Extremos del servicio**.
4. Para los servicios en la nube, selecciónelos en la lista para agregarlos como puntos de conexión para este perfil. Si borra el nombre del servicio en la nube, este se eliminará de la lista de extremos.
5. Para los sitios web, haga clic en la lista desplegable **Tipo de servicio** y seleccione **Aplicación web**.
6. Seleccione los sitios web de la lista para agregarlos como extremos para este perfil. Si borra el nombre del servicio en la nube, este se eliminará de la lista de extremos. Solo puede seleccionar un sitio web por cada centro de datos de Azure (también conocido como región). Cuando se selecciona el primer sitio web, los demás sitios web en el mismo centro de datos dejarán de estar disponibles para seleccionarse. Tenga en cuenta, asimismo, que solo se enumeran sitios web estándar.
7. Una vez seleccionados los extremos de este perfil, haga clic en la marca de verificación, situada abajo a la derecha, para guardar los cambios.

> [!NOTE]
> Después de agregar o quitar un punto de conexión de un perfil mediante el método de enrutamiento de tráfico *Conmutación por error*, no podrá ordenar como desee la lista de prioridad de conmutación por error. Puede ajustar el orden de la lista de prioridad de conmutación por error en la página Configuración. Para obtener más información, consulte [Método de enrutamiento del tráfico de conmutación por error](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Para deshabilitar un extremo

1. En el panel Traffic Manager del Portal de Azure clásico, localice el perfil de Traffic Manager que contiene la configuración del punto de conexión que desea modificar. Para abrir la página de configuración, haga clic en la flecha a la derecha del nombre del perfil.
2. En la parte superior de la página, haga clic en **Extremos** para ver los extremos que se incluyen en la configuración.
3. Haga clic en el extremo que desea deshabilitar y, a continuación, haga clic en **Deshabilitar** en la parte inferior de la página.
4. Los clientes siguen enviando tráfico al punto de conexión durante el período de vida (TTL). Puede cambiar el valor de TTL en la página Configuración del perfil de Traffic Manager.

## <a name="to-enable-an-endpoint"></a>Para habilitar un extremo

1. En el panel Traffic Manager del Portal de Azure clásico, localice el perfil de Traffic Manager que contiene la configuración del punto de conexión que desea modificar. Para abrir la página de configuración, haga clic en la flecha a la derecha del nombre del perfil.
2. En la parte superior de la página, haga clic en **Extremos** para ver los extremos que se incluyen en la configuración.
3. Haga clic en el extremo que desea habilitar y, a continuación, haga clic en **Habilitar** en la parte inferior de la página.
4. Los clientes se dirigen al punto de conexión habilitado como se indica en el perfil.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Para eliminar un extremo del servicio en la nube o del sitio web

1. En el panel Traffic Manager del Portal de Azure clásico, localice el perfil de Traffic Manager que contiene la configuración del punto de conexión que desea modificar. Para abrir la página de configuración, haga clic en la flecha a la derecha del nombre del perfil.
2. En la parte superior de la página, haga clic en **Extremos** para ver los extremos que forman parte de la configuración.
3. En la página Extremos, haga clic en el nombre del extremo que desea eliminar del perfil.
4. En la parte inferior de la página, haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

* [Administración de perfiles de Traffic Manager](traffic-manager-manage-profiles.md)
* [Configurar métodos de enrutamiento](traffic-manager-configure-routing-method.md)
* [Solución de problemas de estado degradado del Administrador de tráfico](traffic-manager-troubleshooting-degraded.md)
* [Consideraciones de rendimiento sobre el Administrador de tráfico](traffic-manager-performance-considerations.md)
* [Operaciones del Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)




<!--HONumber=Nov16_HO5-->


