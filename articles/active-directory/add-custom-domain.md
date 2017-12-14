---
title: "Incorporación de un dominio personalizado a Azure AD | Microsoft Docs"
description: Describe como eliminar un dominio personalizado en Azure Active Directory.
services: active-directory
author: curtand
manager: mtillman
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: e7b85d5f4cd19c94fe904f16090e174d87ea120b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>Guía de inicio rápido: Incorporación de un nombre de dominio personalizado a Azure Active Directory

Cada directorio de Azure AD incluye un nombre de dominio inicial con el formato *nombre de dominio*.onmicrosoft.com. El nombre de dominio inicial no se puede cambiar ni eliminar, pero se puede agregar también el nombre de dominio corporativo a Azure AD. Por ejemplo, puede que su organización tenga otros nombres de dominio que se usan para hacer negocios y usuarios que inician sesión con el nombre de dominio corporativo. La incorporación de nombres de dominio personalizados a Azure AD le permite asignar en el directorio nombres de usuario conocidos para sus usuarios, como "alice@contoso.com". en lugar de "alice@*nombre de dominio*.onmicrosoft.com". El proceso es simple:

1. Incorporación de nombres de dominio personalizados al directorio
2. Incorporación de la entrada DNS para el nombre de dominio en el registrador de nombres de dominio
3. Comprobación del nombre de dominio personalizado en Azure AD

## <a name="add-the-custom-domain-name-to-your-directory"></a>Incorporación de nombres de dominio personalizados al directorio
1. Inicie sesión en [Azure Portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con una cuenta que tenga el rol de administrador global en el directorio.
2. En el lado izquierdo, seleccione **Nombres de dominio personalizados**.
3. Seleccione **Agregar dominio personalizado**.
   
   ![Selección del comando Agregar](./media/add-custom-domain/add-custom-domain.png)
5. En **Nombres de dominio personalizados**, escriba el nombre del dominio personalizado en el cuadro (por ejemplo, "contoso.com") y seleccione **Agregar dominio**. Asegúrese de incluir .com, .net u otra extensión de nivel superior.
6. En ***nombre-del-dominio*** (es decir, el nuevo nombre de dominio es el título), recopile la información de entrada de DNS que se usará para comprobar el nombre de dominio personalizado en Azure AD.
   
   ![Obtención de información de entrada de DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> Si tiene previsto federar la instancia local de Windows Server AD en Azure AD, tiene que activar la casilla **Voy a configurar este dominio para el inicio de sesión único con mi Active Directory local** cuando ejecute la herramienta Azure AD Connect para sincronizar los directorios. También tiene que registrar el mismo nombre de dominio que seleccione para la federación con su directorio local en el paso **Dominio de Azure AD** del asistente. Puede comprobar cómo es ese paso del asistente [en estas instrucciones](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Si no tiene la herramienta Azure AD Connect, [descárguela aquí](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-a-dns-entry-for-the-domain-name-at-the-domain-name-registrar"></a>Incorporación de la entrada DNS para el nombre de dominio en el registrador de nombres de dominio
El siguiente paso para usar el nombre de dominio personalizado con Azure AD consiste en actualizar el archivo de zona DNS para el dominio. Azure AD puede comprobar luego que la organización posee el nombre de dominio personalizado. Puede usar [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) para los registros DNS de Azure, Office 365 y externos de Azure o agregar la entrada DNS en [un registrador DNS diferente](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Inicie sesión en el registrador de nombres de dominio para el dominio. Si no tiene acceso para actualizar la entrada DNS, pida a la persona o equipo que tenga acceso que complete el paso 2 y que le informe cuando esté completo.
2. Actualice el archivo de zona DNS para el dominio. Para ello, agregue la entrada DNS que le proporcione Azure AD. No cambiará ningún comportamiento, como el enrutamiento de correo o el hospedaje web.

## <a name="verify-the-custom-domain-name-in-azure-ad"></a>Comprobación del nombre de dominio personalizado en Azure AD
Una vez agregada la entrada DNS, ya puede comprobar el nombre de dominio con Azure AD. Solo se puede comprobar un nombre de dominio después de que se hayan propagado los registros de DNS. Esta propagación a suele tardar unos segundos, pero a veces puede tardar una hora o incluso más. Si la comprobación no funciona la primera vez, inténtelo de nuevo más tarde.

1. Inicie sesión en [Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con una cuenta que tenga el rol de administrador global en el inquilino.
2. Seleccione **Nombres de dominio personalizados**.
3. Seleccione el nombre de dominio no verificado que quiera verificar.
4. Compruebe las entradas y seleccione **Verificar** para completar la verificación.

Ya puede [asignar nombres de usuario que incluyan su nombre de dominio personalizado](active-directory-users-create-azure-portal.md). Con el nombre de dominio personalizado puede crear cuentas de usuario basadas en la nube o actualizar información de cuentas de usuario locales sincronizadas previamente. También puede cambiar información de sufijo de dominio de cuentas de usuario sincronizadas con [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) o la [API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

> [!TIP]
> Puede agregar un máximo de 900 nombres de dominio administrados. Si quiere configurar todos los dominios de la federación local con Active Directory, puede agregar un máximo de 450 nombres de dominio en cada directorio. Para más información, consulte [Nombres de dominio federados y administrados](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="troubleshooting"></a>Solución de problemas
Si no puede comprobar un nombre de dominio personalizado, pruebe los siguientes pasos de solución de problemas:

1. **Espere una hora**. Los registros de DNS deben propagarse antes de que Azure AD compruebe el dominio. Este proceso puede tardar una hora o más.
2. **Asegúrese de que se especificó el registro de DNS y que es correcto**. Complete este paso en el sitio web del registrador de nombres de dominio para el dominio. Azure AD no puede comprobar el nombre de dominio si 
  * La entrada DNS no está presente en el archivo de zona DNS
  * No es una coincidencia exacta con la entrada DNS que le proporcionó Azure AD. 
  
  Si no tiene acceso para actualizar los registros de DNS para el dominio en el nombre de dominio, comparta la entrada DNS con la persona o el equipo de su organización que cuente con este acceso y solicítele que la agregue.
3. **Elimine el nombre de dominio de otro directorio en Azure AD**. Se puede comprobar el nombre de dominio en solo un único directorio. Si ya se ha comprobado un nombre de dominio en un directorio diferente, no se puede comprobar en el directorio nuevo hasta que se haya eliminado del otro. Para más información sobre cómo eliminar los nombres de dominio, consulte [Administración de nombres de dominio personalizados](active-directory-domains-manage-azure-portal.md).    

Repita los pasos de este artículo para agregar cada nombre de dominio.

## <a name="learn-more"></a>Más información
[Información general conceptual de los nombres de dominio personalizados en Azure Active Directory](active-directory-domains-manage-azure-portal.md)

[Managing custom domain names (Administración de nombres de dominio).](active-directory-domains-manage-azure-portal.md)

## <a name="next-steps"></a>Pasos siguientes
En esta guía de inicio rápido, ha aprendido a agregar un dominio personalizado a Azure AD. 

Puede usar el vínculo siguiente para agregar un nuevo dominio personalizado en Azure AD desde Azure Portal.

> [!div class="nextstepaction"]
> [Agregar un dominio personalizado](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 