---
title: "Introducción a iOS en Azure AD v2 | Microsoft Docs"
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
ms.openlocfilehash: 948693c8501ecc46a1508e5ea085846d0910783e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="call-the-microsoft-graph-api-from-an-ios-app"></a>Llamada a la API de Microsoft Graph desde una aplicación iOS

En esta guía se muestra cómo una aplicación nativa de iOS (Swift) puede obtener un token de acceso y llamar a la API de Microsoft Graph u otras API que requieran tokens de acceso desde un punto de conexión de Azure Active Directory v2.

Al final de esta guía, la aplicación podrá llamar a una API protegida utilizando cuentas personales (como outlook.com, live.com y otras), así como cuentas profesionales y educativas de cualquier empresa u organización que tenga Azure Active Directory.

> ### <a name="pre-requisites"></a>Requisitos previos
> - Para esta guía se requiere XCode 8.x. Puede descargar XCode [aquí](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "Dirección URL de descarga de XCode").
> - [Carthage](https://github.com/Carthage/Carthage) para administración de paquetes

### <a name="how-this-guide-works"></a>Funcionamiento de esta guía

![Funcionamiento de esta guía](media/active-directory-mobileanddesktopapp-ios-introduction/iosintro.png)

La aplicación de ejemplo que se crea con esta guía permite que una aplicación iOS consulte a la API de Microsoft Graph o a una API web que acepte tokens desde un punto de conexión de Azure Active Directory v2. En este escenario, se agrega un token a las solicitudes HTTP a través del encabezado de autorización. La adquisición y la renovación de los tokens se realiza por medio de la biblioteca de autenticación de Microsoft (MSAL).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Tratamiento de la adquisición de tokens para tener acceso a API web protegidas

Una vez que el usuario se autentica, la aplicación de ejemplo recibe un token que se puede utilizar para consultar a la API de Microsoft Graph o a una API web protegida mediante Microsoft Azure Active Directory v2.

Las API como Microsoft Graph requieren un token de acceso para permitir el acceso a recursos específicos; por ejemplo, para leer un perfil de usuario, tener acceso al calendario del usuario o enviar un correo electrónico. La aplicación puede solicitar un token de acceso mediante MSAL mediante la especificación de ámbitos de API. Este token de acceso se agrega entonces al encabezado de autorización de HTTP para todas las llamadas efectuadas al recurso protegido.

MSAL administra el almacenamiento en caché y la actualización de los tokens de acceso automáticamente, así que no tiene que preocuparse por ello.


### <a name="nuget-packages"></a>Paquetes NuGet

Esta guía utiliza los siguientes paquetes NuGet:

|Biblioteca|Descripción|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Versión preliminar de Biblioteca de autenticación de Microsoft para iOS|

