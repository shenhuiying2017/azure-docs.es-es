---
title: "Requisitos previos no técnicos para crear una oferta en Azure Marketplace | Microsoft Docs"
description: Conozca los requisitos para crear e implementar una oferta en Azure Marketplace para que otros usuarios la compren.
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3dae463b-8f48-4f52-8fa8-4e3975f09f43
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/18/2016
ms.author: hascipio
ms.openlocfilehash: 4f86d444a2f2b97fd8605d480db358813bc39fd3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Requisitos previos generales para crear una oferta para Azure Marketplace
Conozca los requisitos previos generales, centrados en procesos de negocio, que se necesitan para avanzar con el proceso de la creación de ofertas.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Asegúrese de que está registrado como vendedor con Microsoft
Para instrucciones detalladas sobre cómo registrar una cuenta de vendedor en Microsoft, vaya a [Creación y registro de cuentas](marketplace-publishing-accounts-creation-registration.md).

* **Si su compañía ya está registrada como vendedor en el Centro de desarrollo y quiere crear una nueva oferta,** inicie sesión en el Portal de publicación con el mismo identificador de correo electrónico con el que se realizó el registro en el Centro de desarrollo. Este paso es necesario para que el Portal de publicación y el Centro de desarrollo están vinculados entre sí.
* **Si su compañía ya está registrada como vendedor en el Centro de desarrollo y quiere editar una oferta existente,** inicie sesión en el Portal de publicación con la cuenta de administrador o con una que se agregue como cuenta de coadministrador en el Portal de publicación. A continuación, se muestran los pasos para agregar una cuenta de coadministrador.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>Pasos para agregar coadministradores en el Portal de publicación
Los administradores del Portal de publicación podrán agregar como coadministradores al resto de los miembros de la compañía que estén utilizando la aplicación. **Si damos por hecho que usted es el administrador** , siga estos pasos para agregar un coadministrador.

> [!NOTE]
> Para los nuevos usuarios, antes de agregar un coadministrador en el Portal de publicación, asegúrese de que ha creado, al menos, una aplicación en el Portal de publicación. Esto es necesario porque la pestaña **PUBLICADORES** solo se muestra después de crear, como mínimo, una aplicación en el Portal de publicación.
> 
> 

1. Asegúrese de que el identificador de correo electrónico de coadministrador sea una cuenta Microsoft (MSA). Si no es así, regístrela como MSA haciendo clic en este [vínculo](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Asegúrese de que hay, al menos, una aplicación en la cuenta de administrador antes de intentar agregar un coadministrador.
3. Cuando realice los pasos anteriores, inicie sesión en el Portal de publicación con el identificador de correo electrónico de coadministrador y, luego, cierre de sesión.
4. Ahora, inicie sesión en el Portal de publicación con el identificador de correo electrónico de administrador.
5. Vaya a Publicadores -> seleccione la cuenta -> Administradores -> Agregar nuevo coadministrador (consulte la captura de pantalla siguiente).
   
    ![dibujo](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. Asegúrese de supervisar los identificadores de correo electrónico proporcionados en las distintas fases del proceso de publicación (por ejemplo, el Centro de desarrollo, el Portal de publicación) en las comunicaciones de Microsoft.
7. En cuanto al registro del Centro de desarrollo, evite usar una cuenta asociada a una sola persona. Esto se recomienda para tener que dejar de depender de una persona.
8. Si experimenta problemas con el registro en el Centro de desarrollo, cree una incidencia haciendo clic en este [vínculo](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>Pasos para eliminar a un coadministrador en el portal de publicación
**Dando por hecho que usted es el administrador** , siga estos pasos para eliminar a un coadministrador.

1. Inicie sesión en el portal de publicación con el identificador de correo electrónico de administrador.
2. Vaya a **Publicadores** -> seleccione su cuenta -> **Administradores** -> **Co-Admins** (Coadministradores).
3. Haga clic en el botón **X** situado junto al coadministrador que desee eliminar (consulte la captura de pantalla siguiente).
   
    ![dibujo](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> No tiene que completar la información bancaria y fiscal de la compañía si solo va a publicar ofertas gratuitas (o de tipo traiga su propia licencia).
> 
> Debe completar el registro de la empresa para comenzar. Sin embargo, aunque la compañía trabaja en la información bancaria y fiscal de la cuenta de desarrollador de Microsoft, los desarrolladores pueden comenzar a trabajar en la creación de la imagen de máquina virtual en el [portal de publicación](https://publish.windowsazure.com), y luego certificarla y probarla en el entorno de ensayo de Azure. Solo necesitará la aprobación de la cuenta de vendedor completa en el paso final de la publicación de la oferta en Azure Marketplace.
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Adquisición de una suscripción de Azure de "pago por uso"
Se trata de la suscripción que usará para crear las imágenes de máquina virtual y entregarlas a [Azure Marketplace](https://azure.microsoft.com/marketplace/). Si no tiene una suscripción, suscríbase en https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>Países de "Venta-de"
> [!WARNING]
> Para vender sus servicios en Azure Marketplace, es necesario que se asegure de que la entidad registrada procede de uno de los países desde donde está permitida la venta. Esta restricción se debe a motivos de pago e impuestos. Trabajamos activamente para ampliar esta lista de países en un futuro próximo, así pues, manténgase atento. Para la lista completa, vea la sección 1b de la página [Directivas de participación de Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Una vez cumplidos los requisitos previos no técnicos, nos encontramos los requisitos previos técnicos específicos de la oferta. Haga clic en el vínculo al artículo correspondiente al tipo de oferta que le gustaría crear en Azure Marketplace.

* [Requisitos previos técnicos de la máquina virtual](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Requisitos previos técnicos de la plantilla de solución](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Otras referencias
* [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

