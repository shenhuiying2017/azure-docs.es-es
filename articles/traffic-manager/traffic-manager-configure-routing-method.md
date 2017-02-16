---
title: "Configuración de métodos de enrutamiento de Traffic Manager | Microsoft Docs"
description: "Este artículo explica cómo se configura en Traffic Manager los diferentes métodos de enrutamiento."
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
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: d4481115dbf8cc720019096969c55509f33d250c

---

# <a name="configure-traffic-manager-routing-methods"></a>Configuración de los métodos de enrutamiento de Traffic Manager

Azure Traffic Manager proporciona tres métodos de enrutamiento que controlan cómo se enruta el tráfico a los puntos de conexión disponibles. El método de enrutamiento de tráfico se aplica a cada consulta de DNS recibida con el fin de determinar qué punto de conexión se debe devolver en la respuesta DNS.

Existen tres métodos de enrutamiento disponibles en el Administrador de tráfico:

* **Prioridad:** seleccione Prioridad cuando quiera usar un punto de conexión de servicio primario y proporcione reservas en caso de que el primario no se encuentre disponible.
* **Ponderado:** seleccione Ponderado cuando quiera distribuir el tráfico entre un conjunto de puntos de conexión, o bien de manera uniforme o según los pesos definidos.
* **Rendimiento** : seleccione Rendimiento cuando tenga puntos de conexión en diferentes ubicaciones geográficas y quiera que los usuarios finales utilicen el punto de conexión "más cercano" según la latencia de red más baja.

## <a name="configure-priority-routing-method"></a>Configuración del método de enrutamiento Prioridad

Con independencia del modo del sitio web, Azure Websites ya proporciona la funcionalidad de conmutación por error para sitios web en un centro de datos (también conocido como región). Traffic Manager proporciona conmutación por error para sitios web en distintos centros de datos.

Un patrón común para la conmutación por error del servicio es proporcionar un conjunto de servicios de copia de seguridad idénticos y enviar tráfico a un servicio principal. Los siguientes pasos explican cómo configurar esta conmutación por error con prioridad con Azure Cloud Services y Azure Websites:

