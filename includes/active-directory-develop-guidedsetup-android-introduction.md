---
title: archivo de inclusión
description: archivo de inclusión
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: aa21168f1e19ccddc190379b30e1b612d96b56d5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32202679"
---
# <a name="sign-in-users-and-all-the-microsoft-graph-api-from-an-android-app"></a>Iniciar sesión de los usuarios y todas las API de Microsoft Graph desde una aplicación de Android

En esta guía se muestra cómo una aplicación nativa de Android puede obtener un token de acceso y llamar a Microsoft Graph API u otras API que requieran tokens de acceso desde un punto de conexión de Azure Active Directory v2.

Cuando haya completado la guía, la aplicación podrá aceptar inicios de sesión de cuentas personales (como outlook.com, live.com y otras), y cuentas profesionales y educativas de cualquier empresa u organización que utilice Azure Active Directory. La aplicación llamará a una API que está protegida por el punto de conexión de Azure Active Directory v2.  

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funcionamiento de la aplicación de ejemplo generada por esta guía
![Funcionamiento de este ejemplo](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

La aplicación de ejemplo creada con esta guía se basa en un escenario en el que se usa una aplicación Android para consultar a una API web que acepta tokens desde un punto de conexión de Azure Active Directory v2 (en este caso, Microsoft Graph API). En este escenario, la aplicación agrega el token adquirido a las solicitudes HTTP mediante el encabezado de autorización. La biblioteca de autenticación de Microsoft (MSAL) administra la adquisición y renovación de los tokens para los usuarios.

## <a name="prerequisites"></a>requisitos previos
* Esta guía paso a paso se centra en Android Studio, pero cualquier otro entorno de desarrollo de aplicaciones de Android también es aceptable. 
* Se requiere Android SDK 21 o posterior (se recomienda SDK 25).
* Se requiere Google Chrome o un explorador web que use pestañas personalizadas para esta versión de MSAL para Android.

> [!NOTE]
> Google Chrome no se incluye en el Emulador de Visual Studio para Android. Le recomendamos que pruebe este código en un emulador con API 25 o una imagen con API 21 o más reciente que tenga Google Chrome instalado.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Tratamiento de la adquisición de tokens para tener acceso a API web protegidas

Una vez que el usuario se autentica, la aplicación de ejemplo recibe un token de acceso que se puede usar para consultar una Microsoft Graph API o a una API web protegida mediante Azure Active Directory v2.

Las API, como Microsoft Graph, requieren un token de acceso para permitir el acceso a recursos específicos. Por ejemplo, es necesario un token de acceso para leer un perfil del usuario, acceder al calendario de un usuario o enviar correo electrónico. La aplicación puede solicitar un token de acceso mediante MSAL para acceder a estos recursos mediante la especificación de ámbitos de API. Este token de acceso se agrega luego al encabezado de autorización de HTTP para todas las llamadas efectuadas al recurso protegido. 

MSAL administra el almacenamiento en caché y la actualización de los tokens de acceso automáticamente, así que no tiene que preocuparse por ello.

## <a name="libraries"></a>Bibliotecas

Esta guía utiliza las siguientes bibliotecas:

|Biblioteca|DESCRIPCIÓN|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Biblioteca de autenticación de Microsoft (MSAL)|
