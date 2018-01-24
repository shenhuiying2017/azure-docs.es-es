---
title: "Vinculación de una suscripción de Azure a Azure AD B2C | Microsoft Docs"
description: "Guía paso a paso para habilitar la facturación de un inquilino de Azure AD B2C en una suscripción de Azure."
services: active-directory-b2c
documentationcenter: dev-center-name
author: parakhj
manager: mtillman
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2017
ms.author: parja
ms.openlocfilehash: 063c00fe47be25b9359e80d71abfaf453c7a7074
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="linking-an-azure-subscription-to-an-azure-ad-b2c-tenant"></a>Vinculación de una suscripción de Azure a un inquilino de Azure AD B2C

> [!IMPORTANT]
> La información más reciente acerca de la facturación del uso y los precios de Azure AD B2C se encuentra en la siguiente página: [Precios de Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

Los cargos por el uso de Azure AD B2C se facturan a una suscripción de Azure. Cuando se crea un inquilino de Azure AD B2C, es preciso que el administrador de inquilinos vincule explícitamente dicho inquilino a una suscripción de Azure. Este artículo le muestra cómo.

> [!NOTE]
> Una suscripción vinculada a un inquilino de Azure AD B2C solo puede usarse para la facturación del uso de Azure AD B2C. La suscripción no se puede usar para agregar otros servicios de Azure o licencias de Office 365 *en el inquilino de Azure AD B2C*.

 El vínculo de la suscripción se consigue mediante la creación de un "recurso" de Azure AD B2C en la suscripción de Azure de destino. Muchos "recursos" de Azure AD B2C se pueden crear en una sola suscripción de Azure, junto con otros recursos de Azure (por ejemplo, máquinas virtuales, almacenamiento de datos y LogicApps). Para ver todos los recursos de la suscripción, vaya al inquilino de Azure AD al que está asociado la suscripción.

Para continuar, se necesita una suscripción de Azure válida.

## <a name="create-an-azure-ad-b2c-tenant"></a>Creación de un inquilino de Azure AD B2C

Primero debe [crear el inquilino de Azure AD B2C](active-directory-b2c-get-started.md) al que desea vincular una suscripción. Este paso se puede omitir si ya se ha creado un inquilino de Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Abra Azure Portal en el inquilino de Azure AD que muestre su suscripción de Azure

Vaya al inquilino de Azure AD que muestra su suscripción de Azure. Abra [Azure Portal](https://portal.azure.com) y cambie al inquilino de Azure AD que muestra la suscripción de Azure que desea usar.

![Cambio a un inquilino de Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Búsqueda de Azure AD B2C en Azure Marketplace

Haga clic en el botón **Nuevo**. En el campo **Buscar en el Marketplace**, escriba `B2C`.

![Agregue el resaltado y el texto Azure AD B2C en el campo de búsqueda en el Marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

En la lista de resultados, seleccione **Azure AD B2C**.

![Azure AD B2C seleccionado en la lista de resultados](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Se muestran los detalles de Azure AD B2C. Haga clic en el botón **Crear** para comenzar a configurar el nuevo inquilino de Azure Active Directory B2C.

En la pantalla de creación de recursos, seleccione **Vincular un inquilino de Azure AD B2C existente a mi suscripción de Azure**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Creación de un recurso de Azure AD B2C la suscripción de Azure

En el cuadro de diálogo de creación de recursos, seleccione un inquilino de Azure AD B2C de la lista desplegable. Verá todos los inquilinos de los que es administrador global y los que ya no están vinculados a una suscripción.

Se preseleccionará el nombre del recurso de Azure AD B2C para que coincida con el nombre de dominio del inquilino de Azure AD B2C.

En Suscripción, seleccione una suscripción activa de Azure de las que sea administrador.

Seleccione un grupo de recursos y una ubicación del grupo de recursos. Aquí la elección no tiene ningún efecto en la ubicación, el rendimiento o el estado de facturación del inquilino de Azure AD B2C.

![Crear recurso de B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Administración de los recursos del inquilino de Azure AD B2C

Una vez que el recurso de Azure AD B2C se haya creado correctamente en la suscripción de Azure, debería ver un nuevo recurso del tipo "inquilino de B2C" agregado junto con los otros recursos de Azure.

Este recurso se puede utilizar para:

- Ir a la suscripción para ver la información de facturación.
- Ir al inquilino de Azure AD B2C.
- Enviar una solicitud de soporte técnico.
- Mover el recurso del inquilino de Azure AD B2C a otra suscripción de Azure o a otro grupo de recursos.

![Configuración de recursos de B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Problemas conocidos

### <a name="csp-subscriptions"></a>Suscripciones de CSP

Actualmente, un inquilino de Azure AD B2C **no se puede** vincular a las suscripciones de CSP.

### <a name="self-imposed-restrictions"></a>Restricciones autoimpuestas

Un usuario puede haber establecido una restricción regional para la creación de recursos de Azure. Esta restricción puede impedir la creación de un recurso de Azure AD B2C. Para mitigar esto, la restricción debe ser menos exigente.

## <a name="next-steps"></a>pasos siguientes

Una vez que se han completados estos pasos en todos los inquilinos de Azure AD B2C, la suscripción de Azure se factura según los detalles del Contrato Enterprise o de Azure Direct.

Los detalles de uso y facturación se pueden consultar en la suscripción de Azure seleccionada. También puede consultar los informes detallados del uso diario mediante la [API de Informes de uso](active-directory-b2c-reference-usage-reporting-api.md).
