---
title: "Autenticación con Data Lake Store mediante Active Directory | Microsoft Docs"
description: "Obtenga información sobre la autenticación con Data Lake Store mediante Active Directory"
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
ms.date: 10/17/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7e7c9fd2fe7e6327cd39c4c26583c8fd556c9044


---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticación de usuario final con Data Lake Store mediante Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticación entre servicios](data-lake-store-authenticate-using-active-directory.md)
> * [Autenticación de usuario final](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store usa Azure Active Directory para la autenticación. Antes de crear una aplicación que funcione con Azure Data Lake Store o Azure Data Lake Analytics, debe determinar cómo desea autenticar la aplicación con Azure Active Directory (Azure AD). Las dos principales opciones disponibles son:

* Autenticación de usuario final. 
* Autenticación entre servicios. 

Con ambas opciones, la aplicación recibe un token de OAuth 2.0 que se adjunta a cada solicitud realizada a Azure Data Lake Store o Azure Data Lake Analytics.

En este artículo se habla de cómo crear una aplicación web de Azure AD para la autenticación de usuario final. Para obtener instrucciones sobre la configuración de la aplicación de Azure AD para la autenticación entre servicios, vea [Service-to-service authentication with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md) (Autenticación entre servicios con Data Lake Store mediante Azure Active Directory).

## <a name="prerequisites"></a>Requisitos previos
* Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/pricing/free-trial/).
* El identificador de suscripción. Puede recuperarlo en Azure Portal. Por ejemplo, está disponible en la hoja de la cuenta de Data Lake Store.
  
    ![Obtener id. de suscripción](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)
* El nombre de dominio de Azure AD. Para recuperarlo, mantenga el puntero del mouse en la esquina superior derecha de Azure Portal. En la siguiente captura de pantalla, el nombre de dominio es **contoso.microsoft.com** y el GUID entre paréntesis es el identificador del inquilino. 
  
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
Su aplicación puede proporcionar directamente las credenciales de usuario a Azure AD. Este método solo funciona con cuentas de usuario de id. organizativo; no es compatible con cuentas de usuario personales o de "live ID", incluidas las que finalizan en @outlook.com o @live.com.. Además, este método no es compatible con cuentas de usuario que requieren autenticación en dos fases de Azure AD (2FA).

### <a name="what-do-i-need-to-use-this-approach"></a>¿Qué se necesita para usar este enfoque?
* El nombre de dominio de Azure AD. Este ya aparece en los requisitos previos de este artículo.
* La **aplicación web**de Azure AD
* El identificador de cliente de la aplicación web de Azure AD
* El URI de respuesta de la aplicación web de Azure AD
* Establecimiento de permisos delegados

