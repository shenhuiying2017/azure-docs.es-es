---
title: "Adición y administración de varios directorios de Azure Active Directory | Microsoft Docs"
description: Instrucciones y procedimientos recomendados para agregar y administrar directorios de Azure Active Directory, explicando los directorios como recursos totalmente independientes
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 2b862b75-14df-45f2-a8ab-2a3ff1e2eb08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: a4a78b92c8bb9e8aff25fd724ed78283de8f2fd8
ms.openlocfilehash: 23aad0421088b620f32bbf3f6b3f0b11e001e3ae


---
# <a name="add-and-manage-multiple-azure-active-directory-directories"></a>Adición y administración de varios directorios de Azure Active Directory
En Azure Active Directory (Azure AD), cada directorio es un recurso totalmente independiente: un recurso del mismo nivel, con características completas y lógicamente independiente de otros directorios que administre. No hay ninguna relación de elementos primarios y secundarios entre los directorios. Esta independencia entre directorios incluye la independencia de recursos, la independencia administrativa y la independencia de sincronización.

## <a name="resource-independence"></a>Independencia de recursos
Si crea o elimina un recurso en un directorio, esto no tiene efecto en ningún recurso de los demás directorios, con la excepción parcial de los usuarios externos, según se describe más adelante. Si utiliza un dominio personalizado “contoso.com” con un directorio, este no se puede utilizar con ningún otro directorio.

## <a name="administrative-independence"></a>Independencia administrativa
Si un usuario no administrador del directorio "Contoso", crea un directorio de prueba "Prueba". En ese caso: 

* De forma predeterminada, el usuario que crea un directorio se agrega como un usuario externo en ese nuevo directorio y se le asigna el rol de administrador global en ese directorio.
* Los administradores del directorio "Contoso" no tienen privilegios administrativos directos en el directorio "Prueba", a menos que el administrador de "Prueba" les otorgue específicamente estos privilegios. Los administradores de "Contoso" pueden controlar el acceso al directorio "Prueba" si controlan la cuenta de usuario que creó "Prueba".
* Si cambia (agrega o quita) un rol de administrador de un usuario en un directorio, el cambio no afecta a ningún rol de administrador que ese usuario pueda tener en otro directorio.

## <a name="synchronization-independence"></a>Independencia de sincronización
Puede configurar cada directorio de Azure AD de manera independiente para que los datos se sincronicen desde una sola instancia de: 

* La herramienta de sincronización de directorios (DirSync), para sincronizar los datos con un único bosque de AD.
* El conector de Azure Active Directory para Forefront Identity Manager, para sincronizar datos con uno o varios bosques locales u orígenes de datos distintos de Azure AD.

## <a name="add-an-azure-ad-directory"></a>Adición de un directorio de Azure AD
Para agregar un directorio de Azure AD en el Portal de Azure clásico, seleccione la extensión de Azure Active Directory de la izquierda y seleccione **Agregar**.

> [!NOTE]
> A diferencia de otros recursos de Azure, los directorios no son recursos secundarios de una suscripción a Azure. Si cancela su suscripción a Azure o deja que esta caduque, aún podrá tener acceso a los datos de su directorio mediante Azure PowerShell, la API Graph de Azure u otras interfaces, como el Centro de administración de Office 365. También puede asociar otra suscripción con el directorio.
>
>

## <a name="next-steps"></a>Pasos siguientes
Para obtener una amplia visión general de los problemas de licencias de Azure AD y prácticas recomendadas, consulte [¿Qué es la licencia de Azure Active Directory?](active-directory-licensing-what-is.md)



<!--HONumber=Feb17_HO2-->


