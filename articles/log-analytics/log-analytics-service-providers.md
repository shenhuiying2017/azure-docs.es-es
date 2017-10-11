---
title: "Características de Log Analytics para proveedores de servicios | Microsoft Docs"
description: Log Analytics puede ayudar a proveedores de servicios administrados (MSP), grandes empresas, proveedores de software independientes (ISV) y proveedores de servicios de hospedaje a administrar y supervisar servidores en infraestructuras locales en la nube del cliente.
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: richrund
ms.openlocfilehash: 8a67d9a9d345682e9e6c8f5c7779204a038f5f6a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="log-analytics-features-for-service-providers"></a>Características de Log Analytics para proveedores de servicios
Log Analytics puede ayudar a proveedores de servicios administrados (MSP), grandes empresas, proveedores de software independientes (ISV) y proveedores de servicios de hospedaje a administrar y supervisar servidores en infraestructuras locales en la nube del cliente. 

Las grandes empresas comparten muchas similitudes con los proveedores de servicios, especialmente cuando hay un equipo de TI centralizado que es responsable de la administración de TI de muchas unidades de negocio diferentes. Por motivos de simplicidad, este documento utiliza el término "*proveedor de servicios*", pero la misma funcionalidad también está disponible para empresas y otros clientes.

## <a name="cloud-solution-provider"></a>Proveedor de soluciones en la nube
Para los asociados y proveedores de servicios que forman parte del programa de [proveedores de soluciones en la nube (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview), Log Analytics es uno de los servicios de Azure disponible en una suscripción de CSP. 

Para análisis Log Analytics, se habilitan las siguientes funcionalidades en las suscripciones de *proveedores de soluciones en la nube*.

Los *proveedores de soluciones en la nube* pueden realizar las siguientes tareas:

* Crear áreas de trabajo de Log Analytics en una suscripción de inquilino (cliente).
* Acceder a las áreas de trabajo que creen los inquilinos. 
* Agregar y quitar el acceso de usuario al área de trabajo mediante la administración de usuarios de Azure. Cuando en un área de trabajo de inquilino en el portal de OMS, la página de administración de usuarios de Configuración no está disponible.
  * Log Analytics aún admite el acceso basado en roles, lo que da un usuario permiso de `reader` en Azure Portal, con lo que podrá realizar cambios de configuración en el portal de OMS

Para iniciar sesión en una suscripción de inquilino, debe especificar el identificador de este. A menudo, el identificador de inquilino es la última parte de la dirección de correo electrónico con la que se inicia sesión.

* En el portal de OMS, agregue `?tenant=contoso.com` en la dirección URL del portal. Por ejemplo, `mms.microsoft.com/?tenant=contoso.com`
* En PowerShell, use el parámetro `-Tenant contoso.com` al usar el cmdlet `Add-AzureRmAccount`.
* El identificador de inquilino se agrega automáticamente al utilizar el vínculo `OMS portal` desde Azure Portal para abrir e iniciar sesión el portal de OMS del área de trabajo seleccionada.

Los *clientes* de un proveedor de soluciones en la nube, puede realizar las siguientes tareas:

* Crear áreas de trabajo de Log Analytics en una suscripción de CSP.
* Acceder a las áreas de trabajo que creen los CSP.
  * Utilizar el vínculo `OMS portal` desde Azure Portal para abrir e iniciar sesión el portal de OMS del área de trabajo seleccionada.
* Ver y usar la página de administración de usuarios en la aplicación Configuración del portal de OMS.

> [!NOTE]
> Las soluciones de copia de seguridad y recuperación del sitio incluidas para Log Analytics no pueden conectarse a un almacén de Recovery Services y no se puede configurar en una suscripción de CSP. 
> 
> 

## <a name="managing-multiple-customers-using-log-analytics"></a>Administrar a varios clientes mediante Log Analytics.
Se recomienda crear un área de trabajo de Log Analytics para cada cliente que administre. Un área de trabajo de Log Analytics proporciona lo siguiente:

* Una ubicación geográfica para almacenar los datos 
* Granularidad para la facturación 
* Aislamiento de datos 
* Configuración única

Al crear un área de trabajo por cliente, se pueden separar los datos de cada cliente y hacer un seguimiento del uso que hace cada uno de ellos.

Para más información sobre cuándo y por qué se debe crear varias áreas de trabajo, consulte [administración del acceso a Log Analytics](log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

La creación y configuración de áreas de trabajo de cliente se pueden automatizar mediante [PowerShell](log-analytics-powershell-workspace-configuration.md), las [plantillas de Resource Manager](log-analytics-template-workspace-configuration.md) o la [API de REST](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

El uso de plantillas de Resource Manager para la configuración de áreas de trabajo permite tener una configuración maestra que puede usarse para crear y configurar áreas de trabajo. Puede estar seguro de que cuando cree áreas de trabajo para los clientes se configuran automáticamente de acuerdo con sus requisitos. Al actualizar los requisitos, la plantilla se actualiza y se vuelve a aplicar a las áreas de trabajo existentes. Este proceso garantiza que incluso los espacios de trabajo existentes cumplan los nuevos estándares.    

Al administrar varias áreas de trabajo de Log Analytics, se recomienda integrar cada una de ellas en su propio sistema de control de vales o consola de operaciones mediante la funcionalidad de [alertas](log-analytics-alerts.md). Mediante la integración con los sistemas existentes, el personal de soporte técnico puede continuar con sus procesos habituales. Log Analytics comprueba con regularidad cada área de trabajo con los criterios de alerta que especifique y genera una alerta cuando tenga que tomar medidas.

Para obtener vistas personalizadas de los datos, use la funcionalidad [panel](../azure-portal/azure-portal-dashboards.md) en Azure Portal.  

Para los informes de nivel ejecutivo que resumen los datos entre áreas de trabajo puede usar la integración entre Log Analytics y [PowerBI](log-analytics-powerbi.md). Si necesita integrarlas con otro sistema informes, puede utilizar la API de búsqueda (a través de PowerShell o [REST](log-analytics-log-search-api.md)) para ejecutar consultas y exportar los resultados de búsqueda.

## <a name="next-steps"></a>Pasos siguientes
* Automatice la creación y configuración de áreas de trabajo con [plantillas de Resource Manager](log-analytics-template-workspace-configuration.md).
* Automatice la creación de áreas de trabajo con [PowerShell](log-analytics-powershell-workspace-configuration.md). 
* Use [alertas](log-analytics-alerts.md) para integrarse con sistemas existentes.
* Genere informes de resumen con [PowerBI](log-analytics-powerbi.md).

