---
title: "Introducción al escritorio de Windows en Azure AD v2: primeros pasos | Microsoft Docs"
description: "Cómo pueden llamar las aplicaciones .NET de escritorio de Windows (XAML) a una API que requiera tokens de acceso mediante el punto de conexión de Azure Active Directory v2"
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
ms.openlocfilehash: 4a695c00fce4deb02261ba58ec95469746bb1486
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Llamada a la API Graph de Microsoft desde una aplicación de escritorio de Windows

Esta guía muestra cómo una aplicación nativa de .NET de escritorio de Windows (XAML) puede obtener un token de acceso y llamar a la API Graph de Microsoft u otras API que requieran tokens de acceso desde un punto de conexión de Azure Active Directory v2.

Al final de esta guía, la aplicación podrá llamar a una API protegida utilizando cuentas personales (como outlook.com, live.com y otras), así como cuentas profesionales y educativas de cualquier empresa u organización que tenga Azure Active Directory.  

> Esta guía requiere Visual Studio 2015 Update 3 o Visual Studio 2017.  ¿No lo tiene? [Descargue Visual Studio 2017 de manera gratuita](https://www.visualstudio.com/downloads/)

### <a name="how-this-guide-works"></a>Funcionamiento de esta guía

![Funcionamiento de esta guía](media/active-directory-mobileanddesktopapp-windowsdesktop-intro/windesktophowitworks.png)

La aplicación de ejemplo que se crea con esta guía permite que una aplicación de escritorio de Windows consulte a la API Graph de Microsoft o a una API web que acepte tokens desde un punto de conexión de Azure Active Directory v2. En este escenario, se agrega un token a las solicitudes HTTP a través del encabezado de autorización. La adquisición y la renovación de los tokens se realiza por medio de la biblioteca de autenticación de Microsoft (MSAL).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Tratamiento de la adquisición de tokens para tener acceso a API web protegidas

Una vez que el usuario se autentica, la aplicación de ejemplo recibe un token que se puede utilizar para consultar a la API Graph de Microsoft o a una API web protegida mediante Microsoft Azure Active Directory v2.

Las API como Microsoft Graph requieren un token de acceso para permitir el acceso a recursos específicos; por ejemplo, para leer un perfil de usuario, tener acceso al calendario del usuario o enviar un correo electrónico. La aplicación puede solicitar un token de acceso mediante MSAL para tener acceso a estos recursos mediante la especificación de ámbitos de API. Este token de acceso se agrega entonces al encabezado de autorización de HTTP para todas las llamadas efectuadas al recurso protegido. 

MSAL administra el almacenamiento en caché y la actualización de los tokens de acceso automáticamente, así que no tiene que preocuparse por ello.


### <a name="nuget-packages"></a>Paquetes de NuGet

Esta guía utiliza los siguientes paquetes NuGet:

|Biblioteca|Descripción|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de autenticación de Microsoft (MSAL)|

