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
ms.openlocfilehash: 1a1f2789076b610ee557bde5981a331c55cc1c95
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="delegate-offers-in-azure-stack"></a>Delegación de ofertas en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Como operador de Azure Stack, es posible que a veces quiera delegar en otras personas las tareas de creación de ofertas y registro de usuarios. Por ejemplo, si es un proveedor de servicios puede desear que los distribuidores registren a los clientes y los administren en su nombre. Si forma parte de un grupo central de TI en una empresa, puede que quiera que las subsidiarias registren usuarios sin su intervención.

La delegación le ayuda con estas tareas permitiéndole alcanzar y administrar a más usuarios que si lo hiciera directamente. En la siguiente ilustración se muestra un nivel de delegación, pero Azure Stack admite varios niveles. Los proveedores delegados pueden a su vez delegar las tareas a otros proveedores, con un total de hasta cinco niveles.

![Niveles de delegación](media/azure-stack-delegated-provider/image1.png)

Los operadores de Azure Stack pueden delegar la creación de ofertas y usuarios a otros usuarios mediante la funcionalidad de delegación.

## <a name="roles-and-steps-in-delegation"></a>Roles y pasos de la delegación
Para comprender la delegación, es necesario tener en cuenta que existen tres roles implicados:

* El *operador de Azure Stack* administra la infraestructura de Azure Stack, crea una plantilla de oferta y delega en otros para que la ofrezcan a sus usuarios.

* Los operadores de Azure Stack se denominan *proveedores delegados*. Pueden pertenecer a otras organizaciones, como otros usuarios de Azure Active Directory (Azure AD).

* Los *usuarios* se suscriben a las ofertas y las usan para administrar las cargas de trabajo, crear máquinas virtuales, almacenar datos, etcétera.

Tal como se muestra en el siguiente gráfico, hay dos pasos para configurar la delegación.

1. *Identificar a los proveedores delegados*. Esto se hace suscribiéndolos a una oferta basada en un plan que contiene solo el servicio de suscripciones. Los usuarios que se suscriben a esta oferta adquieren algunas de las capacidades del operador de Azure Stack, incluida la capacidad para ampliar las ofertas y registrar a los usuarios para que puedan disfrutarlas.

2. *Delegar una oferta en el proveedor delegado*. Esta oferta funciona como una plantilla para lo que puede ofrecer el proveedor delegado. El proveedor delegado puede aprovechar ahora la oferta. Elija un nombre para ella (pero no cambie sus servicios y cuotas) y ofrézcala a los clientes.

![Crear el proveedor delegado y habilitarlo para que puedan suscribir a los usuarios](media/azure-stack-delegated-provider/image2.png)

Para actuar como proveedor delegado, los usuarios tienen que establecer una relación con el proveedor principal. En otras palabras, tienen que crear una suscripción. En este escenario, esta suscripción proporciona a los proveedores delegados el derecho a presentar ofertas en nombre del proveedor principal.

Una vez establecida esta relación, el operador de Azure Stack puede delegar una oferta en el proveedor delegado. El proveedor delegado ya puede coger la oferta, asignarle un nombre (pero sin cambiar el contenido) y ofrecerla a sus clientes.

En las siguientes secciones se describe cómo establecer un proveedor delegado, delegar una oferta y comprobar que los usuarios pueden subscribirse a la misma.

## <a name="set-up-roles"></a>Configurar los roles

Para ver un proveedor delegado trabajando, necesita cuentas adicionales de Azure Active Directory, además de su cuenta de operador de Azure Stack. Si no las tiene, debe crear las dos cuentas. Las cuentas pueden pertenecer a cualquier usuario de Azure Active Directory. Nos referimos a ellos como el proveedor delegado y el usuario.

| **Rol** | **Derechos organizativos** |
| --- | --- |
| Proveedor delegado |Usuario |
| Usuario |Usuario |

## <a name="identify-the-delegated-providers"></a>Identificar los proveedores delegados
1. Inicie sesión como operador de Azure Stack.

2. Cree la oferta que permite que los usuarios se conviertan en proveedores delegados:
   
   a.  [Cree un plan](azure-stack-create-plan.md).
       Este plan debe incluir solo el servicio de suscripciones. En este artículo, se usa un plan denominado **PlanForDelegation**.
   
   b.  [Cree una oferta](azure-stack-create-offer.md) en función de este plan. En este artículo, usamos una oferta denominada **OfferToDP**.
   
   c.  Una vez completada la creación de la oferta, agregue el proveedor delegado como un suscriptor a esta oferta. Para ello seleccione **Suscripciones**  > **Agregar** > **Nueva suscripción de inquilino**.
   
   ![Agregue el proveedor delegado como un suscriptor](media/azure-stack-delegated-provider/image3.png)

> [!NOTE]
> Como con todas las ofertas de Azure Stack, tiene la opción de hacer la oferta pública y permitir que los usuarios se subscriban, o mantenerla privada y dejar que el operador de Azure Stack administre la suscripción. Los proveedores delegados suelen ser un grupo pequeño. Con el fin de controlar a quién se admite en este grupo, mantener esta oferta en privado es lo más lógico en la mayoría de los casos.
> 
> 

