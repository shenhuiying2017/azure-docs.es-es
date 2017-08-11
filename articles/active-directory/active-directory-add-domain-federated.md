---
title: "Incorporación de su nombre de dominio personalizado y configuración de un inicio de sesión federado en Azure Active Directory | Microsoft Docs"
description: "Cómo agregar nombres de dominio de su compañía en Azure Active Directory para configurar un inicio de sesión federado entre Azure Active Directory y su solución de federación local"
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 27126c7e-e6d6-4ef3-a4fb-f5f0706e749d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 7f257dff6fdd423b89d1c52a84b64951dcf5915f
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017

---
# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>Incorporación de su nombre de dominio personalizado a Azure Active Directory
Puede configurar un nombre de dominio personalizado, como 'contoso.com', para que los usuarios de contoso.com puedan tener un inicio de sesión único federado de la red corporativa. Si ya cuenta con Servicios de federación de Active Directory (AD FS) o un servidor de federación diferentes que se ejecutan en su red corporativa, puede configurar Azure AD para utilizar su nombre de dominio personalizado mediante la herramienta Azure AD Connect. También puede usar Azure AD Connect para implementar un nuevo entorno de AD FS y configurarlo para el inicio de sesión único federado en Azure AD.

Si no tiene AD FS u otro servidor de federación, ni piensa implementarlo, siga estas instrucciones: [Incorporación de su nombre de dominio personalizado a Azure Active Directory](active-directory-add-domain.md).

## <a name="add-a-custom-domain-name-to-your-directory"></a>Incorporación de nombres de dominio personalizados al directorio
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con una cuenta de usuario que sea administrador global de su directorio de Azure AD.
2. En **Active Directory**, abra el directorio y seleccione la pestaña **Dominios**.
3. En la barra de comandos, seleccione **Agregar**y después escriba el nombre de dominio personalizado, como 'contoso.com'. Asegúrese de incluir .com, .net u otra extensión de nivel superior.
4. Active la casilla **Tengo previsto configurar este dominio para el inicio de sesión único con mi servicio Active Directory local** .
5. Seleccione **Agregar**.

Ejecute la herramienta Azure AD Connect para obtener la entrada DNS que utilizará Azure AD para comprobar el dominio. Verá la entrada DNS en el paso **Dominio de Azure AD** del asistente. Puede comprobar cómo es ese paso del asistente [en estas instrucciones](connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Si no tiene la herramienta Azure AD Connect, [descárguela aquí](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Incorporación de la entrada DNS en el registrador de nombres de dominio para el dominio
El siguiente paso para usar el nombre de dominio personalizado con Azure AD consiste en actualizar el archivo de zona DNS para el dominio. Esto permite a Azure AD comprobar que su organización posee el nombre de dominio personalizado.

1. Inicie sesión en el sitio web del registrador de nombres de dominio para el nombre de dominio. Si no tiene acceso, pida a la persona o equipo de su organización que tenga acceso que complete el paso 2 y que le informe cuando esté completo.
2. Actualice el archivo de zona DNS para el dominio. Para ello, agregue la entrada DNS que le proporcione Azure AD. Esta entrada DNS permite a Azure AD comprobar la propiedad del dominio. No cambiará ningún comportamiento, como el enrutamiento de correo o el hospedaje web.

Para obtener ayuda con este paso, lea [Instrucciones para agregar entradas DNS en los registradores DNS más habituales](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Comprobación del nombre de dominio con Azure AD
Una vez agregada la entrada DNS, ya puede comprobar el nombre de dominio con Azure AD.

Para comprobar el dominio, seleccione **Siguiente** en el paso **Dominio de Azure AD** del Asistente de Azure AD Connect. Azure AD buscará la entrada DNS en el archivo de zona DNS para el dominio. Azure AD solo comprobará el nombre de dominio una vez se hayan propagado los registros DNS. La propagación suele tardar unos segundos, pero a veces puede tardar una hora o incluso más. Si la comprobación no funciona la primera vez, inténtelo de nuevo más tarde.

Después, continúe con los pasos siguientes del asistente de Azure AD Connect. Esto sincronizará los usuarios de Windows Server AD a Azure AD. Los usuarios sincronizados en el dominio que ha configurado para la federación podrán obtener una experiencia del inicio de sesión único federado de la red corporativa a Azure AD.

## <a name="troubleshooting"></a>Solución de problemas
Si no puede comprobar un nombre de dominio personalizado, intente lo siguiente. Comenzaremos con las más comunes hasta llegar a las menos frecuentes.

1. **Espere una hora**. Los registros de DNS deben propagarse antes de que Azure AD pueda comprobar el dominio. Esto puede tardar una hora o más.
2. **Asegúrese de que se especificó el registro de DNS y que es correcto**. Complete este paso en el sitio web del registrador de nombres de dominio para el dominio. Azure AD no puede comprobar el nombre de dominio si la entrada DNS no está presente en el archivo de zona DNS, o si no es una coincidencia exacta con la entrada DNS que Azure AD le ha proporcionado. Si no tiene acceso para actualizar los registros de DNS para el dominio en el nombre de dominio, comparta la entrada DNS con la persona o el equipo de su organización que cuente con este acceso y solicítele que la agregue.
3. **Elimine el nombre de dominio de otro directorio en Azure AD**. Se puede comprobar el nombre de dominio en solo un único directorio. Si previamente se ha comprobado un nombre de dominio en otro directorio, se debe eliminar allí antes de que se puede comprobar en el nuevo directorio. Para más información sobre cómo eliminar los nombres de dominio, consulte [Administración de nombres de dominio personalizados](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Incorporación de más nombres de dominio personalizados
Si su organización usa varios nombres de dominio personalizados, como 'contoso.com' y 'contosobank.com', se puede agregar un máximo de 900 nombres de dominio. Siga los mismos pasos de este artículo para agregar cada nombre de dominio.

## <a name="next-steps"></a>Pasos siguientes
* [Managing custom domain names (Administración de nombres de dominio).](active-directory-add-manage-domain-names.md)
* [Información general conceptual de nombres de dominio personalizado en Azure Active Directory](active-directory-add-domain-concepts.md)
* [Incorporación de la personalización de marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)
* [Utilización de PowerShell para administrar los nombres de dominio en Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)


