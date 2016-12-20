---
title: "Administración de nombres de dominio personalizados en la versión preliminar de Azure Active Directory | Microsoft Docs"
description: "Conceptos y procedimientos de administración de un nombre de dominio en Azure Active Directory"
services: active-directory
documentationcenter: 
author: jeffsta
manager: femila
editor: 
ms.assetid: 5063cd0a-dba2-4ba9-aa65-b8117490d73a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2751b3bdc890fee15d1976de961c2032b9ced2e5


---
# <a name="managing-custom-domain-names-in-your-azure-active-directory-preview"></a>Administración de los nombres de dominio personalizados en la versión preliminar de Azure Active Directory
Un nombre de dominio es una parte importante del identificador para muchos recursos de directorio: forma parte de un nombre de usuario o una dirección de correo electrónico de un usuario, forma parte de la dirección para un grupo y puede formar parte del URI del identificador de una aplicación. Un recurso de la versión preliminar de Azure Active Directory (Azure AD) puede incluir un nombre de dominio cuya pertenencia al directorio que contiene el recurso ya se haya comprobado. [¿Qué es la versión preliminar?](active-directory-preview-explainer.md)  Solo los administradores globales pueden realizar tareas de administración de Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Establecimiento del nombre de dominio principal para su directorio de Azure AD
Cuando se crea el directorio, el nombre de dominio inicial, por ejemplo, contoso.onmicrosoft.com, también será el nombre de dominio principal. El dominio principal será el nombre de dominio predeterminado de un nuevo usuario cuando este se cree. Esto simplifica el proceso para que un administrador cree usuarios nuevos en el portal. Para cambiar el nombre de dominio principal, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba **Azure Active Directory** en el cuadro de texto y presione **ENTRAR**.
   
   ![Apertura de Administración de usuarios](./media/active-directory-domains-add-azure-portal/user-management.png)
3. En la hoja ***nombreDeDirectorio***, seleccione **Nombres de dominio**.
4. En la hoja ***nombreDeDirectorio* -Nombres de dominio**, seleccione el nombre de dominio que desee que sea el principal.
5. En la hoja ***nombreDeDominio*** (es decir, la hoja que se abre con el nombre de dominio nuevo en el título), seleccione el comando **Convertir en principal**. Confirme la elección cuando se le pregunte.
   
   ![Conversión de un nombre de dominio en principal](./media/active-directory-domains-manage-azure-portal/make-primary.png)

Puede cambiar el nombre de dominio principal para el directorio de modo que sea cualquier dominio personalizado comprobado que no esté federado. El hecho de cambiar el dominio principal para el directorio no cambiará los nombres de usuario existentes.

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Adición de dominios personalizados a Azure AD
Puede agregar hasta 900 nombres de dominio personalizado a cada directorio de Azure AD. El proceso de [agregar un nombre de dominio personalizado adicional](active-directory-domains-add-azure-portal.md) es igual para el primer nombre de dominio personalizado.

## <a name="add-subdomains-of-a-custom-domain"></a>Adición de subdominios de un dominio personalizado
Si desea agregar un nombre de dominio de tercer nivel, como "europe.contoso.com", a su directorio, primero debe agregar y comprobar el dominio de segundo nivel, como contoso.com. Azure AD comprobará automáticamente el subdominio. Para ver que se ha comprobado el subdominio que acaba de agregar, actualice la página del explorador que muestra los dominios.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Qué hacer si se cambia el registrador DNS del nombre de dominio personalizado
Si solo cambia el registrador DNS para el nombre de dominio personalizado, puede continuar usando el nombre de dominio personalizado con el propio Azure AD sin interrupción y sin tareas de configuración adicionales. Si usa el nombre de dominio personalizado con Office 365, Intune u otros servicios que dependan de los nombres de dominio personalizados en Azure AD, consulte la documentación de dichos servicios.

## <a name="delete-a-custom-domain-name"></a>Eliminación de un nombre de dominio personalizado
Puede eliminar un nombre de dominio personalizado de Azure AD si su organización ya no utiliza ese nombre de dominio o si necesita utilizar ese nombre de dominio con otro Azure AD.

Para eliminar un nombre de dominio personalizado, primero debe asegurarse de que no haya recursos en el directorio que se basen en el nombre de dominio. No puede eliminar un nombre de dominio de su directorio si:

* Algún usuario tiene un nombre de usuario, una dirección de correo electrónico o una dirección de proxy que incluyan el nombre de dominio.
* Algún grupo tiene una dirección de correo electrónico o una dirección de proxy que incluya el nombre de dominio.
* Alguna aplicación en Azure AD tiene un URI de identificador de aplicación que incluya el nombre de dominio.

Debe cambiar o eliminar dichos recursos en su directorio Azure AD para poder eliminar el nombre de dominio personalizado.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Utilización de PowerShell o API Graph para administrar nombres de dominio
También se pueden completar la mayoría de las tareas de administración para los nombres de dominio de Azure Active Directory mediante Microsoft PowerShell, o mediante programación utilizando la API Graph (en la versión preliminar pública).

* [Utilización de PowerShell para administrar los nombres de dominio en Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Using Graph API to manage domain names in Azure AD (Uso de la API Graph para administrar nombres de dominio en Azure AD)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Pasos siguientes
* [Agregar nombres de dominio personalizados](active-directory-domains-add-azure-portal.md)




<!--HONumber=Nov16_HO3-->


