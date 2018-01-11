---
title: "Migración de directivas clásicas en Azure Portal | Microsoft Docs"
description: "Aprenda todo lo necesario sobre cómo migrar las directivas clásicas en Azure Portal."
services: active-directory
keywords: acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 16628bd4fa41d2e7697e1c2501f2ccd31dbd0496
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Migración de directivas clásicas en Azure Portal 


El [acceso condicional](active-directory-conditional-access-azure-portal.md) es una funcionalidad de Azure Active Directory (Azure AD) que le permite controlar cómo acceden los usuarios autorizados a las aplicaciones en la nube. Aunque el propósito sigue siendo el mismo, la nueva versión de Azure Portal presenta mejoras importantes acerca de cómo funciona el acceso condicional.

Debería considerar la posibilidad de migrar las directivas que no haya creado en Azure portal porque:

- Ahora puede resolver situaciones que antes no podía controlar.

- Puede reducir el número de directivas que tiene que administrar mediante su consolidación.   

- Puede administrar todas las directivas de acceso condicional en una ubicación central.

- Se retirará el Portal de Azure clásico.   

En este artículo se explica lo que necesita saber para migrar las directivas de acceso condicional existentes al marco nuevo.
 
## <a name="classic-policies"></a>Directivas clásicas

En [Azure Portal](https://portal.azure.com), la página [ Acceso condicional - Directivas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) es el punto de entrada a las directivas de acceso condicional. Sin embargo, en su entorno, también podría tener directivas de acceso condicional que no creara con esta página. Estas directivas se conocen como *directivas clásicas*. Las directivas de clásicas son directivas de acceso condicional que creó con:

- El Portal de Azure clásico
- El Portal de Intune clásico
- El Portal de Intune App Protection


En la página **Acceso condicional**, puede tener acceso a las directivas clásicas si hace clic en [ **Directivas clásicas (versión preliminar)**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) en la sección **Administrar**. 


![Azure Active Directory](./media/active-directory-conditional-access-migration/71.png)


La vista **Directivas clásicas** le permite:

- Filtrar las directivas clásicas.
 
    ![Azure Active Directory](./media/active-directory-conditional-access-migration/72.png)

- Deshabilitar las directivas clásicas.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/73.png)
   
- Revisar la configuración de las directivas clásicas (y desactivarla).

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/74.png)


Si deshabilitó una directiva clásica, ya no podrá revertir ese paso. Por ese motivo puede modificar la pertenencia a un grupo en una directiva clásica mediante la vista **Detalles**. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/75.png)

Si cambia los grupos seleccionados o excluye grupos específicos, puede probar el efecto de una directiva clásica deshabilitada para algunos usuarios de prueba antes de deshabilitar la directiva para todos los usuarios y grupos. 



## <a name="azure-ad-conditional-access-policies"></a>Directivas de acceso condicional de Azure AD

Puede administrar todas las directivas en una ubicación central mediante el acceso condicional en Azure Portal. Dado que la implementación sobre cómo funciona el acceso condicional ha cambiado considerablemente, debe familiarizarse con los conceptos básicos antes de migrar las directivas clásicas.

Consulte:

- [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md) para conocer la terminología y los conceptos básicos.

- [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md) para obtener información sobre la implementación de acceso condicional en su organización.

- [Introducción al acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) para familiarizarse con la interfaz de usuario de Azure Portal.


 
## <a name="migration-considerations"></a>Consideraciones sobre la migración

En este artículo, las directivas de acceso condicional de Azure AD también se conocen como *directivas nuevas*.
Las directivas clásicas seguirán funcionando con las directivas nuevas hasta que las deshabilite o las elimine. 

Los aspectos siguientes son importantes en el contexto de una consolidación de directiva:

- Aunque las directivas clásicas están asociadas a una aplicación de nube específica, puede seleccionar tantas como necesite en una directiva nueva.

- Los controles de una directiva clásica y una directiva nueva para una aplicación de nube requieren que se cumplan todos ellos (*AND*). 


- En una directiva nueva puede:
 
    - Combinar varias condiciones, si la situación lo requiere. 

    - Seleccionar varios requisitos de concesión como control de acceso y combinarlos con un valor lógico *OR* (se requiere uno de los controles seleccionados) o con un valor lógico *AND* (se requieren todos los controles seleccionados).

        ![Azure Active Directory](./media/active-directory-conditional-access-migration/25.png)




### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

Si quiere migrar las directivas clásicas para **Office 365 Exchange Online** que incluyen **Exchange Active Sync** como condición de aplicaciones de cliente, es posible que no pueda consolidarlas en una directiva nueva. 

Esto sucede en caso de que quiera admitir todos los tipos de aplicación de cliente. En una directiva nueva que tiene **Exchange Active Sync** como condición de aplicaciones de cliente no puede seleccionar otras aplicaciones cliente.

![Azure Active Directory](./media/active-directory-conditional-access-migration/64.png)

Tampoco es posible una consolidación en una directiva nueva si las directivas clásicas contienen varias condiciones. Una directiva nueva que tiene **Exchange Active Sync** como condición de aplicaciones de cliente no admite otras condiciones:   

![Azure Active Directory](./media/active-directory-conditional-access-migration/08.png)

Si existe una directiva nueva que tiene **Exchange Active Sync** como condición de aplicaciones de cliente configurada, debe asegurarse de que todas las demás condiciones no están configuradas. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/16.png)
 

Las directivas clásicas [basadas en aplicaciones](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) para Office 365 Exchange Online que incluyen **Exchange Active Sync** como condición de aplicaciones de cliente permiten [plataformas de dispositivos](active-directory-conditional-access-technical-reference.md#device-platform-condition) **admitidas** y **no admitidas**. Aunque no se pueda configurar plataformas de dispositivos individuales en una directiva nueva relacionada, puede limitar la compatibilidad solo para [plataformas de dispositivos compatibles](active-directory-conditional-access-technical-reference.md#device-platform-condition). 

![Azure Active Directory](./media/active-directory-conditional-access-migration/65.png)

Puede consolidar varias directivas clásicas que incluya **Exchange Active Sync** como condición de aplicaciones de cliente si tienen:

- **Exchange Active Sync** como única condición 

- Varios requisitos configurados para la concesión de acceso

Una situación habitual es la consolidación de:

- Una directiva clásica basada en dispositivos desde el Portal de Azure clásico 
- Una directiva clásica basada aplicaciones desde el Portal de Intune App Protection 
 
En este caso, puede consolidar las directivas clásicas en una nueva directiva que tenga seleccionados ambos requisitos.

![Azure Active Directory](./media/active-directory-conditional-access-migration/62.png)



### <a name="device-platforms"></a>Plataformas de dispositivo

Las directivas de clásicas con [controles basados en aplicaciones](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) están preconfiguradas con iOS y Android como la [condición de la plataforma del dispositivo](active-directory-conditional-access-technical-reference.md#device-platform-condition). 

En una directiva nueva, debe seleccionar las [plataformas de dispositivo](active-directory-conditional-access-technical-reference.md#device-platform-condition) que quiera admitir de forma individual.

![Azure Active Directory](./media/active-directory-conditional-access-migration/41.png)



 
 


## <a name="next-steps"></a>Pasos siguientes

- Si quiere saber cómo configurar una directiva de acceso condicional, consulte [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introducción al acceso condicional en Azure Active Directory).

- Si está listo para configurar directivas de acceso condicional para su entorno, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md). 
