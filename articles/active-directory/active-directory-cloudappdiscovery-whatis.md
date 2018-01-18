---
title: "Búsqueda de aplicaciones en la nube no administradas con Cloud App Discovery en Azure Active Directory | Microsoft Docs"
description: "Ofrece información sobre la búsqueda y la administración de aplicaciones con Cloud App Discovery, sobre cuáles son los beneficios y sobre cómo funciona."
services: active-directory
keywords: cloud app discovery, administrar aplicaciones
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1d0ad06fc7eec07f8e1e0ba47121b6eec01c87df
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Búsqueda de aplicaciones en la nube no administradas con Cloud App Discovery
## <a name="summary"></a>Resumen

Cloud App Discovery en Azure Active Directory ahora ofrece una experiencia mejorada de detección sin agente con la tecnología de Microsoft Cloud App Security. Para usar Cloud App Discovery, simplemente inicie sesión con sus credenciales de Azure AD Premium P1. Esta actualización está disponible sin costo adicional para todos los clientes de Azure AD Premium P1. Comience por el artículo [Configurar Cloud App Discovery en Azure AD](https://docs.microsoft.com/azure/active-directory/cloudappdiscovery-get-started) y, a continuación, consulte [Microsoft Cloud App Security](https://portal.cloudappsecurity.com/).

> [!IMPORTANT] 
> La experiencia de Azure AD Cloud App Discovery con detección basada en agente será desactivada el 5 de marzo de 2018; después, los agentes se deshabilitarán y los datos se eliminarán. Tome acciones antes del 5 de marzo para comenzar a trabajar con la nueva experiencia y evitar la interrupción del servicio.  
 
**Con Cloud App Discovery, puede:**

* Encuentre las aplicaciones de nube que se están usando y mida ese uso por el número de usuarios, el volumen de tráfico o el número de solicitudes web a la aplicación.
* Identificar a los usuarios que están usando una aplicación.
* Exporte datos para análisis sin conexión.
* Lleve estas aplicaciones bajo el control de TI y habilite el inicio de sesión único para la administración de usuarios.

## <a name="how-it-works"></a>Cómo funciona
1. Los agentes de uso de aplicaciones se instalan en los equipos de los usuarios.
2. La información de uso de aplicaciones que capturan los agentes se envía a través de un canal seguro y cifrado al servicio Cloud App Discovery.
3. El servicio Cloud App Discovery evalúa los datos y genera informes.

![Diagrama de Cloud App Discovery](./media/active-directory-cloudappdiscovery/cad01.png)


## <a name="next-steps"></a>pasos siguientes
* [Consideraciones de seguridad y privacidad de Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Configuración del registro de Cloud App Discovery para servidores proxy con puertos personalizados](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Registro de cambios del agente de Cloud App Discovery ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

