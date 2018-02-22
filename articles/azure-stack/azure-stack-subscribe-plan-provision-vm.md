---
title: "Suscripción a una oferta en Azure Stack | Microsoft Docs"
description: "Creación de suscripciones para ofertas en Azure Stack"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 9b35b497c264fab2b8352eda82fe6d4e1fc274e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Creación de suscripciones para ofertas en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Después de [crear una oferta](azure-stack-create-offer.md), los usuarios necesitan una suscripción a dicha oferta antes de que puedan utilizarla.   
- Como operador de nube, puede crear una suscripción para un usuario en el portal de administración.  Las suscripciones que cree pueden ser para ofertas tanto públicas como privadas.
- Como usuario inquilino, puede suscribirse a una oferta pública a través del portal de usuarios.  

En las secciones siguientes se proporcionan instrucciones para operadores de nube para crear suscripciones para usuarios y sobre cómo puede un usuario suscribirse a una oferta.

## <a name="create-a-subscription-as-a-cloud-operator"></a>Creación de una suscripción como operador de nube
Los operadores de nube pueden usar el portal de administración para crear una suscripción a una oferta para un usuario.  Puede crear suscripciones para miembros de su propio inquilino de directorio.  Cuando los [servicios multiinquilino](azure-stack-enable-multitenancy.md) están habilitados, también puede crear suscripciones para usuarios de inquilinos de directorio adicionales.

Puede crear suscripciones para ofertas tanto públicas como privadas.  Si no desea que los inquilinos creen sus propias suscripciones, haga todas las ofertas privadas y, a continuación, cree suscripciones en nombre de los inquilinos. Esta estrategia es habitual cuando se integra Azure Stack con sistemas de catálogo de servicios o de facturación externa.

Después de crear una suscripción para un usuario, dicho usuario podrá iniciar sesión en el portal de usuarios y ver que está suscrito a la oferta.  

### <a name="to-create-the-subscription-for-a-user"></a>Para crear la suscripción para un usuario
1.  En el portal de administración, vaya a **Suscripciones de usuario**.
2.  Seleccione **Agregar** para abrir el panel **Nueva suscripción de usuario**. Aquí se especifican los detalles siguientes:  

  a. **Nombre para mostrar**: un nombre descriptivo para identificar la suscripción que aparece como *Nombre de suscripción de usuario*.

  b. **Usuario** : especifique un usuario de un inquilino de directorio disponible para esta suscripción. El nombre de usuario se muestra como *Propietario*.  El formato del nombre de usuario depende de la solución de identidad. Por ejemplo:    

     -  **Azure AD:**  *&lt;user1>@&lt;contoso.onmicrosoft.com>*

     -   **AD FS:**  *&lt;user1>@&lt;azurestack.local>*     

  c.    **Inquilino de directorio** : seleccione el inquilino de directorio al que pertenece la cuenta de usuario. Si no ha habilitado los servicios multiinquilino, solo estará disponible el inquilino de directorio local.

3.  Seleccione **Oferta** para abrir el panel **Oferta** y, a continuación, elija una **oferta** para esta suscripción. Dado que está creando una suscripción para un usuario, puede seleccionar una oferta pública o privada.

4.  Seleccione **Crear** para crear la suscripción. El panel **Suscripciones de usuario** ahora muestra la nueva suscripción.  Más adelante, cuando el usuario inicia sesión en el portal de usuarios, puede ver detalles acerca de esta suscripción.

### <a name="to-make-an-add-on-plan-available"></a>Para añadir un plan complementario  
Un operador de nube puede añadir un plan complementario a una suscripción creada anteriormente en cualquier momento:   
1.  En el portal de administración, vaya a **Más servicios** > **Suscripciones de usuario**y, a continuación, seleccione la suscripción que desea cambiar.   

2.  Seleccione **Complementos** > **Agregar** para abrir el panel **Agregar plan**.  

3.  Seleccione el plan que desea agregar como complemento.  A continuación, la consola muestra los planes asociados con la suscripción.




## <a name="create-a-subscription-as-a-user"></a>Creación de una suscripción como usuario
Como usuario, puede iniciar sesión en el portal de usuarios para buscar y suscribirse a ofertas públicas y planes complementarios de su inquilino de directorio (organización). Cuando el entorno de Azure Stack admite [servicios multiinquilino](azure-stack-enable-multitenancy.md), puede suscribirse a ofertas de un inquilino de directorio remoto.

### <a name="to-subscribe-to-an-offer"></a>Para suscribirse a una oferta
1. [Inicie sesión](azure-stack-connect-azure-stack.md) el portal de usuarios de Azure Stack (https://portal.local.azurestack.external/) y haga clic en **Obtener una suscripción**.

   ![Obtener una suscripción](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. En el campo **Nombre para mostrar**, escriba un nombre para la suscripción, haga clic en **Oferta**, haga clic en una de las ofertas en el panel **Elija una oferta** y, después, haga clic en **Crear**.

   ![Creación de una oferta](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. Para ver la suscripción que ha creado, haga clic en **Más servicios**, en **Suscripciones** y, luego, en la nueva suscripción.  

Después de suscribirse a una oferta, actualice el portal para ver los servicios que forman parte de la nueva suscripción.

### <a name="to-subscribe-to-an-add-on-plan"></a>Para suscribirse a un plan complementario
Si la oferta tiene un plan complementario, puede añadirlo a su suscripción en cualquier momento.  

1. En el portal de usuarios, seleccione **Más servicios** > **Suscripciones** y seleccione la suscripción que desea cambiar.

2. Seleccione el botón **Agregar plan** y, a continuación, seleccione el plan complementario que desee. Si **Agregar plan** no está disponible, no hay ningún plan complementario para la oferta asociado a esa suscripción.



## <a name="next-steps"></a>pasos siguientes
[Aprovisionamiento de una máquina virtual](azure-stack-provision-vm.md)
