---
title: "Tipos de punto de conexión de Traffic Manager | Microsoft Docs"
description: "En este artículo, se explican los diferentes tipos de puntos de conexión que pueden utilizarse con el Administrador de tráfico de Azure."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 4e506986-f78d-41d1-becf-56c8516e4d21
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: kumud
ms.openlocfilehash: 792712e3e529d77ff20a7603b5fbf028ca60f8c8
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="traffic-manager-endpoints"></a>Puntos de conexión del Administrador de tráfico
Microsoft Azure Traffic Manager permite controlar cómo se distribuye el tráfico de red a implementaciones de aplicaciones que se ejecutan en distintos centros de datos. Se configura cada implementación de aplicaciones como un "punto de conexión" en Traffic Manager. Cuando Traffic Manager recibe una solicitud de DNS, elige un punto de conexión disponible para devolverlo en la respuesta DNS. Traffic Manager basa la elección en el estado actual del punto de conexión y el método de enrutamiento del tráfico. Para obtener más información, consulte [Cómo funciona el Administrador de tráfico](traffic-manager-how-traffic-manager-works.md).

El Administrador de tráfico admite tres tipos de puntos de conexión:
* **puntos de conexión de Azure** se utilizan para los servicios hospedados en Azure.
* **puntos de conexión externos** se emplean para los servicios hospedados fuera de Azure; es decir, de forma local o con otro proveedor de hospedaje.
* **puntos de conexión anidados** se utilizan para combinar los perfiles del Administrador de tráfico con el objetivo de crear esquemas de enrutamiento de tráfico más flexibles y, de este modo, satisfacer los requisitos de implementaciones más complejas y de mayor envergadura.

No hay restricciones en cuanto a cómo se combinan los diferentes tipos de puntos de conexión en un único perfil del Administrador de tráfico. Cada perfil puede contener cualquier combinación de tipos de punto de conexión.

En las siguientes secciones se describe con más detalle cada tipo de punto de conexión.

## <a name="azure-endpoints"></a>puntos de conexión de Azure

Los puntos de conexión de Azure se utilizan para servicios basados en Azure en Traffic Manager. Se admiten los siguientes tipos de recursos de Azure:

* Máquinas virtuales IaaS "clásicas" y servicios en la nube PaaS.
* Web Apps
* Recursos de PublicIPAddress (que pueden conectarse a las máquinas virtuales directamente o a través de una instancia de Azure Load Balancer). El recurso de publicIpAddress debe tener un nombre DNS asignado para usarse en el perfil de Traffic Manager.

Los recursos de PublicIPAddress son recursos de Azure Resource Manager. No existen en el modelo de implementación clásica. Por lo tanto, solo son compatibles con Azure Resource Manager de Traffic Manager. Los demás tipos de punto de conexión se admiten tanto en el modelo de Resource Manager como el de implementación clásica.

Cuando se usan puntos de conexión de Azure, Traffic Manager detecta cuándo se detiene e inicia una máquina virtual IaaS "clásica", un servicio en la nube o una aplicación web. Este estado se refleja en el estado del punto de conexión. Consulte [Supervisión de puntos de conexión de Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status) para más información. Cuando se detiene el servicio subyacente, Traffic Manager no lleva a cabo comprobaciones de estado del punto de conexión ni dirige el tráfico al punto de conexión. No se producen eventos de facturación de Traffic Manager para la instancia detenida. Cuando se reinicia el servicio, se reanuda la facturación y el punto de conexión es apto para recibir tráfico. Esta detección no sucede con los puntos de conexión de PublicIpAddress.

## <a name="external-endpoints"></a>puntos de conexión externos

Los puntos de conexión externos se usan para servicios fuera de Azure. Por ejemplo, un servicio hospedado localmente o con un proveedor diferente. Los puntos de conexión externos pueden usarse de forma individual o combinados con puntos de conexión de Azure en el mismo perfil de Traffic Manager. La combinación de puntos de conexión de Azure con otros externos posibilita distintos escenarios:

