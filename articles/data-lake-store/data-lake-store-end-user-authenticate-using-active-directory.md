---
title: "Autenticación de usuario final: Data Lake Store con Azure Active Directory | Microsoft Docs"
description: "Aprenda a lograr la autenticación del usuario final con Data Lake Store mediante Azure Active Directory"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ec586ecd-1b42-459e-b600-fadbb7b80a9b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 0f6af54b351235390afa88f1ce156abd839a723f
ms.lasthandoff: 03/03/2017


---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticación de usuario final con Data Lake Store mediante Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticación entre servicios](data-lake-store-authenticate-using-active-directory.md)
> * [Autenticación de usuario final](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store usa Azure Active Directory para la autenticación. Antes de crear una aplicación que funcione con Azure Data Lake Store o Azure Data Lake Analytics, debe determinar cómo desea autenticar la aplicación con Azure Active Directory (Azure AD). Las dos principales opciones disponibles son:

* Autenticación de usuario final (este artículo)
* Autenticación entre servicios

Con ambas opciones, la aplicación recibe un token de OAuth 2.0 que se adjunta a cada solicitud realizada a Azure Data Lake Store o Azure Data Lake Analytics.

En este artículo se habla de cómo crear una **aplicación nativa de Azure AD para la autenticación de usuario final**. Para obtener instrucciones sobre la configuración de la aplicación de Azure AD para la autenticación entre servicios, vea [Service-to-service authentication with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md) (Autenticación entre servicios con Data Lake Store mediante Azure Active Directory).

## <a name="prerequisites"></a>Requisitos previos
* Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/pricing/free-trial/).

* El identificador de suscripción. Puede recuperarlo en Azure Portal. Por ejemplo, está disponible en la hoja de la cuenta de Data Lake Store.
  
    ![Obtener id. de suscripción](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* El nombre de dominio de Azure AD. Para recuperarlo, mantenga el puntero del mouse en la esquina superior derecha de Azure Portal. En la siguiente captura de pantalla, el nombre de dominio es **contoso.onmicrosoft.com** y el GUID entre paréntesis es el identificador del inquilino. 
  
    ![Obtener dominio de AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>Autenticación de usuario final
Es el enfoque recomendado si desea que un usuario final inicie sesión en la aplicación a través de Azure AD. La aplicación podrá tener acceso a los recursos de Azure con el mismo nivel de acceso que tiene el usuario final que inició sesión. El usuario final deberá proporcionar sus credenciales de manera periódica para que la aplicación conserve el acceso.

El inicio de sesión del usuario final genera que su aplicación reciba un token de acceso y un token de actualización. El token de acceso se adjunta a cada solicitud hecha a Data Lake Store o Data Lake Analytics y es válido, de manera predeterminada, durante 1 hora. El token de actualización se puede usar para obtener un nuevo token de acceso y es válido, de manera predeterminada, hasta por dos semanas, si se usa de manera habitual. Puede usar dos enfoques distintos para el inicio de sesión del usuario final.

### <a name="using-the-oauth-20-pop-up"></a>Uso de la ventana emergente de OAuth 2.0
La aplicación puede desencadenar una ventana emergente de autorización de OAuth 2.0 en la que el usuario final puede escribir sus credenciales. Esta ventana emergente también funciona con el proceso de autenticación en dos pasos (2FA) de Azure AD, si es necesario. 

> [!NOTE]
> Este método todavía no es compatible con la Biblioteca de autenticación de Active Directory (ADAL) para Python o Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Transmisión directa de credenciales de usuario
Su aplicación puede proporcionar directamente las credenciales de usuario a Azure AD. Este método solo funciona con cuentas de usuario con identificador de organización; no es compatible con cuentas de usuario personales de tipo "Live ID", incluidas las que terminan en @outlook.com o @live.com. Además, este método no es compatible con las cuentas de usuario que requieren la autenticación en dos pasos (2FA) de Azure AD.

### <a name="what-do-i-need-to-use-this-approach"></a>¿Qué se necesita para usar este enfoque?
* El nombre de dominio de Azure AD. Este ya aparece en los requisitos previos de este artículo.
* **Aplicación nativa** de Azure AD
* El identificador de cliente para la aplicación nativa de Azure AD
* URI de respuesta para la aplicación nativa de Azure AD
* Establecimiento de permisos delegados


## <a name="step-1-create-an-active-directory-web-application"></a>Paso 1: Crear una aplicación web de Active Directory

Cree y configure una aplicación nativa de Azure AD para la autenticación de usuario final con Azure Data Lake Store mediante Azure Active Directory. Para obtener instrucciones, vea cómo [crear una aplicación de Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Al seguir las instrucciones que aparecen en el vínculo anterior, asegúrese de seleccionar **Nativa** para el tipo de aplicación, como se muestra en la captura de pantalla siguiente.

![Crear aplicación de web](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Crear aplicación nativa")

## <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>Paso 2: Obtener el identificador del cliente, el URI de respuesta y establecer permisos delegados

Consulte [Obtención del identificador del cliente](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) para recuperar el identificador del cliente (también llamado identificador de la aplicación) de la aplicación nativa de Azure AD.

Para recuperar el URI de redireccionamiento, siga estos pasos.

1. En Azure Portal, seleccione **Azure Active Directory**, haga clic en **Registros de aplicaciones**y después busque y haga clic en la aplicación nativa de Azure AD que acaba de crear.

2. En la hoja **Configuración** de la aplicación, haga clic en **URI de redirección**.

    ![Obtener URI de redirección](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Copie el valor mostrado.


## <a name="step-3-set-permissions"></a>Paso 3: Establecer permisos

1. En Azure Portal, seleccione **Azure Active Directory**, haga clic en **Registros de aplicaciones**y después busque y haga clic en la aplicación nativa de Azure AD que acaba de crear.

2. En la hoja **Configuración** de la aplicación, haga clic en **Permisos necesarios** y después en **Agregar**.

    ![ID. DE CLIENTE](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. En la hoja **Agregar acceso de API**, haga clic en **Seleccionar una API**, después en **Azure Data Lake** y, por último, en **Seleccionar**.

    ![ID. DE CLIENTE](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  En la hoja **Agregar acceso de API**, haga clic en **Seleccionar permisos**, active la casilla de verificación para conceder **acceso total a Data Lake Store** y después haga clic en **Seleccionar**.

    ![ID. DE CLIENTE](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Haga clic en **Done**.

5. Repita los dos últimos pasos para conceder permisos también para **Service Management API de Windows Azure**.
   
## <a name="next-steps"></a>Pasos siguientes
En este artículo creó una aplicación web de Azure AD y recopiló la información que necesita en las aplicaciones cliente que cree mediante SDK con .NET, SDK de Java, etc. Ahora puede continuar en los artículos siguientes, que hablan de cómo usar la aplicación web de Azure AD para autenticarse primero en Data Lake Store y luego realizar otras operaciones en el almacén.

* [Introducción al Almacén de Azure Data Lake mediante SDK de .NET](data-lake-store-get-started-net-sdk.md)
* [Introducción a Azure Data Lake Store con el SDK de Java](data-lake-store-get-started-java-sdk.md)


