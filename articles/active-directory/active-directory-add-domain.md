---
title: "Incorporación de su nombre de dominio personalizado a Azure Active Directory | Microsoft Docs"
description: "Cómo agregar nombres de dominio de la empresa a Azure Active Directory y comprobar el nombre de dominio."
services: active-directory
documentationcenter: 
author: jeffsta
manager: femila
editor: 
ms.assetid: 35a6e20a-9907-432b-9d36-16b916a5c249
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/30/2016
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7bd889847f07ebbadc5e427f2b1ff3f9f869e69a


---
# <a name="add-a-custom-domain-name-to-azure-active-directory"></a>Incorporación de su nombre de dominio personalizado a Azure Active Directory
> [!div class="op_single_selector"]
> * [Portal de Azure](active-directory-domains-add-azure-portal.md)
> * [Portal de Azure clásico](active-directory-add-domain.md)
> 
> 

Dispone de uno o varios nombres de dominio que su organización usa para su actividad empresarial, y los usuarios inician sesión en la red corporativa mediante su nombre de dominio corporativo. Ahora que usa Azure Active Directory (Azure AD), también puede agregar el nombre de dominio corporativo a Azure AD. Esto permite asignar en el directorio nombres de usuario que resultan familiares a los usuarios, como ‘alice@contoso.com.’ El proceso es sencillo:

1. Incorporación de nombres de dominio personalizados al directorio
2. Incorporación de la entrada DNS para el nombre de dominio en el registrador de nombres de dominio
3. Comprobación del nombre de dominio personalizado en Azure AD

> [!NOTE]
> Si planea configurar el nombre de dominio personalizado para su uso con Servicios de federación de Active Directory (AD FS) o un servicio de token de seguridad (STS) diferente en su red corporativa, siga las instrucciones de [Add your custom domain name to Azure Active Directory](active-directory-add-domain-federated.md)(Incorporación y configuración de un dominio para federación con Azure Active Directory). Este procedimiento es útil si piensa sincronizar usuarios desde su directorio corporativo a Azure AD y la [sincronización de hash de contraseña](active-directory-aadconnectsync-implement-password-synchronization.md) no cumple sus requisitos.
> 
> 

## <a name="add-a-custom-domain-name-to-your-directory"></a>Incorporación de nombres de dominio personalizados al directorio
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con una cuenta de usuario que sea administrador global de su directorio de Azure AD.
2. En **Active Directory**, abra el directorio y seleccione la pestaña **Dominios**.
3. En la barra de comandos, haga clic en **Agregar**. Escriba el nombre del dominio personalizado como, por ejemplo, ‘contoso.com’. No se olvide de incluir la extensión .com, .net u otra de nivel superior y deje la casilla para "inicio de sesión único" (federación) desactivada.
4. Seleccione **Agregar**.
5. En la segunda página del asistente para agregar dominios, obtenga la entrada DNS que utilizará Azure AD para comprobar que su organización posee el nombre de dominio personalizado.

Ahora que ha agregado el nombre de dominio, Azure AD debe comprobar que su organización posea el nombre de dominio. Para que Azure AD pueda realizar esta comprobación, tiene que agregar una entrada DNS en el archivo de zona DNS para el nombre de dominio. Esta tarea se lleva a cabo en el sitio web del registrador de nombres de dominio para el nombre de dominio.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Incorporación de la entrada DNS en el registrador de nombres de dominio para el dominio
El siguiente paso para usar el nombre de dominio personalizado con Azure AD consiste en actualizar el archivo de zona DNS para el dominio. Esto permite a Azure AD comprobar que su organización posee el nombre de dominio personalizado.

1. Inicie sesión en el registrador de nombres de dominio para el dominio. Si no tiene acceso para actualizar la entrada DNS, pida a la persona o equipo que tenga acceso que complete el paso 2 y que le informe cuando esté completo.
2. Actualice el archivo de zona DNS para el dominio. Para ello, agregue la entrada DNS que le proporcione Azure AD. Esta entrada DNS permite a Azure AD comprobar la propiedad del dominio. No cambiará ningún comportamiento, como el enrutamiento de correo o el hospedaje web.

Para obtener ayuda con la incorporación de la entrada DNS, lea [Instrucciones para agregar entradas DNS en los registradores DNS más habituales](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Comprobación del nombre de dominio con Azure AD
Una vez agregada la entrada DNS, ya puede comprobar el nombre de dominio con Azure AD.

Si sigue teniendo el asistente para **agregar dominio** abierto, seleccione **Comprobar** en la tercera página del asistente. Cuando selecciona **Comprobar**, Azure AD buscará la entrada DNS en el archivo de zona DNS para el dominio. Azure AD puede comprobar el nombre de dominio solo después de que se hayan propagado los registros de DNS. Esta propagación a suele tardar unos segundos, pero a veces puede tardar una hora o incluso más. Si la comprobación no funciona la primera vez, inténtelo de nuevo más tarde.

Si el asistente para **agregar dominio** no sigue abierto, puede comprobar el dominio en el [Portal de Azure clásico](https://manage.windowsazure.com/):

1. Inicie sesión con una cuenta de usuario que sea un administrador global de su directorio de Azure AD.
2. Abra el directorio y seleccione la pestaña **Dominios** .
3. Seleccione el nombre de dominio que desea comprobar y seleccione **Comprobar** en la barra de comandos.
4. Seleccione **Comprobar** en el cuadro de diálogo para completar la comprobación.

Ya puede [asignar nombres de usuario a un dominio personalizado](active-directory-add-domain-add-users.md).

## <a name="troubleshooting"></a>Solución de problemas
Si no puede comprobar un nombre de dominio personalizado, intente lo siguiente. Comenzaremos con las más comunes hasta llegar a las menos frecuentes.

1. **Espere una hora**. Los registros de DNS deben propagarse antes de que Azure AD pueda comprobar el dominio. Esto puede tardar una hora o más.
2. **Asegúrese de que se especificó el registro de DNS y que es correcto**. Complete este paso en el sitio web del registrador de nombres de dominio para el dominio. Azure AD no puede comprobar el nombre de dominio si la entrada DNS no está presente en el archivo de zona DNS, o si no es una coincidencia exacta con la entrada DNS que Azure AD le ha proporcionado. Si no tiene acceso para actualizar los registros de DNS para el dominio en el nombre de dominio, comparta la entrada DNS con la persona o el equipo de su organización que cuente con este acceso y solicítele que la agregue.
3. **Elimine el nombre de dominio de otro directorio en Azure AD**. Se puede comprobar el nombre de dominio en solo un único directorio. Si previamente se ha comprobado un nombre de dominio en otro directorio, se debe eliminar allí antes de que se puede comprobar en el nuevo directorio. Para más información sobre cómo eliminar los nombres de dominio, consulte [Administración de nombres de dominio personalizados](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Incorporación de más nombres de dominio personalizados
Si su organización usa varios nombres de dominio personalizados, como 'contoso.com' y 'contosobank.com', se puede agregar un máximo de 900 nombres de dominio. Siga los mismos pasos de este artículo para agregar cada nombre de dominio.

## <a name="next-steps"></a>Pasos siguientes
* [Asignación de usuarios a un dominio personalizado](active-directory-add-domain-add-users.md)
* [Managing custom domain names (Administración de nombres de dominio).](active-directory-add-manage-domain-names.md)
* [Información general conceptual de nombres de dominio personalizado en Azure Active Directory](active-directory-add-domain-concepts.md)
* [Incorporación de la personalización de marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)
* [Utilización de PowerShell para administrar los nombres de dominio en Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)




<!--HONumber=Dec16_HO2-->


