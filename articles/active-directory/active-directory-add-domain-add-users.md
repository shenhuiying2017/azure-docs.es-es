---
title: Asignación de usuarios a un dominio personalizado en Azure Active Directory | Microsoft Docs
description: Cómo rellenar un dominio personalizado en Azure Active Directory con cuentas de usuario.
services: active-directory
documentationcenter: ''
author: jeffsta
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: curtand;jeffsta

---
# Asignación de usuarios a un dominio personalizado
Una vez que haya agregado el dominio personalizado a Azure Active Directory, debe agregar las cuentas de usuario de este dominio para que pueda comenzar a autenticarlas.

## Usuarios sincronizados desde un directorio de la red corporativa
Si ya estableció una conexión entre su instancia de Active Directory local y Azure Active Directory, puede rellenar las cuentas mediante la sincronización. Para más información sobre cómo sincronizar Azure Active Directory con su Active Directory local, consulte [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

## Usuarios que se agregan y administran en la nube
Para cambiar el dominio de una cuenta de usuario existente:

1. Abra el Portal de Azure clásico con una cuenta de administrador global o administrador de usuarios.
2. Abra el directorio.
3. Seleccione la pestaña **Usuarios**.
4. Seleccione el usuario de la lista.
5. Cambie el dominio del usuario y seleccione **Guardar**.

Esto también puede hacerse con [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) o [API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## Selección de un dominio personalizado al crear un nuevo usuario
1. Abra el Portal de Azure clásico con una cuenta de administrador global o administrador de usuarios.
2. Abra el directorio.
3. Seleccione la pestaña **Usuarios**.
4. En la barra de comandos, haga clic en **Agregar**.
5. Cuando agregue el nombre de usuario, elija el dominio personalizado de la lista de dominios.
6. Seleccione **Guardar**.

## Pasos siguientes
* [Using custom domain names to simplify the sign-in experience for your users (Uso de nombres de dominio personalizado para simplificar la experiencia de inicio de sesión para los usuarios)](active-directory-add-domain.md)
* [Managing custom domain names (Administración de nombres de dominio).](active-directory-add-manage-domain-names.md)
* [Información general conceptual de nombres de dominio personalizado en Azure Active Directory](active-directory-add-domain-concepts.md)

<!---HONumber=AcomDC_0720_2016-->