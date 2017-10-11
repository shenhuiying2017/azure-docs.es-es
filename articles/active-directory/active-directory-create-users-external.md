---
title: "Incorporación de usuarios de otros directorios o compañías asociadas en Azure Active Directory | Microsoft Docs"
description: "Explica cómo agregar usuarios o cambiar la información de usuarios en Azure Active Directory, incluidos los usuarios externos e invitados."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 564a04ec-53c1-470b-9ab9-f3db57da0a89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/25/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.openlocfilehash: 399230584d01986dd0f793a6ff8245ef2b4f8fb1
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="add-users-from-other-directories-or-partner-companies-in-azure-active-directory"></a>Incorporación de usuarios de otros directorios o compañías asociadas en Azure Active Directory

En este artículo se explica cómo agregar usuarios de otros directorios o de compañías asociadas en Azure Active Directory. Para más información sobre cómo agregar nuevos usuarios a su organización y cómo agregar usuarios con cuentas Microsoft, consulte el tema acerca de cómo [agregar o cambiar usuarios en Azure Active Directory](active-directory-create-users.md). 

> [!IMPORTANT]
> Microsoft recomienda administrar Azure AD con el [Centro de administración de Azure AD](https://aad.portal.azure.com) en Azure Portal en lugar de usar el portal de Azure clásico al que se hace referencia en este artículo. Para agregar usuarios invitados de colaboración B2B en el Centro de administración de Azure AD, consulte [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)

De forma predeterminada, los usuarios agregados no tienen permisos de administrador, pero puede asignárselos en cualquier momento.

## <a name="add-a-user"></a>Adición de un usuario
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com) con una cuenta que sea administrador global para el directorio.
2. Seleccione **Active Directory**y después abra el directorio.
3. Seleccione la pestaña **Usuarios** y, después, en la barra de comandos, seleccione **Agregar usuario**.
4. En la página **Proporcione información sobre este usuario**, elija una de estas opciones en **Tipo de usuario**:

   * **Usuario de otro directorio de Microsoft Azure AD** : agrega a su directorio una cuenta de usuario cuyo origen es otro directorio de Azure AD. Puede seleccionar un usuario de otro directorio solo si también es un miembro de ese directorio.
   * **Usuarios en compañías asociadas** : para invitar y autorizar a usuarios de compañías asociadas a su directorio (consulte el tema sobre [colaboración B2B de Azure Active Directory](active-directory-b2b-what-is-azure-ad-b2b.md)). Tendrá que [cargar un archivo CSV con las direcciones de correo electrónico](active-directory-b2b-references-csv-file-format.md).
5. En la página **Perfiles** del usuario, especifique el nombre, los apellidos y un nombre descriptivo, así como un rol de usuario en la lista **Roles**. Para obtener más información acerca de los roles del usuario y el administrador, consulte [Asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles.md). Especifique si se debe **habilitar Multi-Factor Authentication** para el usuario.
6. En la página **Obtener contraseña temporal**, seleccione **Crear**.

> [!IMPORTANT]
> Si su organización usa más de un dominio, debe tener en cuenta los siguientes problemas al agregar una cuenta de usuario:
>
> * Para agregar cuentas de usuario con el mismo nombre principal de usuario (UPN) en todos los dominios, agregue **primero**, por ejemplo, geoffgrisso@contoso.onmicrosoft.com, **seguido de** geoffgrisso@contoso.com.
> * **No** agregue geoffgrisso@contoso.com antes de geoffgrisso@contoso.onmicrosoft.com.
>

Si cambia la información de un usuario cuya identidad se sincroniza con el servicio Active Directory local, no puede cambiar la información del usuario en el Portal de Azure clásico. Para cambiar la información del usuario, utilice las herramientas de administración locales de Active Directory.

## <a name="add-external-users"></a>Adición de usuarios externos
También puede agregar usuarios de otro directorio de Azure AD al que pertenezca o desde las empresas asociadas mediante la carga de un archivo CSV. Para agregar un usuario externo, en **Tipo de usuario**, especifique **Usuario de otro directorio de Microsoft Azure AD** o **Usuarios en compañías asociadas**.

Los usuarios de ambos tipos proceden de otro directorio y se agregan como **usuarios externos**. Los usuarios externos pueden colaborar con otros usuarios en un directorio sin ningún requisito para agregar nuevas cuentas y credenciales. Los usuarios externos se autentican mediante su directorio principal cuando inician sesión, y esa autenticación funciona para todos los demás directorios a los que se han agregado.

## <a name="external-user-management-and-limitations"></a>Limitaciones y administración de usuarios externos
Cuando se agrega un usuario desde otro directorio al suyo, ese usuario es un usuario externo en el directorio. El nombre para mostrar y el nombre de usuario se copian desde su directorio principal y se usan para el usuario externo en el directorio. Desde ese momento, las propiedades de la cuenta de usuario externo son completamente independientes. Si se realizan cambios en las propiedades del usuario en su directorio particular, esos cambios no se propagan a la cuenta de usuario externa de su directorio.

La única vinculación entre las dos cuentas es que el usuario siempre se autentica en el directorio particular o con su cuenta de Microsoft. Ese es el motivo de que no vea una opción para restablecer la contraseña o habilitar la autenticación multifactor para un usuario externo. Actualmente, la directiva de autenticación del directorio particular o la cuenta de Microsoft es la única que se evalúa cuando el usuario inicia sesión.

> [!NOTE]
> Si lo desea, puede deshabilitar el usuario externo en el directorio, lo que bloqueará el acceso al directorio.
>
>

Si se elimina un usuario en su directorio principal o se cancela su cuenta de Microsoft, el usuario externo sigue existiendo en el directorio. Sin embargo, el usuario del directorio no puede tener acceso a los recursos porque no se puede autenticar con un directorio particular o una cuenta de Microsoft.

### <a name="services-that-currently-support-access-by-azure-ad-external-users"></a>Servicios que actualmente permiten el acceso a los usuarios externos de Azure AD
* **Portal de Azure clásico**: permite que un usuario externo que sea administrador de varios directorios administre cada uno de ellos.
* **SharePoint Online**: si el uso compartido externo está habilitado, permite que un usuario externo acceda a los recursos autorizados de SharePoint Online.
* **Dynamics CRM**: si el usuario tiene licencia mediante PowerShell, permite que un usuario externo acceda a los recursos autorizados en Dynamics CRM.
* **Dynamics AX**: si el usuario tiene licencia mediante PowerShell, permite que un usuario externo acceda a los recursos autorizados en Dynamics AX. Las limitaciones de los [usuarios externos de Azure AD](#known-limitations-of-azure-ad-external-users) se aplican también a los usuarios externos en Dynamics AX.

## <a name="next-steps"></a>Pasos siguientes
* [Adición de nuevos usuarios a Azure Active Directory](active-directory-create-users.md)
* [Administración de Azure AD](active-directory-administer.md)
* [Administración de contraseñas en Azure AD](active-directory-manage-passwords.md)
* [Administración de grupos en Azure AD](active-directory-manage-groups.md)
