---
title: Requisitos previos de Azure Data Catalog | Microsoft Docs
description: "Más información sobre los requisitos previos necesarios para empezar a usar Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: a7effaaaeb23661b9be96dcddc2c140ab8c8b92b
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="azure-data-catalog-prerequisites"></a>Requisitos previos de Azure Data Catalog

Hay algunas cosas de las que debe encargarse para poder configurar Azure Data Catalog. No se preocupe, este proceso lleva poco tiempo.

## <a name="azure-subscription"></a>Suscripción de Azure
Para configurar Data Catalog, debe ser propietario o copropietario de una suscripción de Azure.

Las suscripciones de Azure le ayudan a organizar el acceso a los recursos de servicio en la nube como Data Catalog. También le ayudan a controlar cómo se informa, factura y paga el uso de recursos. Cada suscripción puede tener una configuración de facturación y pago independiente, por lo que puede tener suscripciones y planes que varían según el departamento, proyecto, oficina regional, etc. Cada servicio en la nube pertenece a una suscripción, y debe tener una suscripción para poder configurar Data Catalog. Para más información, consulte [Administración de cuentas, suscripciones y roles administrativos](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Para configurar Data Catalog, debe haber iniciado sesión con una cuenta de usuario de Azure Active Directory (Azure AD).

Azure AD proporciona una manera fácil de que cualquier empresa pueda administrar la identidad y el acceso, tanto en la nube como de forma local. Los usuarios pueden usar una única cuenta profesional o educativa para efectuar el inicio de sesión único en cualquier aplicación web en la nube y local. Data Catalog usa Azure AD para autenticar el inicio de sesión. Para más información, consulte [¿Qué es Azure Active Directory?](../active-directory/active-directory-whatis.md)

> [!NOTE]
> [Azure Portal](http://portal.azure.com/) le permite iniciar sesión con una cuenta personal de Microsoft o una cuenta profesional o educativa de Azure Active Directory. Para configurar Data Catalog mediante Azure Portal o mediante el [portal de Data Catalog](http://www.azuredatacatalog.com) debe iniciar sesión con una cuenta de Azure Active Directory, no con una cuenta personal.
>
>

## <a name="active-directory-policy-configuration"></a>Configuración de directivas de Active Directory
Se puede dar el caso de que pueda iniciar sesión en el portal de Data Catalog, pero al intentar iniciar sesión en la herramienta de registro de orígenes de datos, se mostrará un mensaje de error que le impedirá hacerlo. El problema de este comportamiento se puede producir solo cuando esté en la red de la empresa o solo cuando se conecte desde fuera de la red de empresa.

La herramienta de registro de orígenes de datos usa la autenticación de formularios para validar las credenciales de usuario en Active Directory. Para que el inicio de sesión sea correcto, un administrador de Active Directory tiene que habilitar la autenticación de formularios en la directiva de autenticación global.

Con la directiva de autenticación global se puede habilitar los métodos de autenticación de forma independiente para las conexiones de extranet y de intranet, como se muestra en la siguiente captura de pantalla. Pueden producirse errores de inicio de sesión si no está habilitada la autenticación de formularios en la red desde la que se conecta.

 ![Directiva de autenticación global de Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>pasos siguientes
Para obtener más información, vea [Configuración de directivas de autenticación](https://technet.microsoft.com/library/dn486781.aspx).
