---
title: "Administración de nombres de dominio personalizados en Azure Active Directory | Microsoft Docs"
description: "Conceptos y procedimientos de administración de un dominio personalizado en Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cf3523bd-9ee0-439e-963d-ccea038867b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.openlocfilehash: 5ae19bb370064de96cf466ca09b13d02563d65a4
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Administración de los nombres de dominio personalizados en Azure Active Directory
Un nombre de dominio puede ser un identificador importante para muchos recursos de directorio, como parte de:

* Un nombre de usuario o una dirección de correo electrónico de un usuario
* La dirección de un grupo
* El identificador URI del id. de aplicación para una aplicación

Un recurso en Azure Active Directory (Azure AD) puede incluir un nombre de dominio cuya pertenencia al directorio que contiene el recurso ya se haya verificado. Solo los administradores globales pueden realizar tareas de administración de Azure AD.

> [!IMPORTANT]
> Microsoft recomienda administrar Azure AD con el [Centro de administración de Azure AD](https://aad.portal.azure.com) en Azure Portal en lugar de usar el portal de Azure clásico al que se hace referencia en este artículo. Para saber cómo administrar los nombres de dominio en el centro de administración de Azure AD, consulte [Administración de los nombres de dominio personalizados en Azure Active Directory](active-directory-domains-manage-azure-portal.md).

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Establecimiento del nombre de dominio principal para su directorio de Azure AD
Cuando se crea el directorio, el nombre de dominio inicial, por ejemplo, "contoso.onmicrosoft.com", también es el nombre de dominio principal para el directorio. El dominio principal es el nombre de dominio predeterminado para un nuevo usuario cuando se crea un nuevo usuario en el [Portal de Azure clásico](https://manage.windowsazure.com/)o en otros portales, como el portal de administración de Office 365. Esto simplifica el proceso para que un administrador cree usuarios nuevos en el portal.

Para cambiar el nombre de dominio principal para el directorio:

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con una cuenta de usuario que sea administrador global de su directorio de Azure AD.
2. Seleccione **Active Directory** en la barra de navegación de la izquierda.
3. Abra el directorio.
4. Seleccione la pestaña **Dominios** .
5. Seleccione el botón **Cambiar principal** en la barra de comandos.
6. Seleccione el dominio que desea convertir en el nuevo dominio principal para el directorio.

Puede cambiar el nombre de dominio principal para el directorio de modo que sea cualquier dominio personalizado comprobado que no esté federado. El hecho de cambiar el dominio principal para el directorio no cambiará los nombres de usuario existentes.

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Adición de dominios personalizados a Azure AD
Puede agregar hasta 900 nombres de dominio personalizado a cada directorio de Azure AD. El proceso de [agregar un nombre de dominio personalizado adicional](active-directory-add-domain.md) es igual para el primer nombre de dominio personalizado.

## <a name="add-subdomains-of-a-custom-domain"></a>Adición de subdominios de un dominio personalizado
Si desea agregar un nombre de dominio de tercer nivel, como "europe.contoso.com", a su directorio, primero debe agregar y comprobar el dominio de segundo nivel, como contoso.com. Azure AD comprobará automáticamente el subdominio. Para ver que se ha comprobado el subdominio que acaba de agregar, actualice la página del explorador que muestra los dominios en el directorio.

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
También se pueden completar la mayoría de las tareas de administración para los nombres de dominio de Azure Active Directory mediante Microsoft PowerShell, o mediante programación utilizando la API de Azure Graph.

* [Utilización de PowerShell para administrar los nombres de dominio en Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Using Graph API to manage domain names in Azure AD (Uso de la API Graph para administrar nombres de dominio en Azure AD)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Pasos siguientes
* [Obtenga más información acerca de los nombres de dominio en Azure AD](active-directory-add-domain-concepts.md)
* [Managing custom domain names (Administración de nombres de dominio).](active-directory-add-manage-domain-names.md)

