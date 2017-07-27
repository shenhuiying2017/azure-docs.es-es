---
title: "Asignación de usuarios a un dominio personalizado en Azure Active Directory | Microsoft Docs"
description: "Cómo rellenar un dominio personalizado en Azure Active Directory con cuentas de usuario."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 717b5a7c-7bc3-4ab1-98b5-4740b53338fe
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ccfa8d72b51c43e04d924d2b096e3cbc407af043
ms.contentlocale: es-es
ms.lasthandoff: 12/28/2016

---
# <a name="assign-users-to-a-custom-domain"></a>Asignación de usuarios a un dominio personalizado
Una vez que haya agregado el dominio personalizado a Azure Active Directory, debe agregar las cuentas de usuario de este dominio para que pueda comenzar a autenticarlas.

## <a name="users-synced-in-from-a-directory-on-your-corporate-network"></a>Usuarios sincronizados desde un directorio de la red corporativa
Si ya estableció una conexión entre su instancia de Active Directory local y Azure Active Directory, puede rellenar las cuentas mediante la sincronización. Para más información sobre cómo sincronizar Azure Active Directory con su Active Directory local, consulte [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

## <a name="users-added-and-managed-in-the-cloud"></a>Usuarios que se agregan y administran en la nube
Para cambiar el dominio de una cuenta de usuario existente:

1. Abra el Portal de Azure clásico con una cuenta de administrador global o administrador de usuarios.
2. Abra el directorio.
3. Seleccione la pestaña **Usuarios** .
4. Seleccione el usuario de la lista.
5. Cambie el dominio del usuario y seleccione **Guardar**.

Esto también puede hacerse con [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) o la [API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>Selección de un dominio personalizado al crear un nuevo usuario
1. Abra el Portal de Azure clásico con una cuenta de administrador global o administrador de usuarios.
2. Abra el directorio.
3. Seleccione la pestaña **Usuarios** .
4. En la barra de comandos, haga clic en **Agregar**.
5. Cuando agregue el nombre de usuario, elija el dominio personalizado de la lista de dominios.
6. Seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes
* [Using custom domain names to simplify the sign-in experience for your users (Uso de nombres de dominio personalizado para simplificar la experiencia de inicio de sesión para los usuarios)](active-directory-add-domain.md)
* [Managing custom domain names (Administración de nombres de dominio).](active-directory-add-manage-domain-names.md)
* [Información general conceptual de nombres de dominio personalizado en Azure Active Directory](active-directory-add-domain-concepts.md)


