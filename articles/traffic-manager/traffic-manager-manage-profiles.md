---
title: Administrar perfiles del Azure Traffic Manager | Microsoft Docs
description: "Este artículo lo ayudará a crear, deshabilitar, habilitar, eliminar y ver el historial de un perfil de Azure Traffic Manager."
services: traffic-manager
documentationcenter: 
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: f06e0365-0a20-4d08-b7e1-e56025e64f66
ms.service: traffic-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7c322fef4fb8588d6d3cc5c6c30fdcb486834155

---

# <a name="manage-an-azure-traffic-manager-profile"></a>Administrar un perfil del Administrador de tráfico de Azure

Los perfiles de Traffic Manager usan métodos de enrutamiento de tráfico para controlar la distribución del tráfico a los puntos de conexión de servicios en la nube o de sitios web. En este artículo se explica cómo crear y administrar estos perfiles.

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>Crear un perfil del Administrador de tráfico mediante Creación rápida

Puede crear rápidamente un perfil del Administrador de tráfico mediante Creación rápida en el Portal de Azure clásico. Creación rápida permite crear perfiles con valores de configuración básicos. Sin embargo, no puede usar Creación rápida para valores como, por ejemplo, el conjunto de extremos (servicios en la nube y sitios web), el orden de conmutación por error para el método de enrutamiento del tráfico de conmutación por error o la configuración de supervisión. Después de crear el perfil, puede configurar estas opciones en el Portal de Azure clásico. El Administrador de tráfico admite un máximo de 200 extremos por perfil. Sin embargo, la mayoría de los escenarios de uso tan solo requieren unos pocos puntos de conexión.

### <a name="to-create-a-traffic-manager-profile"></a>Para crear un perfil de Traffic Manager