Para obtener instrucciones sobre cómo crear un una aplicación web de Azure AD y configurarla según los requisitos mencionados anteriormente, vea a continuación la sección [Creación de una aplicación de Active Directory](#create-an-active-directory-application). 

## <a name="create-an-active-directory-application"></a>Creación de una aplicación de Active Directory
En esta sección aprenderemos a crear y configurar una aplicación web de Azure AD para la autenticación de usuario final con Azure Data Lake Store mediante Azure Active Directory.

### <a name="step-1-create-an-azure-active-directory-application"></a>Paso 1: Crear una aplicación de Azure Active Directory
> [!NOTE]
> En los pasos siguientes se usa Azure Portal. También puede crear una aplicación de Azure AD mediante [Azure PowerShell](../resource-group-authenticate-service-principal.md) o [CLI de Azure](../resource-group-authenticate-service-principal-cli.md).
> 
> 

1. Inicie sesión en su cuenta de Azure a través del [portal clásico](https://manage.windowsazure.com/).
2. Seleccione **Active Directory** en el panel izquierdo.
   
     ![seleccionar Active Directory](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory.png)
3. Seleccione el directorio de Active Directory que desea usar para crear la nueva aplicación. Si tiene más de uno, suele ser preferible crear la aplicación en el directorio en el que reside la suscripción. Solo puede conceder acceso a recursos de la suscripción para aplicaciones que se encuentren en el mismo directorio que su suscripción.  
   
     ![elegir directorio](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory-details.png)
4. Para ver las aplicaciones en su directorio, haga clic en **Aplicaciones**.
   
     ![ver aplicaciones](./media/data-lake-store-end-user-authenticate-using-active-directory/view-applications.png)
5. Si no ha creado una aplicación en ese directorio previamente, debería ver algo similar a la siguiente imagen. Haga clic en **AGREGAR UNA APLICACIÓN**.
   
     ![Agregar una aplicación](./media/data-lake-store-end-user-authenticate-using-active-directory/create-application.png)
   
     O bien, haga clic en **Agregar** en el panel inferior.
   
     ![agregar](./media/data-lake-store-end-user-authenticate-using-active-directory/add-icon.png)
6. Dé un nombre a la aplicación y seleccione el tipo al que esta pertenece. Para este tutorial, cree una **APLICACIÓN WEB Y/O API WEB** y haga clic en el botón Siguiente.
   
     ![aplicación de nombre](./media/data-lake-store-end-user-authenticate-using-active-directory/tell-us-about-your-application.png)
7. Rellene las propiedades de la aplicación. Para **DIRECCIÓN URL DE INICIO DE SESIÓN**, proporcione el identificador URI para un sitio web que describe la aplicación. No se valida la existencia del sitio web. 
   Para **URI DE ID. DE APLICACIÓN**, proporcione el URI que identifica la aplicación.
   
     ![propiedades de la aplicación](./media/data-lake-store-end-user-authenticate-using-active-directory/app-properties.png)
   
    Haga clic en la marca de verificación para finalizar el asistente y crear la aplicación.

### <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>Paso 2: Obtener el identificador del cliente, el URI de respuesta y establecer permisos delegados
1. Haga clic en la pestaña **Configurar** para configurar la contraseña de la aplicación.
   
     ![configurar aplicación](./media/data-lake-store-end-user-authenticate-using-active-directory/application-configure.png)
2. Copie el valor de **ID. DE CLIENTE**.
   
     ![ID. DE CLIENTE](./media/data-lake-store-end-user-authenticate-using-active-directory/client-id.png)
3. En la sección **Inicio de sesión único**, copie el **URI de respuesta**.
   
    ![ID. DE CLIENTE](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-get-reply-uri.png)
4. En **Permisos para otras aplicaciones**, haga clic en **Agregar aplicación**.
   
    ![ID. DE CLIENTE](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)
5. En el asistente para **Permisos para otras aplicaciones**, seleccione **Azure Data Lake** y **Azure Service Management API******, y luego haga clic en la marca de verificación.
6. De forma predeterminada, **Permisos delegados** está establecida en cero para los servicios recién agregados. Haga clic en la lista desplegable **Permisos delegados** para Azure Data Lake y el servicio de administración de Microsoft Azure, y active las casillas disponibles para establecer los valores en 1. El resultado debería tener este aspecto.
   
     ![ID. DE CLIENTE](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
7. Haga clic en **Guardar**.

## <a name="next-steps"></a>Pasos siguientes
En este artículo creó una aplicación web de Azure AD y recopiló la información que necesita en las aplicaciones cliente que cree mediante SDK con .NET, SDK de Java, etc. Ahora puede continuar en los artículos siguientes, que hablan de cómo usar la aplicación web de Azure AD para autenticarse primero en Data Lake Store y luego realizar otras operaciones en el almacén.

* [Introducción al Almacén de Azure Data Lake mediante SDK de .NET](data-lake-store-get-started-net-sdk.md)
* [Introducción a Azure Data Lake Store con el SDK de Java](data-lake-store-get-started-java-sdk.md)




<!--HONumber=Nov16_HO3-->