## <a name="azure-stack-operator-creates-the-delegated-offer"></a>El operador de Azure Stack crea la oferta delegada

Ya ha establecido su proveedor delegado. El siguiente paso es crear el plan y la oferta que va a delegar y que usarán los clientes. Es conveniente definir esta oferta exactamente como desea que los clientes la vean, porque el proveedor delegado no podrá cambiar los planes ni las cuotas que incluye.

1. Como operador de Azure Stack, [cree un plan](azure-stack-create-plan.md) y [una oferta](azure-stack-create-offer.md) basada en él. En este artículo, usamos una oferta denominada **DelegatedOffer.**
   
   > [!NOTE]
   > Esta oferta no tiene que ser pública. Si así lo desea, puede hacerla pública. En la mayoría de los casos, solo deseará que los proveedores delegados tengan acceso a ella. Una vez que delegue una oferta privada tal como se describe en los pasos siguientes, el proveedor delegado tendrá acceso a la misma.
   > 
   > 
1. Delegue la oferta. Vaya a **DelegatedOffer.** A continuación, en el panel **Configuración** seleccione **Proveedores delegados** > **Agregar**.

2. Seleccione la suscripción del proveedor delegado en el cuadro de lista desplegable y haga clic en **Delegado**.

> ![Agregar proveedor delegado](media/azure-stack-delegated-provider/image4.png)
> 
> 

## <a name="delegated-provider-customizes-the-offer"></a>El proveedor delegado personaliza la oferta

Inicie sesión en el portal de usuarios como el proveedor delegado. A continuación, cree una nueva oferta usando la oferta delegada como una plantilla.

1. Seleccione **Nueva** > **Ofertas + Planes de inquilinos** > **Oferta**.

    ![Crear una nueva oferta](media/azure-stack-delegated-provider/image5.png)


1. Asigne un nombre a la oferta. Aquí elegiremos **ResellerOffer**. Seleccione la oferta delegada en la que basarse y, después seleccione **Crear**.
   
   ![Asignar un nombre](media/azure-stack-delegated-provider/image6.png)

    >[!NOTE] 
    > Tenga en cuenta cómo difiere la creación de la oferta tal y como la experimenta el operador de Azure Stack. El proveedor delegado no construye la oferta a partir de planes de base y planes complementarios. Solo puede elegir entre ofertas que se han delegado en él y no puede realizar cambios en esas ofertas.

1. Haga la oferta pública seleccionando **Examinar**y, a continuación, **Ofertas**. Seleccione la oferta y, a continuación, seleccione **Cambiar estado**.

2. El proveedor delegado expone estas ofertas a través de la URL de su propio portal. Estas ofertas solo son visibles en el portal de delegado. Para buscar y cambiar esta dirección URL:
   
    a.  Seleccione **Examinar** > **Más servicios** >  **Suscripciones**. A continuación, seleccione la suscripción del proveedor delegado. En nuestro caso, es **DPSubscription** > **Propiedades**.
   
    b.  Copie la URL del portal en una ubicación diferente, como el Bloc de notas.
   
    ![Seleccione la suscripción del proveedor delegado](media/azure-stack-delegated-provider/dpportaluri.png)  
   
   Ha creado una oferta delegada como proveedor delegado. Cierre sesión como proveedor delegado. Cierre la ventana de explorador que ha usado.

## <a name="sign-up-for-the-offer"></a>Suscribirse a la oferta
1. En una nueva ventana del explorador, vaya a la URL del portal de delegado que guardó en el paso anterior. Inicie sesión en el portal como usuario. 
   
   >[!NOTE]
   > Use el portal de delegado para este paso. Si no las ofertas delegadas no serán visibles.

2. En el panel, haga clic en **Obtener una suscripción**. Verá que solo se presentan al usuario las ofertas delegadas que creó el proveedor delegado:

> ![Ver y seleccionar ofertas](media/azure-stack-delegated-provider/image8.png)
> 
> 

El proceso de delegación de oferta se ha completado. El usuario puede subscribirse a esta oferta obteniendo una suscripción para ella.

## <a name="multiple-tier-delegation"></a>Delegación de varios niveles

La delegación de varios niveles permite al proveedor delegado delegar la oferta en otras entidades. Esto permite, por ejemplo, la creación de canales de reventa más profundos, en los que el proveedor que administra Azure Stack delega una oferta en un distribuidor. Dicho distribuidor, a su vez, la delega en un revendedor. Azure Stack admite hasta cinco niveles de delegación.

Para crear varios niveles de delegación de ofertas, el proveedor delegado, a su vez, delega la oferta al siguiente proveedor. El proceso es el mismo para el proveedor delegado que para el operador de Azure Stack (consulte [El operador de Azure Stack crea la oferta delegada](#cloud-operator-creates-the-delegated-offer)).

## <a name="next-steps"></a>Pasos siguientes
[Aprovisionar una máquina virtual](azure-stack-provision-vm.md)

