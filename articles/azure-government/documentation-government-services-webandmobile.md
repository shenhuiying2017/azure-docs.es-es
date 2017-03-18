---
title: "API, web y móvil de Azure Government | Microsoft Docs"
description: "Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure"
services: azure-government
cloud: gov
documentationcenter: 
author: brendalee
manager: zakramer
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/5/2016
ms.author: brendal
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: f0ba3e50c4281a0e8c365be075ae45ab72f6e82d
ms.lasthandoff: 12/14/2016

---
# <a name="azure-government-web--mobile"></a>Web + Móvil de Azure Government
## <a name="app-services"></a>Servicios de aplicaciones
### <a name="variations"></a>Variaciones
Azure App Services está disponible con carácter general en Azure Government.

La dirección para las aplicaciones de Azure App Services creadas en Azure Government es diferente de las creadas en la nube pública:

| Tipo de servicio | Azure Public | Azure Government |
| --- | --- | --- |
| App Service |*.azurewebsites.net |*.azurewebsites.us|

Algunas características de App Service disponibles en la nube pública aún no están disponibles en Azure Government:

- Entornos del Servicio de aplicaciones
- Implementación de aplicaciones
    - Entrega continua (versión preliminar)
- Settings
    - Integración con red virtual y conexiones híbridas
    - Examen de seguridad
- Herramientas de desarrollo
    - Prueba de rendimiento
    - Explorador de recursos
    - Depuración de PHP
- Supervisión
    - Application Insights
    - Métricas por instancia
    - Tráfico HTTP en vivo
    - Eventos de aplicación
    - Registros de FREB
- Soporte y solución de problemas
    - App Service Advisor
    - Historial de errores
    - Diagnóstico como servicio
    - Mitigación


### <a name="considerations"></a>Consideraciones
La siguiente información identifica el límite de Azure Government para App Service:

| Se permiten datos regulados o controlados | No se permiten datos regulados o controlados |
| --- | --- |
| Los datos especificados, almacenados y procesados dentro de Azure App Service pueden contener datos controlados para exportación. Archivos binarios que se ejecutan dentro de Azure App Service. Autenticadores estáticos, como contraseñas y PIN de smartcard para el acceso a componentes de la plataforma Azure. Claves privadas de certificados que se usan para administrar los componentes de la plataforma Azure. Cadenas de conexión SQL. Otros secretos o información de seguridad, como certificados, claves de cifrado, claves maestras y claves de almacenamiento almacenadas en servicios de Azure. |Los metadatos no pueden contener datos controlados para exportación. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener su instancia de Azure App Service. No inserte datos regulados o controlados en los siguientes campos: Grupos de recursos, Nombres de recursos o Etiquetas de recursos.|

## <a name="next-steps"></a>Pasos siguientes
Para información complementaria y actualizaciones, suscríbase al [blog de Microsoft Azure Government.](https://blogs.msdn.microsoft.com/azuregov/)


