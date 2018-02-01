---
title: "Sincronización de Azure AD Connect: Extensiones de directorio | Microsoft Docs"
description: "En este tema se describe la característica de extensiones de directorio en Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 9abd035b13a0d51c534eb8cac50c045012399a69
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Sincronización de Azure AD Connect: Extensiones de directorio
Las extensiones de directorio le permiten ampliar el esquema de Azure AD con sus propios atributos desde Active Directory local. Esta característica permite crear aplicaciones de LOB mediante el consumo de atributos que se siguen administrando de forma local. Estos atributos se pueden consumir mediante [extensiones de directorio de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) o [Microsoft Graph](https://graph.microsoft.io/). Puede ver los atributos disponibles mediante el [explorador de Azure AD Graph](https://graphexplorer.azurewebsites.net/) y el [explorador de Microsoft Graph](https://developer.microsoft.com/en-us/graph/graph-explorer), respectivamente.

En la actualidad, ninguna carga de trabajo de Office 365 consume estos atributos.

Configure qué atributos adicionales desea sincronizar en la ruta de acceso de configuración personalizada en el Asistente para instalación.
![Asistente para la extensión de esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  
La instalación muestra los atributos siguientes, que son candidatos válidos:

* Tipos de objetos de usuario y de grupo
* Atributos de valor único: cadena, booleano, entero y binario
* Atributos multivalor: cadena y binario


>[!NOTE]
> Aunque Azure AD Connect admite la sincronización con varios valores de atributos de AD con Azure AD como extensiones de directorio con varios valores, actualmente no hay ninguna característica de Azure AD que admita el uso de extensiones de directorio con varios valores.

La lista de atributos se lee de la memoria caché de esquema creada durante la instalación de Azure AD Connect. Si ha ampliado el esquema de Active Directory con atributos adicionales, [se debe actualizar el esquema](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) para que los nuevos atributos estén visibles.

Un objeto de Azure AD puede tener hasta 100 atributos de extensiones de directorio. La longitud máxima es de 250 caracteres. Si un valor de atributo es mayor, el motor de sincronización lo trunca.

Durante la instalación de Azure AD Connect, se registra una aplicación donde estos atributos estén disponibles. Puede ver esta aplicación en el Portal de Azure.  
![Aplicación de extensión de esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

Los atributos tienen el prefijo extension \_{AppClientId}\_. El AppClientId tiene el mismo valor en todos los atributos del directorio de Azure AD.

Estos atributos están disponibles ahora en **Azure AD Graph**:

Es posible consultar Azure AD Graph mediante el Explorador de Azure AD Graph: [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)

![Grafo](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

O mediante **Microsoft Graph API**:

Es posible consultar Microsoft Graph API mediante el Explorador de Microsoft Graph: [https://developer.microsoft.com/en-us/graph/graph-explorer#](https://developer.microsoft.com/en-us/graph/graph-explorer#)

>[!NOTE]
> Debe pedir explícitamente que se devuelva el atributo. Para hacerlo, seleccione de manera explícita los atributos de esta manera: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division Para más información, revise [Microsoft Graph: uso de parámetros de consulta](https://developer.microsoft.com/en-us/graph/docs/concepts/query_parameters#select-parameter)

## <a name="next-steps"></a>pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
