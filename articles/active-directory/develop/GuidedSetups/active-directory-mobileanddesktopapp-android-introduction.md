---
title: "Introducción a Android en Azure AD v2: primeros pasos | Microsoft Docs"
description: "Cómo puede una aplicación de Android obtener un token de acceso y llamar a las API Graph que requieren tokens de acceso desde el punto de conexión de Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
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
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: d933781713456f73aa76db557fdf35672dfb2a29
ms.contentlocale: es-es

---

# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>Llamada a la API Graph de Microsoft desde una aplicación de Android

En esta guía se muestra cómo una aplicación nativa de Android puede obtener un token de acceso y llamar a la API Graph de Microsoft u otras API que requieran tokens de acceso desde un punto de conexión de Azure Active Directory v2.

Al final de esta guía, la aplicación podrá llamar a una API protegida utilizando cuentas personales (como outlook.com, live.com y otras), así como cuentas profesionales y educativas de cualquier empresa u organización que tenga Azure Active Directory.  

### <a name="how-this-sample-works"></a>Funcionamiento de este ejemplo
![Funcionamiento de este ejemplo](media/active-directory-mobileanddesktopapp-android-intro/android-intro.png)

El ejemplo creado con esta guía se basa en un escenario en el que se usa una aplicación Android para consultar a una API web que acepta tokens desde un punto de conexión de Azure Active Directory v2; en este caso, API Graph de Microsoft. En este escenario, se agrega un token a las solicitudes HTTP a través del encabezado de autorización. La adquisición y la renovación de los tokens se realiza por medio de la biblioteca de autenticación de Microsoft (MSAL).

### <a name="pre-requisites"></a>Requisitos previos
* Esta guía paso a paso se centra en Android Studio, pero cualquier otro entorno de desarrollo de aplicaciones de Android también es aceptable. 
* Se requiere Android SDK 21 o posterior (se recomienda SDK 25).
* Se requiere Google Chrome o un explorador web que use pestañas personalizadas para esta versión de la biblioteca de autenticación de Microsoft (MSAL) para Android.

> Nota: Google Chrome no se incluye en el Emulador de Visual Studio para Android. Le recomendamos que pruebe este código en un emulador con API 25 o una imagen con API 21 o posterior que tenga Google Chrome instalado.


### <a name="how-to-handle-token-acquisition-to-access-a-protected-web-api"></a>Control de la adquisición de tokens para tener acceso a una API web protegida

Una vez que el usuario se autentica, la aplicación de ejemplo recibe un token que se puede utilizar para consultar a la API Graph de Microsoft o a una API web protegida mediante Microsoft Azure Active Directory v2.

Las API como Microsoft Graph requieren un token de acceso para permitir el acceso a recursos específicos; por ejemplo, para leer un perfil de usuario, tener acceso al calendario del usuario o enviar un correo electrónico. La aplicación puede solicitar un token de acceso mediante MSAL para tener acceso a estos recursos mediante la especificación de ámbitos de API. Este token de acceso se agrega entonces al encabezado de autorización de HTTP para todas las llamadas efectuadas al recurso protegido. 

MSAL administra el almacenamiento en caché y la actualización de los tokens de acceso automáticamente, así que no tiene que preocuparse por ello.

### <a name="libraries"></a>Bibliotecas

Esta guía utiliza las siguientes bibliotecas:

|Biblioteca|Descripción|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Biblioteca de autenticación de Microsoft (MSAL)|

