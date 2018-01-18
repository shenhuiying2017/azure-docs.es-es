---
title: "Autenticación en Data Lake Store con Azure Active Directory | Microsoft Docs"
description: "Obtenga información sobre la autenticación con Data Lake Store mediante Azure Active Directory"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 7e6e37c0f5c8447b0e6d147a297b460d323b8894
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticación con Data Lake Store mediante Azure Active Directory

Azure Data Lake Store usa Azure Active Directory para la autenticación. Antes de crear una aplicación que funcione con Azure Data Lake Store, debe decidir cómo autenticar la aplicación con Azure Active Directory (Azure AD). 

## <a name="authentication-options"></a>Opciones de autenticación

* **Autenticación de usuario final**: las credenciales de Azure para usuarios finales se utilizan para la autenticación en Data Lake Store. La aplicación que crea para trabajar con Data Lake Store solicita estas credenciales de usuario. Como resultado, este mecanismo de autenticación es *interactivo* y la aplicación se ejecuta en el contexto del usuario que ha iniciado sesión. Para más información e instrucciones, vea la información sobre la [autenticación de usuarios finales en Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Autenticación entre servicios**: utilice esta opción si desea que una aplicación se autentique en Data Lake Store. En tales casos, cree una aplicación de Azure Active Directory (AD) y use la clave de la aplicación de Azure AD para la autenticación en Data Lake Store. Como resultado, este mecanismo de autenticación es *no interactivo*. Para más información e instrucciones, vea la información sobre la [autenticación entre servicios en Data Lake Store](data-lake-store-service-to-service-authenticate-using-active-directory.md).

En la tabla siguiente se indica cómo se admiten los mecanismos de autenticación de usuarios finales y entre servicios en Data Lake Store. Así es como se debe interpretar la tabla.

* El símbolo ✔* indica que la opción de autenticación es compatible y se vincula a un artículo en el que se explica cómo utilizar la opción de autenticación. 
* El símbolo ✔ indica que se admite la opción de autenticación. 
* Las celdas vacías significan que la opción de autenticación no es compatible.


|Use esta opción de autenticación con…                   |.NET         |Java     |PowerShell |CLI 2.0 | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Usuario final (sin MFA**)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)** (en desuso)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Usuario final (con MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Entre servicios (con clave de cliente)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Entre servicios (con certificado de cliente) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Haga clic en el símbolo <b>✔\*</b>. Es un vínculo.</i><br>
<i>** MFA hace referencia a la autenticación multifactor</i>.

Vea [Escenarios de autenticación para Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md) para obtener más información sobre cómo usar Azure Active Directory para la autenticación.

## <a name="next-steps"></a>pasos siguientes

* [Autenticación de usuario final](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Autenticación entre servicios](data-lake-store-service-to-service-authenticate-using-active-directory.md)


