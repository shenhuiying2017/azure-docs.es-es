---
title: "Azure AD Connect Health y el Reglamento general de protección de datos | Microsoft Docs"
description: "En este documento se describe cómo obtener el cumplimiento con el GDPR en Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2018
ms.author: billmath
ms.openlocfilehash: b9a0b9027bbead00300040186e453933b3a7f46b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect-health"></a>Cumplimiento con GDPR y Azure AD Connect Health 

El [Reglamento general de protección de datos (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) es una ley de privacidad y protección de datos de la Unión Europea (UE). El RGPD impone nuevas reglas sobre las empresas, agencias gubernamentales, entidades sin ánimo de lucro y otras organizaciones que ofrecen bienes y servicios a personas de la UE o que recopilan y analizan datos vinculados a residentes en la UE. 

En la actualidad, hay productos y servicios de Microsoft para ayudarle a cumplir los requisitos de GDPR. Puede encontrar más información sobre la directiva de privacidad de Microsoft en [Trust Center](https://www.microsoft.com/trustcenter).

Azure AD Connect Health supervisa la infraestructura de identidad local y el servicio de sincronización. También proporciona información detallada y alertas de superficie. Microsoft se compromete al cumplimiento del GDPR en los servicios en la nube a partir de mayo de 2018 y a proporcionar garantías relacionadas con GDPR en sus compromisos contractuales. 

>[!NOTE] 
> En este artículo se describe el cumplimiento del GDPR en Azure AD Connect Health. Para más información sobre el cumplimiento del RGPD en Azure AD Connect, consulte [Cumplimiento de RGPD y Azure AD Connect](../../active-directory/connect/active-directory-aadconnect-gdpr.md).

## <a name="gdpr-classification"></a>Clasificación del RGPD
Azure AD Connect Health se incluye dentro de la categoría de **procesador de datos** de la clasificación del GDPR. Como canalización del procesador de datos, el servicio proporciona servicios de procesamiento de datos a asociados clave y consumidores finales. Azure AD Connect Health no genera datos de usuario y no tiene control independiente sobre qué datos personales se recopilan y cómo se usan. La recuperación de datos, la agregación, el análisis y los informes en Azure AD Connect Health se basan en los datos locales existentes. 

## <a name="data-retention-policy"></a>Directiva de retención de datos
Azure AD Connect Health no genera informes, ni realiza análisis ni proporciona información después de 30 días. Por lo tanto, no almacena, procesa ni retiene datos más allá de ese período. Este diseño es compatible con el reglamento del GDPR, las regulaciones de cumplimiento de la privacidad de Microsoft y las directivas de retención de datos de Azure AD. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Deshabilitación de la recopilación de datos y la supervisión en Azure AD Connect Health
Azure AD Connect Health le permite detener la recopilación de datos de cada servidor supervisado individual o de una instancia de un servicio supervisado. Por ejemplo, puede detener la recopilación de datos de servidores de ADFS (Servicios de federación de Active Directory) individuales que se supervisan mediante Azure AD Connect Health. También puede detener la recopilación de datos de la instancia completa de ADFS que se supervisa mediante Azure AD Connect Health. Si decide hacerlo, los servidores correspondientes se eliminan del portal de Azure AD Connect Health después de detener la recopilación de datos. 

>[!IMPORTANT]
> Para eliminar servidores supervisados de Azure AD Connect Health, necesita privilegios de administrador global de Azure AD o tener el rol de colaborador en RBAC.
>
> Quitar un servidor o una instancia de servicio de Azure AD Connect Health no es una acción reversible. 

### <a name="what-to-expect"></a>Qué cabe esperar
Si detiene la recopilación de datos y la supervisión de un servidor supervisado individual o de una instancia de un servicio supervisado, tenga en cuenta lo siguiente:

- Cuando se elimina una instancia de un servicio supervisado, se quita la instancia de la lista de servicios de supervisión de Azure AD Connect Health en el portal. 
- Cuando se elimina un servidor supervisado o una instancia de un servicio supervisado, no se desinstala el Agente de mantenimiento no se quita de los servidores. El Agente de mantenimiento se configura para no enviar datos a Azure AD Connect Health. Debe desinstalar manualmente el Agente de mantenimiento en los servidores supervisados previamente.
- Si no ha desinstalado el agente de mantenimiento antes de realizar este paso, es posible que vea eventos de error en los servidores relacionados con el agente de mantenimiento.
- Todos los datos que pertenecen a la instancia del servicio supervisado se eliminan según la directiva de retención de datos de Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Deshabilitación de la recopilación de datos y la supervisión en un servidor supervisado
Consulte [cómo eliminar un servidor de Azure AD Connect Health](active-directory-aadconnect-health-operations.md#to-delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Deshabilitación de la recopilación de datos y la supervisión en una instancia de un servicio supervisado
Consulte [cómo eliminar una instancia de servicio de Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Nueva habilitación de la recopilación de datos y la supervisión en Azure AD Connect Health
Para volver a habilitar la supervisión en Azure AD Connect Health en un servicio supervisado previamente eliminado, debe desinstalar y [volver a instalar el Agente de mantenimiento](active-directory-aadconnect-health-agent-install.md) en todos los servidores.


## <a name="next-steps"></a>pasos siguientes
* [Revisar la directiva de privacidad de Microsoft en Trust Center](https://www.microsoft.com/trustcenter)
* [Azure AD Connect y GDPR](../../active-directory/connect/active-directory-aadconnect-gdpr.md)
* [Operaciones de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
