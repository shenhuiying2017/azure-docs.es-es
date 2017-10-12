---
title: "Delegación de ofertas en Azure Stack | Microsoft Docs"
description: "Obtenga información acerca de cómo delegar a otras personas sus tareas de crear ofertas y registrar a usuarios."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: 157f0207-bddc-42e5-8351-197ec23f9d46
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: alfredop
ms.openlocfilehash: 48b75233e1a485e8fb3fbce9776a6ef398c4e99c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="delegating-offers-in-azure-stack"></a>Delegación de ofertas en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Como operador de Azure Stack, es posible que a veces quiera delegar a otras personas las tareas de creación de ofertas y registro de usuarios. Por ejemplo, si es un proveedor de servicios y quiere que los distribuidores registren a los clientes y los administren en su nombre. Esto también puede ocurrir en una empresa si forma parte de un grupo de TI central y quiere que las divisiones o las subsidiarias registren usuarios sin su intervención.

La delegación le ayuda con estas tareas y le permite alcanzar y administrar a más usuarios que si lo hiciera directamente. En la siguiente ilustración se muestra un nivel de delegación, pero Azure Stack admite varios niveles. Los proveedores delegados pueden a su vez delegar las tareas a otros proveedores, en hasta cinco niveles.

![](media/azure-stack-delegated-provider/image1.png)

Los operadores de Azure Stack pueden delegar la creación de ofertas y usuarios a otros usuarios mediante la funcionalidad de delegación.

## <a name="roles-and-steps-in-delegation"></a>Roles y pasos de la delegación
Para comprender la delegación, es necesario tener en cuenta que existen tres roles implicados:

* El operador de Azure Stack administra la infraestructura de Azure Stack, crea una plantilla de oferta y delega a otras personas para que la ofrezcan a sus usuarios.
* Los operadores de Azure Stack se denominan **proveedores delegados**. Pueden pertenecer a otras organizaciones (por ejemplo, a otros usuarios de Azure Active Directory).
* Los **usuarios** se suscriben a las ofertas y las usan para administrar las cargas de trabajo, crear máquinas virtuales, almacenar datos, etc.

Como se muestra en el siguiente gráfico, hay dos pasos para configurar la delegación.

1. **Identificar a los proveedores delegados** suscribiéndolos a una oferta basada en un plan que contiene solo el servicio de suscripciones.
   Los usuarios que se suscriben a esta oferta adquieren algunas de las capacidades del operador de Azure Stack, incluida la capacidad para ampliar las ofertas y registrar a los usuarios para que puedan disfrutarlas.
2. **Delegar una oferta al proveedor delegado**: esta oferta funciona como una plantilla para lo que puede ofrecer el proveedor delegado. El proveedor delegado ya puede coger la oferta, asignarle un nombre (pero sin cambiar sus servicios ni cuotas) y ofrecerla a sus clientes.

![](media/azure-stack-delegated-provider/image2.png)

Para actuar como proveedor delegado, los usuarios deben establecer una relación con el proveedor principal, en otras palabras, deben crear una suscripción. En este escenario, esta suscripción proporciona a los proveedores delegados el derecho a presentar ofertas en nombre del proveedor principal.

Una vez establecida esta relación, el operador de Azure Stack puede delegar una oferta al proveedor delegado. El proveedor delegado ya puede coger la oferta, asignarle un nombre (pero sin cambiar el contenido) y ofrecerla a sus clientes.

En las siguientes secciones se describe cómo establecer un proveedor delegado, delegar una oferta y comprobar que los usuarios pueden subscribirse a la misma.

## <a name="set-up-roles"></a>Configurar los roles

Para ver un proveedor delegado trabajando, necesita cuentas adicionales de Azure Active Directory, además de su cuenta de operador de Azure Stack. Si no las tiene, debe crear las dos cuentas. Las cuentas pueden pertenecer a cualquier usuario de AAD. Nos referimos a ellos como el proveedor delegado y el usuario.

| **Rol** | **Derechos organizativos** |
| --- | --- |
| Proveedor delegado |Usuario |
| Usuario |Usuario |

## <a name="identify-the-delegated-providers"></a>Identificar los proveedores delegados
1. Inicie sesión como operador de Azure Stack.
2. Cree la oferta que permite que los usuarios se conviertan en proveedores delegados. Esto requiere la creación de un plan y una oferta basada en este:
   
   a.  [Cree un plan](azure-stack-create-plan.md).
       Este plan debe incluir solo el servicio de suscripciones. En este artículo, se usa un plan denominado PlanForDelegation.
   
   b.  [Cree una oferta](azure-stack-create-offer.md) en función de este plan. En este artículo, usamos una oferta denominada OfferToDP.
   
   c.  Una vez completada la creación de la oferta, agregue el proveedor delegado como un suscriptor a esta oferta haciendo clic en **Suscripciones** &gt; **Agregar** &gt; **Nueva suscripción de inquilino**.
   
   ![](media/azure-stack-delegated-provider/image3.png)

