---
title: Planeamiento de la capacidad de los roles de servidor de Azure App Service en Azure Stack | Microsoft Docs
description: Planeamiento de la capacidad de los roles de servidor de Azure App Service en Azure Stack
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: anwestg
ms.openlocfilehash: 4ad91def00ca73f91f0ffd8e57afa442a93176f6
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Planeamiento de la capacidad de los roles de servidor de Azure App Service en Azure Stack

Para proporcionar una implementación lista para producción de Azure App Service en Azure Stack, debe planear la capacidad que espera que el sistema admita.  Esta es una guía del número mínimo de SKU de proceso e instancias que debe usar para una implementación de producción.

Puede planear la estrategia de capacidad de App Service mediante estas instrucciones. Las versiones futuras de Azure Stack proporcionarán opciones de alta disponibilidad para App Service.

| Rol de servidor de App Service | Número mínimo recomendado de instancias | SKU de proceso recomendadas|
| --- | --- | --- |
| Controller | 2 | A1 |
| Front End | 2 | A1 |
| Administración | 2 | A3 |
| Publicador | 2 | A1 |
| Roles de trabajo web: compartidos | 2 | A1 |
| Roles de trabajo web: dedicados | 2 por nivel | A1 |

## <a name="controller-role"></a>Rol de controlador

**Mínimo recomendado**: dos instancias del estándar A1

El controlador de servicio de Azure App Service suele tener un consumo bajo de CPU, memoria y recursos de red. Sin embargo, para lograr una alta disponibilidad, debe tener dos controladores. Dos controladores también son el número máximo de controladores permitidos. Puede crear el segundo controlador de Web Sites directamente desde el instalador durante la implementación.

## <a name="front-end-role"></a>Rol de front-end

**Mínimo recomendado**: dos instancias del estándar A1

El front-end enruta las solicitudes a los trabajos web según la disponibilidad de estos. Para lograr una alta disponibilidad, debe tener más de un front-end y puede tener más de dos. A fin de planear la capacidad, tenga en cuenta que cada núcleo puede atender aproximadamente 100 solicitudes por segundo.

## <a name="management-role"></a>Rol de administración

**Mínimo recomendado**: dos instancias de A3

El rol de administración de Azure App Service es responsable de los puntos de conexión de API y Azure Resource Manager de App Service, las extensiones de portales (portal de Functions, administración e inquilino) y el servicio de datos. El rol de servidor de administración solo suele requerir aproximadamente 4 GB de RAM en un entorno de producción. Sin embargo, puede experimentar niveles elevados de CPU cuando se realizan muchas tareas de administración (por ejemplo, la creación de sitios web). Para lograr una alta disponibilidad, debe tener más de un servidor asignado a este rol y al menos dos núcleos por servidor.

## <a name="publisher-role"></a>Rol de publicador

**Mínimo recomendado**: dos instancias de A1

Si muchos usuarios están publicando simultáneamente, el rol de publicador puede experimentar una utilización elevada de CPU. Para lograr una alta disponibilidad, disponga de más de un rol de publicador.  El publicador solo controla el tráfico FTP o FTPS.

## <a name="web-worker-role"></a>Rol de trabajo web

**Mínimo recomendado**: dos instancias de A1

Para lograr una alta disponibilidad, debe tener al menos cuatro roles de trabajo web, dos para el modo de sitio web compartido y dos para cada nivel de trabajo dedicado que planee ofrecer. Los modos de proceso dedicado y compartido proporcionan diferentes niveles de servicio a los inquilinos. Si tiene muchos clientes, puede que necesite más roles de trabajo web:
 - que usen niveles de trabajo de modo de proceso dedicado (que consumen gran cantidad de recursos)
 - que se ejecuten en modo de proceso compartido.

Una vez que un usuario haya creado un plan de App Service para la SKU de un modo de proceso dedicado, el número de trabajos web especificado en el plan de App Service ya no estará disponible para los usuarios.

Para proporcionar Azure Functions a los usuarios en el modelo de plan de consumo, debe implementar los roles de trabajo web compartidos.

Cuando decida el número de roles de trabajo web compartidos que utilizar, revise estas consideraciones:

- **Memoria**: es el recurso más crítico para un rol de trabajo web. Si no hay memoria suficiente, afecta al rendimiento del sitio web cuando se intercambia memoria virtual del disco. Cada servidor necesita aproximadamente 1,2 GB de RAM para el sistema operativo. La RAM por encima de este umbral puede utilizarse para ejecutar sitios web.
- **Porcentaje de sitios web activos**: por lo general, aproximadamente el cinco por ciento de las aplicaciones en una instancia de Azure App Service en Azure Stack están activas. Sin embargo, el porcentaje de aplicaciones que están activas en un momento dado puede ser superior o inferior. Con una tasa de aplicaciones activas del cinco por ciento, el número máximo de aplicaciones que colocar en una instancia de Azure App Service en la implementación de Azure Stack debe ser menor de:
    - 20 veces el número de sitios web activos (5 x 20 = 100).
- **Cantidad de memoria promedio**: la cantidad de memoria promedio para las aplicaciones observada en entornos de producción es de aproximadamente 70 MB. Por lo tanto, la memoria asignada en todos los equipos con el rol de trabajo web o en las máquinas virtuales se puede calcular de la siguiente manera:

    *Número de aplicaciones aprovisionado * 70 MB * 5 % - (número de roles de trabajo web * 1044 MB)*

   Por ejemplo, si hay 5000 aplicaciones en el entorno que ejecuta diez roles de trabajo web, cada máquina virtual con el rol de trabajo web debe tener 7060 MB de memoria RAM:

   5000 * 70 * 0,05: (10 * 1044) = 7060 (= aproximadamente 7 GB)

   Para obtener información sobre cómo agregar más instancias de trabajo, consulte [Incorporación de más roles de trabajo](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Rol de servidor de archivos

Para el rol de servidor de archivos, puede usar un servidor de archivos independiente para desarrollo y pruebas. Por ejemplo, cuando implemente Azure App Service en Azure Stack Development Kit puede usar esta plantilla: https://aka.ms/appsvconmasdkfstemplate. Para fines de producción, debe utilizar un servidor de archivos preconfigurado de Windows o que no sea de Windows.

En entornos de producción, el rol de servidor de archivos experimenta una intensa actividad de E/S de disco. Puesto que contiene todos los archivos de contenido y de aplicación para los sitios web de usuario, debe configurar previamente uno de los elementos siguientes acciones para que ejerza este rol:
- Servidor de archivos de Windows
- Clúster de servidor de archivos
- Servidor de archivos que no sea de Windows
- clúster de servidor de archivos
- Dispositivo NAS (almacenamiento conectado a la red). Para más información, consulte [Aprovisionamiento de un servidor de archivos](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Pasos siguientes

[Antes de empezar a trabajar con App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md)
