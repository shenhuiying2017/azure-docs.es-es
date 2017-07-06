---
title: "Búsqueda de aplicaciones en la nube no administradas con Cloud App Discovery | Microsoft Docs"
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
ms.date: 07/05/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 011cdf5f1e1b78832a8e4f18f4eef0f376860c45
ms.contentlocale: es-es
ms.lasthandoff: 12/28/2016


---
# <a name="finding-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Búsqueda de aplicaciones de nube no administradas con Cloud App Discovery
## <a name="overview"></a>Información general
En las empresas modernas, los departamentos de TI a menudo no son conscientes de todas las aplicaciones en la nube que usan los miembros de su organización para realizar su trabajo. Es fácil ver por qué a los administradores les podría preocupar el acceso no autorizado a datos corporativos, la posible pérdida de datos y otros riesgos de seguridad. Esta falta de conciencia puede hacer que un plan para tratar estos riesgos de seguridad parezca abrumador.

Cloud App Discovery es una característica de Azure Active Directory (AD) Premium que permite detectar aplicaciones de nube usadas por las personas de su organización.

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

Para empezar con Cloud App Discovery, vea [Introducción a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

## <a name="related-articles"></a>Artículos relacionados
* [Consideraciones de seguridad y privacidad de Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Guía de implementación de la directiva de grupo de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)
* [Guía de implementación de System Center de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)
* [Configuración del registro de Cloud App Discovery para servidores proxy con puertos personalizados](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Registro de cambios del agente de Cloud App Discovery ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Preguntas más frecuentes de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)