* En un modelo de conmutación por error activo-activo o activo-pasivo, use Azure para proporcionar mayor redundancia a una aplicación local existente.
* Para reducir la latencia de aplicación para usuarios de todo el mundo, extienda una aplicación local existente a más ubicaciones geográficas en Azure. Para más información, consulte [Método de enrutamiento del tráfico de rendimiento de Traffic Manager](traffic-manager-routing-methods.md#performance).
* Use Azure para ofrecer más capacidad para una aplicación local existente, bien de forma continua o bien empleando el modelo de migración a la nube para satisfacer un pico de demanda.

En algunos casos, resulta útil usar puntos de conexión externos para hacer referencia a servicios de Azure (consulte las [preguntas más frecuentes](traffic-manager-faqs.md#traffic-manager-endpoints) para ver ejemplos). En este caso, las comprobaciones de estado se facturan a la tarifa Puntos de conexión de Azure, y no Puntos de conexión externos. Sin embargo, a diferencia de los puntos de conexión de Azure, si detiene o elimina el servicio subyacente, se le seguirá cobrando por las comprobaciones de estado hasta que deshabilite o elimine el punto de conexión en Traffic Manager.

## <a name="nested-endpoints"></a>puntos de conexión anidados

Los puntos de conexión anidados combinan varios perfiles de Traffic Manager para crear esquemas de enrutamiento de tráfico flexibles y satisfacer los requisitos de implementaciones más complejas y de mayor envergadura. Con los puntos de conexión anidados, se agrega un perfil "secundario" como punto de conexión a un perfil "primario". Los perfiles primarios y secundarios pueden contener otros puntos de conexión de cualquier tipo, incluidos otros perfiles anidados. Para más información, consulte [Nested Traffic Manager profiles](traffic-manager-nested-profiles.md)(Perfiles anidados de Administrador de tráfico).

## <a name="web-apps-as-endpoints"></a>Aplicaciones web como puntos de conexión

A la hora de configurar aplicaciones web como puntos de conexión en el Administrador de tráfico, hay que tener en cuenta otras consideraciones:

1. Solo se puede utilizar con Traffic Manager Web Apps del nivel de SKU "Estándar" o superiores. Si se intenta agregar una aplicación web de una SKU inferior, se producirá un error. Si se cambia el nivel de SKU de una aplicación web existente a otro inferior, Traffic Manager deja de enviar tráfico a esa aplicación web.
2. Cuando un punto de conexión recibe una solicitud HTTP, usa el encabezado "host" de la solicitud para determinar qué aplicación web debe atenderla. El encabezado host contiene el nombre DNS utilizado para iniciar la solicitud, por ejemplo, "contosoapp.azurewebsites.net". Para usar otro nombre DNS con la aplicación web, este debe estar registrado como nombre de dominio personalizado para la aplicación. Al agregar un punto de conexión de aplicación web como punto de conexión de Azure, el nombre DNS del perfil de Traffic Manager se registra automáticamente para la aplicación. Este registro se elimina automáticamente cuando se elimina el punto de conexión.
3. Cada perfil del Administrador de tráfico puede tener, como máximo, un punto de conexión de aplicación web en cada región de Azure. Como solución alternativa para esta restricción, puede configurar una aplicación web como punto de conexión externo. Para más información, consulte las [preguntas más frecuentes](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Habilitación y deshabilitación de puntos de conexión

Deshabilitar un punto de conexión en Traffic Manager puede ser útil para quitar temporalmente tráfico de un punto de conexión que se encuentre en modo de mantenimiento o que se vaya a volver a implementar. Cuando el punto de conexión esté de nuevo en funcionamiento, se puede volver a habilitar.

Los puntos de conexión se pueden habilitar y deshabilitar por medio del portal de Traffic Manager, PowerShell, la CLI o la API de REST, que son compatibles con el modelo de implementación clásica y el de Resource Manager.

> [!NOTE]
> Deshabilitar un punto de conexión de Azure no afectará a su estado de implementación en Azure. Un servicio de Azure (como una máquina virtual o una aplicación web) permanece ejecución y puede recibir tráfico incluso cuando esté deshabilitado en Traffic Manager. El tráfico se puede enviar directamente a la instancia del servicio en lugar de por medio del nombre DNS del perfil de Traffic Manager. Para obtener más información, consulte [Cómo funciona el Administrador de tráfico](traffic-manager-how-traffic-manager-works.md).

La idoneidad actual de cada punto de conexión para recibir tráfico depende de los siguientes factores:

* El estado del perfil (habilitado/deshabilitado)
* El estado del punto de conexión (habilitado/deshabilitado)
* Los resultados de las comprobaciones de estado de ese punto de conexión

Consulte [Acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md#endpoint-and-profile-status)para obtener más información.

> [!NOTE]
> Debido a que el Administrador de tráfico funciona en el nivel de DNS, no es posible influir en las conexiones existentes con ningún punto de conexión. Cuando un punto de conexión no está disponible, Traffic Manager dirige las nuevas conexiones a otro que sí lo esté. Sin embargo, el host tras el punto de conexión deshabilitado o en mal estado puede continuar recibiendo tráfico a través de conexiones existentes hasta que finalizan esas sesiones. Las aplicaciones deberían limitar la duración de la sesión para permitir que se agote el tráfico procedente de conexiones existentes.

Si se deshabilitan todos los puntos de conexión de un perfil o si se deshabilita el perfil en sí, Traffic Manager envía una respuesta "NXDOMAIN" a una nueva consulta de DNS.


## <a name="next-steps"></a>Pasos siguientes

* Consulte [Cómo funciona el Administrador de tráfico](traffic-manager-how-traffic-manager-works.md).
* Obtenga información sobre la [supervisión del punto de conexión y la conmutación por error automática](traffic-manager-monitoring.md)del Administrador de tráfico.
* Conozca los [métodos de enrutamiento de tráfico](traffic-manager-routing-methods.md)del Administrador de tráfico.
