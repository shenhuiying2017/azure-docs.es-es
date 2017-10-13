---
title: "Introducción a iOS en Azure AD v2: configuración (ARP) | Microsoft Docs"
description: "Cómo pueden llamar las aplicaciones de iOS (Swift) a una API que requiera tokens de acceso mediante el punto de conexión de Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 50cb4a2803b6aebe8b39ec9fb02da2293c1065fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Adición de información de registro de la aplicación a su aplicación

En este paso, debe agregar el identificador de aplicación a su proyecto:

1.  En `ViewController.swift`, reemplace la línea que empieza con '`let kClientID`' por:
```swift
let kClientID = "[Enter the application Id here]"
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Presione Control y haga clic en <code>Info.plist</code> para mostrar el menú contextual y, a continuación, haga clic en: <code>Open As</code> > <code>Source Code</code>
</li>
<li>
Agregue lo siguiente bajo el nodo raíz <code>dict</code>:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Enter the application Id here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
