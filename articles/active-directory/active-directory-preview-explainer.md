---
title: "Explicación de la versión preliminar de Azure Active Directory | Microsoft Docs"
description: "En este tema se explican las diferencias entre Azure Active Directory en el portal clásico y la versión preliminar de Azure Active Directory en Azure Portal."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e717c11c-e7c9-4565-8e47-1950905e5b3c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 664abe5887b3d96eb48e3b47df9a32648ee623dd


---
# <a name="preview-of-the-azure-active-directory-management-experience-in-the-azure-portal"></a>Versión preliminar de la experiencia de administración de Azure Active Directory en Azure Portal
La experiencia de administración de Azure Active Directory (Azure AD) está en versión preliminar en Azure Portal. Para probarla, inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global de su directorio. A continuación, seleccione Azure Active Directory en la lista de servicios, si está visible, o seleccione **Más servicios** para ver la lista de todos los servicios. No necesita una suscripción de Azure para usar la experiencia de administración de AD en Azure Portal.

## <a name="learn-about-what-you-can-do-in-the-preview-experience"></a>Información sobre lo que puede hacer en la versión preliminar
La experiencia de versión preliminar le permite administrar muchos recursos de directorio diferentes, como usuarios, grupos y aplicaciones, así como configuraciones de directorio, en Azure Portal. Estamos mejorando esta experiencia para incluir todas las funcionalidades que existen en la experiencia de administración de Azure AD en el [Portal de Azure clásico](https://manage.windowsazure.com). Hasta entonces, hay algunas tareas de administración de directorios que debe seguir realizando en el portal clásico.

## <a name="manage-the-same-azure-ad-tenants"></a>Administración de los mismos inquilinos de Azure AD
En la experiencia de versión preliminar se lee y escribe en el mismo inquilino de Azure Active Directory que el portal clásico y el Centro de administración de Office 365. Los cambios realizados en cualquiera de estos portales se reflejan en todos los demás.

## <a name="use-the-same-authorization-logic"></a>Uso de la misma lógica de autorización
En la experiencia de versión preliminar se usa la misma lógica de autorización que en los clientes de Active Directory existentes. Los usuarios tienen autorización para realizar cambios en los recursos de directorio según su rol de directorio, como administrador global, administrador de usuarios o administrador de contraseñas. El hecho de tener un rol en recursos de Azure o en una suscripción de Azure no autoriza a un usuario a administrar recursos de directorio. Para obtener más información sobre los roles de administración de Azure AD, consulte [Asignación de roles de administrador en Azure Active Directory](active-directory-assign-admin-roles.md). 

La experiencia de versión preliminar está optimizada para los administradores globales. Si usa la experiencia de versión preliminar con una sesión iniciada como usuario que no es administrador global, su experiencia se verá limitada. Por ejemplo, podría seleccionar un botón que le permite iniciar una tarea que no se puede completar en el directorio. Pronto mejoraremos esta experiencia.

## <a name="tell-us-what-you-think"></a>Díganos lo que opina.
Puede proporcionar comentarios sobre la experiencia de versión preliminar en la sección del portal de administración del [foro de comentarios de Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc).




<!--HONumber=Nov16_HO3-->


