---
title: "Sincronización de Azure AD Connect: Extensiones de directorio | Microsoft Docs"
description: "En este tema se describe la característica de extensiones de directorio en Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: d586df0ede6ff9b3a4837a4a9c551ab3ee6d763a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
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

La lista de atributos se lee de la memoria caché de esquema creada durante la instalación de Azure AD Connect. Si ha ampliado el esquema de Active Directory con atributos adicionales, [se debe actualizar el esquema](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) para que los nuevos atributos estén visibles.

Un objeto de Azure AD puede tener hasta 100 atributos de extensiones de directorio. La longitud máxima es de 250 caracteres. Si un valor de atributo es mayor, el motor de sincronización lo trunca.

Durante la instalación de Azure AD Connect, se registra una aplicación donde estos atributos estén disponibles. Puede ver esta aplicación en el Portal de Azure.  
![Aplicación de extensión de esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

Estos atributos están ahora disponibles en Graph:   
![Gráfico](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Los atributos tienen el prefijo extension \_{AppClientId}\_. El AppClientId tiene el mismo valor en todos los atributos del directorio de Azure AD.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
