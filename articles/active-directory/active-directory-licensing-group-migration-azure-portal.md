---
title: "Migración de usuarios individuales con licencia a un grupo en Azure Active Directory | Microsoft Docs"
description: Cambio de licencias de usuarios individuales por licencias basadas en grupos mediante Azure Active Directory
services: active-directory
keywords: Licencias de Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6b77dd4e9a6d361a05382397e89b575896fdad84
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>Cómo agregar usuarios con licencia a un grupo para la obtención de licencias en Azure Active Directory

Puede que actualmente haya licencias implementadas para usuarios de organizaciones a través de una "asignación directa"; es decir, mediante el uso de scripts de PowerShell u otras herramientas diseñadas para asignar licencias de usuarios individuales. Si desea empezar a usar licencias basadas en grupos para administrar las licencias de su organización, necesita contar con un plan de migración para reemplazar las soluciones existentes por licencias basadas en grupos con facilidad.

Lo más importante es tener en cuenta que hay que evitar una situación tal en que la migración a licencias basadas en grupos conlleve que los usuarios pierdan temporalmente las licencias que actualmente tienen asignadas. Se debe evitar cualquier proceso que pueda dar lugar a la eliminación de licencias a fin de evitar el riesgo de que los usuarios pierdan el acceso a los servicios y a sus datos.

## <a name="recommended-migration-process"></a>Proceso de migración recomendado

1. Cuenta actualmente con un sistema de automatización (por ejemplo, PowerShell) que administra la asignación y la retirada de licencias de los usuarios. Deje que se ejecute de la forma habitual.

2. Cree un grupo de licencias nuevo (o decida qué grupos utilizar de entre los existentes) y asegúrese de que todos los usuarios necesarios se agregan como miembros.

3. Asigne las licencias necesarias a esos grupos; el objetivo debe consistir en reflejar el mismo estado de licencia que el sistema de automatización (por ejemplo, PowerShell) aplica a dichos usuarios.

4. Verifique que dichas licencias se hayan aplicado a todos los usuarios de esos grupos. Para ello, compruebe el estado de procesamiento de cada grupo y los registros de auditoría.

  - Puede realizar una revisión rápida de cada usuario observando los detalles de sus licencias. Observará que tienen las mismas licencias asignadas "directamente" o "heredadas" de los grupos.

  - Puede ejecutar un script de PowerShell para [verificar cómo se asignan las licencias a los usuarios](active-directory-licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

  - Cuando se asigna la misma licencia de producto al usuario directamente y a través de un grupo, el usuario solo puede consumir una licencia. Por lo tanto, no se necesitan licencias adicionales para realizar la migración.

5. Compruebe si en algún grupo de usuarios aparece el estado de error, a fin de verificar que no se produzcan errores en las asignaciones de licencias. Para más información, vea [Identificación y resolución de problemas de licencias de un grupo](active-directory-licensing-group-problem-resolution-azure-portal.md).

6. Considere la posibilidad de quitar las asignaciones directas originales; puede que desee hacerlo gradualmente en "ondas" para supervisar primero el resultado en un subconjunto de usuarios.

  Puede dejar las asignaciones directas originales de los usuarios, pero, en ese caso, cuando el usuario deja los grupos con licencia a los que pertenecen, conserva su licencia original, y posiblemente no es esto lo que desea.

## <a name="an-example"></a>Un ejemplo

Tenemos una organización con 1000 usuarios. Todos los usuarios necesitan la licencia de Enterprise Mobility + Security (EMS). 200 usuarios trabajan en el departamento financiero y necesitan licencias para Office 365 Enterprise E3. Existe un script de PowerShell que se ejecuta a nivel local mediante la adición y retirada de licencias de usuarios a medida que se incorporan y que se van. Se va a reemplazar el script con licencias basadas en grupos, de tal forma que Azure AD administre las licencias automáticamente.

El proceso de migración podría ser similar al siguiente:

1. En Azure Portal, asigne la licencia de EMS al grupo **Todos los usuarios** de Azure AD. Asigne la licencia de E3 al grupo **Departamento financiero** que contiene todos los usuarios necesarios.

2. Confirme en cada grupo que se haya completado la asignación de licencia a todos los usuarios. Vaya a la hoja de cada grupo, seleccione **Licencias** y compruebe el estado de procesamiento en la parte superior de la hoja **Licencias**.

  - Busque "Latest license changes have been applied to all users" (Los últimos cambios de licencia se han aplicado a todos los usuarios) para confirmar que el procesamiento se ha completado.

  - Busque si hay alguna notificación en la parte superior sobre algún usuario cuya licencia no se haya podido asignar correctamente. ¿Se han agotado las licencias para algunos usuarios? ¿Algunos usuarios tienen SKU de licencias conflictivas que les impidan heredar las licencias de grupo?

3. Realice una revisión rápida de algunos usuarios para verificar que tengan aplicadas tanto licencias directas como de grupo. Vaya a la hoja de un usuario, seleccione **Licencias** y examine el estado de las licencias.

  - Este es el estado de usuario esperado durante la migración:

      ![estado de usuario esperado](media/active-directory-licensing-group-migration-azure-portal/expected-user-state.png)

  Esto confirma que el usuario tiene licencias directas y heredadas. Se observa que tiene asignadas las licencias para **EMS** y **E3**.

  - Seleccione cada licencia para ver la información sobre los servicios habilitados. Se puede usar para comprobar si las licencias directas y de grupo habilitan exactamente los mismos planes de servicio para el usuario.

      ![comprobación de planes de servicio](media/active-directory-licensing-group-migration-azure-portal/check-service-plans.png)

4. Después de confirmar que las licencias directas y de grupo son equivalentes, puede empezar a quitar a los usuarios las licencias directas. Para probarlo, quítelos para usuarios individuales en el portal y luego ejecute los scripts de automatización para quitarlos en masa. Este es un ejemplo del mismo usuario con las licencias directas quitadas a través del portal. Tenga en cuenta que el estado de licencia no varía, pero aún no se ven las asignaciones directas.

  ![licencias directas quitadas](media/active-directory-licensing-group-migration-azure-portal/direct-licenses-removed.png)


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre otros escenarios de administración de licencias a través de grupos, vea

* [Assigning licenses to a group in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) (Asignación de licencias a un grupo en Azure Active Directory)
* [¿En qué consisten las licencias basadas en grupos de Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Identificación y resolución de problemas de licencias de un grupo en Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory group-based licensing additional scenarios](active-directory-licensing-group-advanced.md) (Escenarios adicionales de licencias basadas en grupos de Azure Active Directory)
