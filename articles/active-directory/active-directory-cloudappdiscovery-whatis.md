---
title: "Búsqueda de aplicaciones en la nube no administradas con Cloud App Discovery en Azure Active Directory | Microsoft Docs"
description: "Ofrece información sobre la búsqueda y la administración de aplicaciones con Cloud App Discovery, sobre cuáles son los beneficios y sobre cómo funciona."
services: active-directory
keywords: cloud app discovery, administrar aplicaciones
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 35b898aa3c03aeef914a7df574ac65a22a6c7bec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Búsqueda de aplicaciones en la nube no administradas con Cloud App Discovery
## <a name="summary"></a>Resumen

Cloud App Discovery es una característica de Azure Active Directory Premium que permite detectar aplicaciones en la nube no administradas usadas por las personas de su organización. En las empresas modernas, los departamentos de TI a menudo no son conscientes de todas las aplicaciones en la nube que usan los miembros de su organización para realizar su trabajo. Es fácil ver por qué a los administradores les podría preocupar el acceso no autorizado a datos corporativos, la posible pérdida de datos y otros riesgos de seguridad. Esta falta de conciencia puede hacer que un plan para tratar estos riesgos de seguridad parezca abrumador.

> [!TIP] 
> Eche un vistazo a las mejoras de Cloud App Discovery en Azure Active Directory (Azure AD), entre las que se encuentran la [integración con Microsoft Cloud App Security](https://portal.cloudappsecurity.com).

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


## <a name="next-steps"></a>Pasos siguientes
* [Consideraciones de seguridad y privacidad de Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Configuración del registro de Cloud App Discovery para servidores proxy con puertos personalizados](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Registro de cambios del agente de Cloud App Discovery ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

