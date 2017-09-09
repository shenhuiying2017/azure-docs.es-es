---
title: "Incorporación de un dominio personalizado a Azure AD | Microsoft Docs"
description: Describe como eliminar un dominio personalizado en Azure Active Directory.
services: active-directory
author: jeffgilb
manager: femila
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 3acdc406b5e753d407c7c6fb32fe0ad5ed6dd68c
ms.contentlocale: es-es
ms.lasthandoff: 08/30/2017

---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>Guía de inicio rápido: Incorporación de un nombre de dominio personalizado a Azure Active Directory

Cada directorio de Azure AD incluye un nombre de dominio inicial con el formato *nombre de dominio*.onmicrosoft.com. El nombre de dominio inicial no se puede cambiar ni eliminar, pero se puede agregar también el nombre de dominio corporativo a Azure AD. Por ejemplo, puede que su organización tenga otros nombres de dominio que se usan para hacer negocios y usuarios que inician sesión con el nombre de dominio corporativo. La incorporación de nombres de dominio personalizados a Azure AD le permite asignar en el directorio nombres de usuario conocidos para sus usuarios, como "alice@contoso.com". en lugar de "alice@*<domain name>*.onmicrosoft.com". El proceso es simple:

1. Incorporación de nombres de dominio personalizados al directorio
2. Incorporación de la entrada DNS para el nombre de dominio en el registrador de nombres de dominio
3. Comprobación del nombre de dominio personalizado en Azure AD

## <a name="add-your-custom-domain"></a>Incorporación del dominio personalizado
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba **Azure Active Directory** en el cuadro de texto y presione **ENTRAR**.
   
   ![Apertura de Administración de usuarios](./media/active-directory-domains-add-azure-portal/user-management.png)
3. En la hoja ***nombreDeDirectorio***, seleccione **Nombres de dominio**.
4. En la hoja ***nombreDeDirectorio* - Nombres de dominio**, seleccione el comando **Agregar**.
   
   ![Selección del comando Agregar](./media/active-directory-domains-add-azure-portal/add-command.png)