1. En el Portal de Azure clásico, en el panel izquierdo, haga clic en el icono **Administrador de tráfico** para abrir el panel del Administrador de tráfico.
2. En el panel Traffic Manager del Portal de Azure clásico, localice el perfil de Traffic Manager que contiene la configuración que desea modificar. Para abrir la página de configuración del perfil, haga clic en la flecha a la derecha del nombre del perfil.
3. En la página del perfil, haga clic en **Extremos** en la parte superior de la página. Compruebe que existen los servicios en la nube y los sitios web que desea incluir en la configuración.
4. Haga clic en **Configurar**, en la parte superior, para abrir la página de configuración.
5. En **Configuración del método de enrutamiento del tráfico**, compruebe que dicho método sea **Conmutación por error**. De no ser así, haga clic en **Conmutación por error** en la lista desplegable.
6. En **Lista de prioridades de conmutación por error**, ajuste el orden de la conmutación por error de los extremos. Cuando seleccione el método de enrutamiento del tráfico de **Conmutación por error** , el orden de los extremos seleccionados es importante. El extremo principal se encuentra en primer lugar. Utilice las flechas hacia arriba y hacia abajo para cambiar el orden según lo desee. Para obtener más información sobre cómo establecer la prioridad de conmutación por error con Windows PowerShell, consulte [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Compruebe que la **Configuración de supervisión** sea correcta. La supervisión garantiza que no se envíe tráfico a los extremos sin conexión. Para supervisar los puntos de conexión, debe especificar una ruta de acceso y un nombre de archivo. Una barra diagonal "/" es una entrada válida para la ruta de acceso relativa e implica que el archivo se encuentra en el directorio raíz (valor predeterminado).
8. Una vez que haya terminado de cambiar la configuración, haga clic en **Guardar** en la parte inferior de la página.
9. Pruebe los cambios de la configuración.
10. Una vez que el perfil de Traffic Manager esté en funcionamiento, edite el registro DNS en el servidor DNS relevante para redireccionar el nombre de dominio de la empresa al nombre de dominio del Administrador de tráfico.

## <a name="configure-weighted-routing-method"></a>Configuración del método de enrutamiento Ponderado

Un patrón del método de enrutamiento del tráfico es proporcionar un conjunto de extremos idénticos, que incluye servicios en la nube y sitios web, y enviar tráfico a cada uno de ellos de forma equitativa (round robin). Los siguientes pasos describen cómo configurar este tipo de método de enrutamiento de tráfico.

> [!NOTE]
> Azure ya proporciona la funcionalidad de equilibrio de carga Round Robin para sitios web en un centro de datos (también denominado "región"). El Administrador de tráfico permite especificar el método de enrutamiento del tráfico round robin para sitios web en distintos centros de datos.

1. En el Portal de Azure clásico, en el panel izquierdo, haga clic en el icono **Administrador de tráfico** para abrir el panel del Administrador de tráfico.
2. En el panel Traffic Manager, localice el perfil de Traffic Manager que contiene la configuración del punto de conexión que desea modificar. Para abrir la página de configuración del perfil, haga clic en la flecha a la derecha del nombre del perfil.
3. En la página del perfil, haga clic en **Extremos** y compruebe que están presentes los extremos del servicio que desea incluir en la configuración.
4. En la página del perfil, haga clic en **Configurar** en la parte superior, para abrir la página de configuración.
5. En **Configuración del método de enrutamiento del tráfico**, compruebe que dicho método sea **Round Robin**. De lo contrario, haga clic en **Round Robin** en la lista desplegable.
6. Compruebe que la **Configuración de supervisión** sea correcta. La supervisión garantiza que no se envíe tráfico a los extremos sin conexión. Para supervisar los puntos de conexión, debe especificar una ruta de acceso y un nombre de archivo. Una barra diagonal "/" es una entrada válida para la ruta de acceso relativa e implica que el archivo se encuentra en el directorio raíz (valor predeterminado).
7. Una vez que haya terminado de cambiar la configuración, haga clic en **Guardar** en la parte inferior de la página.
8. Pruebe los cambios de la configuración.
9. Una vez que el perfil de Traffic Manager esté en funcionamiento, edite el registro DNS en el servidor DNS relevante para redireccionar el nombre de dominio de la empresa al nombre de dominio del Administrador de tráfico.

## <a name="configure-performance-traffic-routing-method"></a>Configuración del método de enrutamiento del tráfico de rendimiento

El método de enrutamiento de tráfico Rendimiento permite dirigir el tráfico al punto de conexión con la menor latencia de red del cliente. Normalmente, el centro de datos con la latencia más baja corresponde a la distancia geográfica más cercana. Este método de enrutamiento de tráfico no cuenta los cambios en tiempo real en la carga o configuración de red.

1. En el Portal de Azure clásico, en el panel izquierdo, haga clic en el icono **Administrador de tráfico** para abrir el panel del Administrador de tráfico.
2. En el panel Traffic Manager, localice el perfil de Traffic Manager que contiene la configuración del punto de conexión que desea modificar. Para abrir la página de configuración del perfil, haga clic en la flecha a la derecha del nombre del perfil.
3. En la página del perfil, haga clic en **Extremos** y compruebe que están presentes los extremos del servicio que desea incluir en la configuración.
4. En la página del perfil, haga clic en **Configurar** en la parte superior, para abrir la página de configuración.
5. Para la **configuración del método de enrutamiento del tráfico**, compruebe que este método sea **Rendimiento**. Si no es así, haga clic en **Rendimiento** en la lista desplegable.
6. Compruebe que la **Configuración de supervisión** sea correcta. La supervisión garantiza que no se envíe tráfico a los extremos sin conexión. Para supervisar los puntos de conexión, debe especificar una ruta de acceso y un nombre de archivo. Una barra diagonal "/" es una entrada válida para la ruta de acceso relativa e implica que el archivo se encuentra en el directorio raíz (valor predeterminado).
7. Una vez que haya terminado de cambiar la configuración, haga clic en **Guardar** en la parte inferior de la página.
8. Pruebe los cambios de la configuración.
9. Una vez que el perfil de Traffic Manager esté en funcionamiento, edite el registro DNS en el servidor DNS relevante para redireccionar el nombre de dominio de la empresa al nombre de dominio del Administrador de tráfico.

## <a name="next-steps"></a>Pasos siguientes

* [Administración de perfiles de Traffic Manager](traffic-manager-manage-profiles.md)
* [Métodos de enrutamiento del Administrador de tráfico](traffic-manager-routing-methods.md)
* [Pruebas de configuración de Traffic Manager](traffic-manager-testing-settings.md)
* [Hacer que un dominio de Internet de la empresa indique un dominio del Administrador de tráfico](traffic-manager-point-internet-domain.md)
* [Administración de puntos de conexión de Traffic Manager](traffic-manager-manage-endpoints.md)
* [Solución de problemas de estado degradado del Administrador de tráfico](traffic-manager-troubleshooting-degraded.md)




<!--HONumber=Nov16_HO5-->