> [!NOTE]
> Como con todas las ofertas de Azure Stack, tiene la opción de hacer la oferta pública y permitir que los usuarios se subscriban; o mantenerla privada y dejar que el operador de Azure Stack administre la suscripción. Los proveedores delegados suelen ser un grupo pequeño y usted quiere controlar quién se admitió, por lo que mantener esta oferta en privado tiene sentido en la mayoría de los casos.
> 
> 

## <a name="azure-stack-operator-creates-the-delegated-offer"></a>El operador de Azure Stack crea la oferta delegada

Ya ha establecido su proveedor delegado. El siguiente paso es crear el plan y la oferta que va a delegar y que usarán los clientes. Debe definir esta oferta exactamente como desea que los clientes la vean, porque el proveedor delegado no podrá cambiar los planes ni las cuotas que incluye.

1. Como operador de Azure Stack, [cree un plan](azure-stack-create-plan.md) y [una oferta](azure-stack-create-offer.md) basada en él. En este artículo, usamos una oferta denominada DelegatedOffer.
   
   > [!NOTE]
   > Esta oferta no tiene que ser pública. Se pueda hacer pública si así lo elige, pero en la mayoría de los casos, solo querrá que los proveedores delegados tengan acceso a la misma. Una vez que delegue una oferta privada tal como se describe en los pasos siguientes, el proveedor delegado tendrá acceso a la misma.
   > 
   > 
1. Delegue la oferta. Vaya a DelegatedOffer y en el panel Configuración, haga clic en **Proveedores delegados** &gt; **Agregar**.
2. Seleccione la suscripción del proveedor delegado en el cuadro de lista desplegable y haga clic en **Delegado**.

> ![](media/azure-stack-delegated-provider/image4.png)
> 
> 

## <a name="delegated-provider-customizes-the-offer"></a>El proveedor delegado personaliza la oferta

Inicie sesión en el portal de usuarios como proveedor delegado y cree una oferta nueva usando la oferta delegada como plantilla.

1. Haga clic en **Nueva** &gt; **Ofertas + Planes de inquilinos** &gt; **Oferta**.

    ![](media/azure-stack-delegated-provider/image5.png)


1. Asigne un nombre a la oferta. Aquí elegiremos ResellerOffer. Seleccione la oferta delegada en la que basarse y, después, haga clic en **Crear**.
   
   ![](media/azure-stack-delegated-provider/image6.png)

    >[!NOTE] 
    > Tenga en cuenta cómo difiere la creación de la oferta tal y como la experimenta el operador de Azure Stack. El proveedor delegado no construye la oferta a partir de planes base ni complementarios; solo puede elegir entre ofertas que le han sido delegadas y no las puede modificar.

1. Para hacer la oferta pública, haga clic en **Examinar** &gt; **Ofertas**, seleccione la oferta y haga clic en **Cambiar estado**.
2. El proveedor delegado expone estas ofertas a través de la URL de su propio portal. Estas ofertas solo son visibles en el portal de delegado. Para buscar y cambiar esta dirección URL:
   
    a.  Haga clic en **Examinar** &gt; **Más servicios** &gt; **Suscripciones** &gt; Seleccione la suscripción del proveedor delegado, en nuestro caso es *DPSubscription* &gt; **Propiedades**.
   
    b.  Copie la URL del portal en una ubicación diferente, como el Bloc de notas.
   
    ![](media/azure-stack-delegated-provider/dpportaluri.png)  
   
   Ya ha completado la creación de una oferta delegada como un proveedor delegado. Cierre sesión como proveedor delegado. Cierre la pestaña de explorador que ha usado.

## <a name="sign-up-for-the-offer"></a>Suscribirse a la oferta
1. En una nueva ventana del explorador, vaya a la URL del portal de delegado que guardó en el paso anterior. Inicie sesión como usuario del portal. Nota: Use el portal de delegado para este paso, si no la oferta delegada no será visible.
2. En el panel, haga clic en **Obtener una suscripción**. Verá que solo se presentan las ofertas delegadas que creó el proveedor delegado al usuario:

> ![](media/azure-stack-delegated-provider/image8.png)
> 
> 

Esto concluye el proceso de delegación de la oferta. El usuario puede subscribirse a esta oferta obteniendo una suscripción para ella.

## <a name="multiple-tier-delegation"></a>Delegación de varios niveles

La delegación de varios niveles permite al proveedor delegado delegar la oferta a otras entidades. Esto permite, por ejemplo, la creación de canales de reventa más profundos, en los que el proveedor que administra Azure Stack delega una oferta a un distribuidor que, a su vez, la delega a un revendedor.
Azure Stack admite hasta cinco niveles de delegación.

Para crear varios niveles de delegación de ofertas, el proveedor delegado, a su vez, delega la oferta al siguiente proveedor. El proceso es el mismo para el proveedor delegado que para el operador de Azure Stack (consulte [El operador de Azure Stack crea la oferta delegada](#cloud-operator-creates-the-delegated-offer)).

## <a name="next-steps"></a>Pasos siguientes
[Aprovisionar una máquina virtual](azure-stack-provision-vm.md)

