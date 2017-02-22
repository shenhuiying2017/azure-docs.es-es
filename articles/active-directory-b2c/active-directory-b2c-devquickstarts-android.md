---
title: "Azure Active Directory B2C: llamada a una API web desde una aplicación de Android | Microsoft Docs"
description: "Este artículo le mostrará cómo crear una aplicación Android de &quot;lista de tareas pendientes&quot; que llama a una API web de Node.js con tokens de portador de OAuth 2.0. Tanto la aplicación Android como la API web usan Azure Active Directory B2C para administrar identidades de usuario y autenticar usuarios."
services: active-directory-b2c
documentationcenter: android
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/31/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 3f89a0e06ea4596d38f7f7b5e52cea54c5e30780
ms.openlocfilehash: bb94ab8d794d1e836df57e1bc42df42825f2668d


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C: llamada a una API web desde una aplicación Android
> [!WARNING]
> Se ha publicado un ejemplo de código Android actualizado que se puede encontrar [aquí](https://github.com/Azure-Samples/active-directory-b2c-android-native-nodejs-webapi).  Este ejemplo usa una biblioteca de terceros cuya compatibilidad se ha probado en escenarios básicos con B2C de Azure AD.  Esta biblioteca utiliza vistas web incrustadas en lugar del explorador del sistema.  Google [ha anunciado](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html) que el 20 de abril de 2017 dejará de admitirse el inicio de sesión de las cuentas de Google con vistas web incrustadas, por lo que aquellos que quieran continuar admitiendo estas cuentas necesitan actualizar las bibliotecas.  

>No se proporcionan correcciones para bibliotecas de terceros y no se ha realizado ninguna revisión de estas bibliotecas. Los problemas y las solicitudes de características deben dirigirse al proyecto de código abierto de la biblioteca. Vea [este](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) artículo para más información.
>
>




<!--HONumber=Feb17_HO1-->


