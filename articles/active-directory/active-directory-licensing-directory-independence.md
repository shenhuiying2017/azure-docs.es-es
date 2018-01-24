---
title: "Características de la interacción de inquilinos de Azure Active Directory | Microsoft Docs"
description: "Administre sus inquilinos de Azure Active Directory considerándolos como recursos completamente independientes."
services: active-tenant
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 2b862b75-14df-45f2-a8ab-2a3ff1e2eb08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 2c00f382546a0e5f1e9da9dc90777dcf9622ec94
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Información de cómo interactúan varios inquilinos de Azure Active Directory

En Azure Active Directory (Azure AD), cada inquilino es un recurso totalmente independiente: un recurso del mismo nivel, que es lógicamente independiente de otros inquilinos que administre. No hay ninguna relación de elementos primarios y secundarios entre los inquilinos. Esta independencia entre inquilinos incluye la de los recursos, la independencia administrativa y la de sincronización.

## <a name="resource-independence"></a>Independencia de recursos
* Si crea o elimina un recurso en un inquilino, no tiene efecto en ningún recurso de los demás inquilinos, con la excepción parcial de los usuarios externos. 
* Si usa uno de los nombres de dominio con un inquilino, no se puede usar con ningún otro.

## <a name="administrative-independence"></a>Independencia administrativa
Si un usuario no administrador del inquilino "Contoso" crea un directorio de prueba "Prueba", en ese caso:

* De forma predeterminada, el usuario que crea un inquilino se agrega como un usuario externo en ese nuevo inquilino y se le asigna el rol de administrador global en ese inquilino.
* Los administradores del inquilino "Contoso" no tienen privilegios administrativos directos en el inquilino "Prueba", a menos que el administrador de "Prueba" se los otorgue específicamente. Sin embargo, los administradores de "Contoso" pueden controlar el acceso al inquilino "Prueba" si controlan la cuenta de usuario que creó "Prueba".
* Si se agrega o quita un rol de administrador para un usuario en un inquilino, el cambio no afecta a los roles de administrador que tenga el usuario en otro inquilino.

## <a name="synchronization-independence"></a>Independencia de sincronización
Puede configurar cada inquilino de Azure AD de manera independiente para que los datos se sincronicen desde una sola instancia de:

* La herramienta Azure AD Connect, para sincronizar los datos con un único bosque de AD.
* El conector de inquilinos de Azure Active Directory para Forefront Identity Manager, para sincronizar datos con uno o varios bosques locales u orígenes de datos distintos de Azure AD.

## <a name="add-an-azure-ad-tenant"></a>Adición de un inquilino de Azure AD
Para agregar un inquilino de Azure AD en Azure Portal, inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que sea un administrador global de Azure AD y, a la izquierda, seleccione **Nuevo**.

> [!NOTE]
> A diferencia de otros recursos de Azure, los inquilinos no son recursos secundarios de una suscripción a Azure. Si su suscripción a Azure se cancela o expira, aún podrá tener acceso a los datos de su inquilino mediante Azure PowerShell, Azure Graph API o el Centro de administración de Office 365. También puede [asociar otra suscripción al inquilino](active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>pasos siguientes
Para una amplia visión general de los problemas de licencias de Azure AD y los procedimientos recomendados, consulte [¿Qué es la licencia de inquilinos de Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