5. En la hoja **Nombre de dominio**, escriba el nombre del dominio personalizado en el cuadro, como "contoso.com", y seleccione **Agregar dominio**. Asegúrese de incluir .com, .net u otra extensión de nivel superior.
6. En la hoja ***nombreDeDominio*** (que tiene el nombre de dominio personalizado en el título), obtenga la información de entrada DNS que se usará para comprobar que la organización posee el nombre de dominio personalizado.
   
   ![Obtención de información de entrada de DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> Si tiene previsto federar la instancia local de Windows Server AD en Azure AD, tiene que activar la casilla **Voy a configurar este dominio para el inicio de sesión único con mi Active Directory local** cuando ejecute la herramienta Azure AD Connect para sincronizar los directorios. También tiene que registrar el mismo nombre de dominio que seleccione para la federación con su directorio local en el paso **Dominio de Azure AD** del asistente. Puede comprobar cómo es ese paso del asistente [en estas instrucciones](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Si no tiene la herramienta Azure AD Connect, [descárguela aquí](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Incorporación de la entrada DNS en el registrador de nombres de dominio para el dominio
El siguiente paso para usar el nombre de dominio personalizado con Azure AD consiste en actualizar el archivo de zona DNS para el dominio. Azure AD puede comprobar luego que la organización posee el nombre de dominio personalizado. Puede usar [Azure DNS en Azure Portal](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) como punto de administración único de los registros de Azure, Office 365 y DNS externos dentro de Azure.

1. Inicie sesión en el registrador de nombres de dominio para el dominio. Si no tiene acceso para actualizar la entrada DNS, pida a la persona o equipo que tenga acceso que complete el paso 2 y que le informe cuando esté completo.
2. Actualice el archivo de zona DNS para el dominio. Para ello, agregue la entrada DNS que le proporcione Azure AD. Esta entrada DNS permite a Azure AD comprobar la propiedad del dominio. No cambiará ningún comportamiento, como el enrutamiento de correo o el hospedaje web.

Para obtener ayuda con la incorporación de la entrada DNS, lea [Instrucciones para agregar entradas DNS en los registradores DNS más habituales](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Comprobación del nombre de dominio con Azure AD
Una vez agregada la entrada DNS, ya puede comprobar el nombre de dominio con Azure AD.

Solo se puede comprobar un nombre de dominio después de que se hayan propagado los registros de DNS. Esta propagación a suele tardar unos segundos, pero a veces puede tardar una hora o incluso más. Si la comprobación no funciona la primera vez, inténtelo de nuevo más tarde.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Examinar**, escriba Administración de usuarios en el cuadro de texto y seleccione **ENTRAR**.
   
   ![Apertura de Administración de usuarios](./media/active-directory-domains-add-azure-portal/user-management.png)
3. En la hoja **Administración de usuarios - Nombres de dominio** , seleccione el nombre de dominio sin comprobar que quiere comprobar.
4. En la hoja ***nombreDeDominio*** (es decir, la hoja que se abre con el nuevo nombre de dominio en el título), seleccione **Comprobar** para finalizar la comprobación.

> [!TIP]
> Puede agregar hasta 900 nombres de dominio personalizados, pero solo uno se puede [establecer como nombre de dominio personalizado del directorio de Azure AD](active-directory-domains-manage-azure-portal.md#set-the-primary-domain-name-for-your-azure-ad-directory) que se usa de manera predeterminada al crear otras cuentas.

Ahora puede crear cuentas de usuario basadas en la nube, o actualizar información de cuentas de usuario locales sincronizadas previamente, con el nombre de dominio personalizado. También puede modificar información de sufijo de dominio de cuentas de usuario sincronizadas previamente con [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) o la [API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## <a name="troubleshooting"></a>Solución de problemas
Si no puede comprobar un nombre de dominio personalizado, pruebe los siguientes pasos de solución de problemas:

1. **Espere una hora**. Los registros de DNS deben propagarse antes de que Azure AD pueda comprobar el dominio. Esto puede tardar una hora o más.
2. **Asegúrese de que se especificó el registro de DNS y que es correcto**. Complete este paso en el sitio web del registrador de nombres de dominio para el dominio. Azure AD no puede comprobar el nombre de dominio si la entrada DNS no está presente en el archivo de zona DNS, o si no es una coincidencia exacta con la entrada DNS que Azure AD le ha proporcionado. Si no tiene acceso para actualizar los registros de DNS para el dominio en el nombre de dominio, comparta la entrada DNS con la persona o el equipo de su organización que cuente con este acceso y solicítele que la agregue.
3. **Elimine el nombre de dominio de otro directorio en Azure AD**. Se puede comprobar el nombre de dominio en solo un único directorio. Si previamente se ha comprobado un nombre de dominio en otro directorio, se debe eliminar allí antes de que se puede comprobar en el nuevo directorio. Para más información sobre cómo eliminar los nombres de dominio, consulte [Administración de nombres de dominio personalizados](active-directory-domains-manage-azure-portal.md).    

## <a name="add-more-custom-domain-names"></a>Incorporación de más nombres de dominio personalizados
Si la organización usa más de un nombre de dominio personalizado, como, por ejemplo, "contoso.com" y "contosobank.com", puede agregar hasta 900 más repitiendo los pasos descritos en este artículo para cada uno.

### <a name="learn-more"></a>Más información
[Información general conceptual de los nombres de dominio personalizados en Azure Active Directory](active-directory-add-domain-concepts.md)

[Managing custom domain names (Administración de nombres de dominio).](active-directory-domains-manage-azure-portal.md)


## <a name="next-steps"></a>Pasos siguientes
En esta guía de inicio rápido, ha aprendido a agregar un dominio personalizado a Azure AD. 

Puede usar el vínculo siguiente para agregar un nuevo dominio personalizado en Azure AD desde Azure Portal.

> [!div class="nextstepaction"]
> [Agregar un dominio personalizado](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 