1. **Implemente en el entorno de producción sus servicios en la nube y sitios web.** Para obtener más información sobre servicios en la nube, consulte [Servicios en la nube](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para obtener más información acerca de los sitios web, consulte [Sitios web](http://go.microsoft.com/fwlink/p/?LinkId=393327).
2. **Inicie sesión en el Portal de Azure clásico.** Haga clic en **Nuevo** en la parte inferior izquierda del portal, en **Servicios de red > Traffic Manager** y en **Creación rápida** para comenzar a configurar su perfil.
3. **Configure el prefijo DNS.**  Proporcione a su perfil de Administrador de tráfico un nombre de prefijo DNS único. Puede especificar solo el prefijo de un nombre de dominio de Administrador de tráfico.
4. **Seleccione la suscripción.**  Seleccione la suscripción de Azure apropiada. Cada perfil está asociado a una sola suscripción. Si solo tiene una suscripción, esta opción no aparecerá.
5. **Seleccione el método de enrutamiento del tráfico.** Seleccione el método de enrutamiento del tráfico en **Directiva de enrutamiento del tráfico**. Para obtener más información acerca de los métodos de enrutamiento del tráfico, consulte [Información acerca de los métodos de enrutamiento del tráfico del Administrador de tráfico](traffic-manager-routing-methods.md).
6. **Haga clic en "Crear" para crear el perfil**. Cuando haya terminado de configurar el perfil, puede buscarlo en el panel del Administrador de tráfico del Portal de Azure clásico.
7. **Configure los puntos de conexión, la supervisión y la configuración adicional en el Portal de Azure clásico.** Con Creación rápida solo se configuran los valores básicos. Es necesario configurar valores adicionales, como la lista de puntos de conexión y el orden de conmutación por error de puntos de conexión.

## <a name="disable-enable-or-delete-a-profile"></a>Deshabilitar, habilitar o eliminar un perfil

Puede deshabilitar un perfil existente para que Traffic Manager no refiera solicitudes de usuario a los puntos de conexión configurados. Cuando deshabilite un perfil de Traffic Manager, dicho perfil y la información que contiene se mantienen intactos y se pueden editar en la interfaz de Traffic Manager.  Las referencias se reanudarán cuando vuelva a habilitar el perfil. Cuando se crea un perfil de Traffic Manager en el Portal de Azure clásico, se habilita automáticamente. Si decide que un perfil ha dejado de ser necesario, puede eliminarlo.

### <a name="to-disable-a-profile"></a>Para deshabilitar un perfil

1. Si usa un nombre de dominio personalizado, cambie el registro CNAME en el servidor DNS de Internet para que deje de apuntar a su perfil de Traffic Manager.
2. El tráfico dejará de dirigirse a los puntos de conexión por medio de la configuración del perfil de Traffic Manager.
3. Seleccione el perfil que desea deshabilitar. En la página Traffic Manager, resalte el perfil haciendo clic en la columna situada junto a su nombre. Tenga en cuenta que, al hacer clic en el nombre del perfil o en la flecha junto al nombre, se abre la página de configuración del perfil.
4. Después de seleccionar el perfil, haga clic en **Deshabilitar** en la parte inferior de la página.

### <a name="to-enable-a-profile"></a>Para habilitar un perfil

1. Seleccione el perfil que desea deshabilitar. En la página Traffic Manager, resalte el perfil haciendo clic en la columna situada junto a su nombre. Tenga en cuenta que, al hacer clic en el nombre del perfil o en la flecha junto al nombre, se abre la página de configuración del perfil.
2. Después de seleccionar el perfil, haga clic en **Habilitar** , en la parte inferior de la página.
3. Si usa un nombre de dominio personalizado, cree un registro de recursos CNAME en el servidor DNS de Internet para que apunte al nombre de dominio de su perfil de Traffic Manager.
4. El tráfico se dirige a los puntos de conexión de nuevo.

### <a name="to-delete-a-profile"></a>Para eliminar un perfil

1. Asegúrese de que el registro de recursos DNS del servidor DNS de Internet ya no usa un registro de recursos CNAME que señale al nombre de dominio del perfil del Administrador de tráfico.
2. Seleccione el perfil que desea deshabilitar. En la página Traffic Manager, resalte el perfil haciendo clic en la columna situada junto a su nombre. Tenga en cuenta que, al hacer clic en el nombre del perfil o en la flecha junto al nombre, se abre la página de configuración del perfil.
3. Después de seleccionar el perfil, haga clic en **Eliminar** en la parte inferior de la página.

## <a name="view-traffic-manager-profile-change-history"></a>Ver el historial de cambios de perfiles del Administrador de tráfico

Puede ver el historial de cambios del perfil del Administrador de tráfico en el Portal de Azure clásico, en Servicios de administración.

### <a name="to-view-your-traffic-manager-change-history"></a>Para ver el historial de cambios del Administrador de tráfico

1. En el panel izquierdo del Portal de Azure clásico, haga clic en **Servicios de administración**.
2. En la página de Servicios de administración, haga clic en **Registros de operaciones**.
3. En la página Registros de operaciones puede usar los filtros para ver el historial de cambios del perfil del Administrador de tráfico. Después de seleccionar las opciones de filtrado, haga clic en la marca de verificación para ver los resultados.

   * Para ver los cambios de todos los perfiles, seleccione la suscripción y un intervalo de tiempo, y seleccione **Traffic Manager** en el menú contextual **Tipo**.
   * Para filtrar por nombre de perfil, escriba el nombre del perfil en el campo **Nombre del servicio** o selecciónelo en el menú contextual.
   * Para ver los detalles de cada cambio individualmente, seleccione la fila que contiene el cambio que desea ver y haga clic en **Detalles** , en la parte inferior de la página. En la ventana **Detalles de la operación**, puede ver la representación XML del objeto de API que se creó o actualizó como parte de la operación.

## <a name="next-steps"></a>Pasos siguientes

* [Agregación de un extremo](traffic-manager-endpoints.md)
* [Configuración del método de enrutamiento de conmutación por error](traffic-manager-configure-failover-routing-method.md)
* [Configuración del método de enrutamiento round robin](traffic-manager-configure-round-robin-routing-method.md)
* [Configuración del método de enrutamiento de rendimiento](traffic-manager-configure-performance-routing-method.md)
* [Hacer que un dominio de Internet de la compañía indique un nombre de dominio de Traffic Manager](traffic-manager-point-internet-domain.md)
* [Solución de problemas de estado degradado del Administrador de tráfico](traffic-manager-troubleshooting-degraded.md)



<!--HONumber=Nov16_HO2-->


