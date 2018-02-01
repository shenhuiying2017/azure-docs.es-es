---
title: "Introducción al escritorio de Windows en Azure AD v2: configuración | Microsoft Docs"
description: "Cómo puede obtener una aplicación .NET de escritorio de Windows (XAML) un token de acceso y llamar a una API protegida por un punto de conexión de Azure Active Directory v2."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: c42f40252733f0c4fd7fdbabb49714ea537acc70
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Adición de información de registro de la aplicación a su aplicación
En este paso, debe agregar el identificador de aplicación a su proyecto.

1.  Abra `App.xaml.cs` y reemplace la línea que contiene el `ClientId` con:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Pasos siguientes

[!INCLUDE [Test and Validate](..\..\..\..\includes\active-directory-develop-guidedsetup-windesktop-test.md)]
