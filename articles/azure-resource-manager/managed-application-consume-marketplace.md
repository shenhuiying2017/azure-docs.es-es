---
title: Uso de aplicaciones administradas de Azure en Marketplace | Microsoft Docs
description: "En este artículo se describe la creación de una aplicación administrada de Azure que está disponible a través de Marketplace."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/11/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: baf456740bddd562391ed64d707f990c8921d710
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2017
---
# <a name="consume-azure-managed-applications-in-the-marketplace"></a>Uso de aplicaciones administradas de Azure en Marketplace

Tal y como se describe en el artículo [Información general de las aplicaciones administradas](managed-application-overview.md), hay dos escenarios en la experiencia completa. Uno es el publicador o proveedor que desea crear una aplicación administrada para que la utilicen los usuarios. El segundo es el usuario final de la aplicación administrada. Este artículo trata el segundo escenario. Describe cómo se puede implementar una aplicación administrada de Microsoft Azure Marketplace.

## <a name="create-from-the-marketplace"></a>Creación desde Marketplace

Implementar una aplicación administrada de Marketplace es similar a la implementación de cualquier tipo de recursos de Marketplace. 

En el portal, seleccione **+ Nuevo** y busque el tipo de solución para implementar. Entre las opciones disponibles, seleccione la que necesita.

![Búsqueda de soluciones](./media/managed-application-consume-marketplace/search-apps.png)

Revise el resumen de la aplicación y seleccione **Crear**.

![Creación de aplicación administrada](./media/managed-application-consume-marketplace/create-marketplace-managed-app.png)

Al igual que cualquier otra solución, se presentan diversos campos para los que debe proporcionar valores. Estos campos varían según el tipo de aplicación administrada que se cree. 

## <a name="view-support-information"></a>Ver la información de soporte técnico

Una vez implementada una aplicación administrada, puede ver la información de soporte técnico para la aplicación. En la hoja de la aplicación administrada, se muestra la información de soporte técnico.

![support](./media/managed-application-consume-marketplace/support.png)

## <a name="view-publisher-permissions"></a>Ver los permisos del publicador

Al proveedor que administra la aplicación se le concede acceso a sus recursos. Para ver los permisos, seleccione **Autorizaciones**.

![Autorizaciones](./media/managed-application-consume-marketplace/authorizations.png)

## <a name="next-steps"></a>Pasos siguientes

* Para información sobre cómo publicar aplicaciones administradas en Marketplace, consulte [Aplicaciones administradas de Azure en Marketplace](managed-application-author-marketplace.md).
* Para publicar aplicaciones administradas que solo están disponibles para los usuarios de su organización, consulte [Creación y publicación de una aplicación administrada del catálogo de servicios](managed-application-publishing.md).
* Para utilizar aplicaciones administradas que solo están disponibles para los usuarios de su organización, consulte [Utilización de una aplicación administrada del catálogo de servicios](managed-application-consumption.md).