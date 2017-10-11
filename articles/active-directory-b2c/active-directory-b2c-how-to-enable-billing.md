---
title: "Vinculación de una suscripción de Azure a Azure AD B2C | Microsoft Docs"
description: "Guía paso a paso para habilitar la facturación de un inquilino de Azure AD B2C en una suscripción de Azure."
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: joroja
ms.openlocfilehash: 5b9955b2af7f20a79981315fa33a0eb5380a5465
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="linking-an-azure-subscription-to-an-azure-b2c-tenant-to-pay-for-usage-charges"></a>Vinculación de una suscripción de Azure a un inquilino de Azure B2C para pagar los cargos por uso

Los cargos por el uso continuo de Azure Active Directory B2C (o Azure AD B2C) se facturan a una suscripción de Azure. Es necesario que el Administrador de inquilinos vincule explícitamente el inquilino de Azure AD B2C a una suscripción de Azure después de crear el propio inquilino de B2C.  Este vínculo se consigue mediante la creación de un recurso de inquilino de Azure AD B2C en la suscripción de destino de Azure. Muchos inquilinos de B2C pueden vincularse a una sola suscripción de Azure, junto con otros recursos de Azure (por ejemplo, máquinas virtuales, almacenamiento de datos y LogicApps)


> [!IMPORTANT]
> La información más reciente sobre la facturación del uso y precios de B2C se encuentra en la siguiente página: [Azure Active Directory B2C Precios](
https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="step-1---create-an-azure-ad-b2c-tenant"></a>Paso 1: Crear un inquilino de Azure AD B2C
Primero se debe completar la creación de un inquilino de B2C. Este paso se puede omitir si ya se ha creado un inquilino de B2C de destino. [Introducción a Azure AD B2C](active-directory-b2c-get-started.md)

## <a name="step-2---open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Paso 2: Abrir Azure Portal en el inquilino de Azure AD que muestra su suscripción de Azure
Acceda a [Azure Portal](https://portal.azure.com). Cambie al inquilino de Azure AD que muestra la suscripción de Azure que desea usar. Este inquilino de Azure AD no es el inquilino de B2C. En Azure Portal, haga clic en el nombre de cuenta de la esquina superior derecha del panel para seleccionar al inquilino de Azure AD. Para continuar, se necesita una suscripción de Azure. [Obtención de una suscripción de Azure](https://account.windowsazure.com/signup?showCatalog=True)

![Cambio a un inquilino de Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="step-3---create-a-b2c-tenant-resource-in-azure-marketplace"></a>Paso 3: Crear un recurso B2C Tenant en Azure Marketplace
Abra Marketplace, para lo que debe hacer clic en el icono de Marketplace, o bien seleccionar el signo "+" verde de en la esquina superior izquierda del panel.  Busque y seleccione Azure Active Directory B2C. Seleccione Crear.

![Seleccione Marketplace](./media/active-directory-b2c-how-to-enable-billing/marketplace.png)

![Buscar en AD B2C](./media/active-directory-b2c-how-to-enable-billing/searchb2c.png)

El cuadro de diálogo de creación de recursos de Azure AD B2C cubre los siguientes parámetros:

1. Inquilino de Azure AD B2C: seleccione un inquilino de Azure AD B2C en la lista desplegable.  Solo se muestran los inquilinos de Azure AD B2C que se pueden elegir.  Los inquilinos B2C aptos cumplen estas condiciones: usted es el administrador global del inquilino de B2C y este no está asociado actualmente a ninguna suscripción de Azure

2. Nombre del recurso de Azure AD B2C: está preseleccionado para que coincida con el nombre de dominio del inquilino de B2C

3. Suscripción: suscripción activa de Azure en la que es administrador o coadministrador.  Se pueden agregar varios inquilinos de Azure AD B2C a una suscripción de Azure

4. Ubicación de grupo de recursos y grupo de recursos: este artefacto le ayuda a organizar varios recursos de Azure.  Esta opción no tiene ningún efecto en la ubicación, el rendimiento o el estado de facturación del inquilino de B2C

5. Con Anclar al panel se facilita el acceso a la información de facturación del inquilino de B2C y a la configuración de inquilino de B2C ![Crear recurso de B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="step-4---manage-your-b2c-tenant-resources-optional"></a>Paso 4: Administrar los recursos de inquilino de B2C (opcional)
Una vez completada la implementación, se crea un nuevo recurso de inquilino de B2C en el grupo de recursos de destino y en la suscripción de Azure relacionada.  Debería ver que se ha agregado un nuevo recurso del tipo "Inquilino de B2C" a los restantes recursos de Azure.

![Crear recurso de B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcedashboard.png)

Al hacer clic en el recurso del inquilino de B2C, es posible
- Hacer clic en el nombre de la suscripción para ver la información de facturación. Consulte Facturación y uso.
- Hacer clic en Configuración de Azure AD B2C para abrir la hoja de configuración del inquilino directamente en una nueva pestaña del explorador.
- Enviar una solicitud de soporte técnico.
- Mover el recurso de inquilino de B2C a otra suscripción de Azure o a otro grupo de recursos.  Esta opción cambia la suscripción de Azure que recibe los gastos por uso.

![Configuración de recursos de B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Problemas conocidos
- Eliminación del inquilino de B2C. Si se crea un inquilino de B2C, lo elimina y lo vuelve a crear con el mismo nombre de dominio, elimine también el recurso "Linking" (Vinculación) y vuélvalo a crear con el mismo nombre de dominio.  Encontrará este recurso "Linking" (Vinculación) en "All resources" (Todos los recursos) en el inquilino de suscripción a través de Azure Portal.
- Restricciones autoimpuestas en la ubicación del recurso regional.  En raras ocasiones, un usuario puede haber establecido una restricción regional para la creación de recursos de Azure.  Esta restricción podría impedir la creación del vínculo entre una suscripción de Azure y un inquilino de B2C. Para mitigar esto, la restricción debe ser menos exigente.

## <a name="next-steps"></a>Pasos siguientes
Una vez que se han completados estos pasos en cada uno de los inquilinos B2C, la suscripción de Azure se factura según los detalles del Contrato Enterprise o directo de Azure.
- Revisión del uso y facturación en la suscripción de Azure seleccionada
- Revisión de los informes detallados de [uso diario mediante la API de Informes de uso](active-directory-b2c-reference-usage-reporting-api.md)
