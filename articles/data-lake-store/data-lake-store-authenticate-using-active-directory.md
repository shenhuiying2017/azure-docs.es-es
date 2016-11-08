---
title: Autenticación con Data Lake Store mediante Active Directory | Microsoft Docs
description: Obtenga información sobre la autenticación con Data Lake Store mediante Active Directory
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: nitinme

---
# <a name="authenticate-with-data-lake-store-using-azure-active-directory"></a>Autenticación con Data Lake Store mediante Azure Active Directory
Azure Data Lake Store usa Azure Active Directory para la autenticación. Antes de crear una aplicación que funcione con Azure Data Lake Store o Azure Data Lake Analytics, debe determinar cómo desea autenticar la aplicación con Azure Active Directory (Azure AD). Las dos principales opciones disponibles son:

* Autenticación de usuario final. 
* Autenticación entre servicios. 

Con ambas opciones, la aplicación recibe un token de OAuth 2.0 que se adjunta a cada solicitud realizada a Azure Data Lake Store o Azure Data Lake Analytics.

## <a name="prerequisites"></a>Requisitos previos
* Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/pricing/free-trial/).
* El identificador de suscripción. Puede recuperarlo en Azure Portal. Por ejemplo, está disponible en la hoja de la cuenta de Data Lake Store.
  
    ![Obtener id. de suscripción](./media/data-lake-store-authenticate-using-active-directory/get-subscription-id.png)
* El nombre de dominio de Azure AD. Para recuperarlo, mantenga el puntero del mouse en la esquina superior derecha de Azure Portal. 
  
    ![Obtener dominio de AAD](./media/data-lake-store-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>Autenticación de usuario final
Es el enfoque recomendado si desea que un usuario final inicie sesión en la aplicación a través de Azure AD. La aplicación podrá tener acceso a los recursos de Azure con el mismo nivel de acceso que tiene el usuario final que inició sesión. El usuario final deberá proporcionar sus credenciales de manera periódica para que la aplicación conserve el acceso.

El inicio de sesión del usuario final genera que su aplicación reciba un token de acceso y un token de actualización. El token de acceso se adjunta a cada solicitud hecha a Data Lake Store o Data Lake Analytics y es válido, de manera predeterminada, durante 1 hora. El token de actualización se puede usar para obtener un nuevo token de acceso y es válido, de manera predeterminada, hasta por dos semanas, si se usa de manera habitual. Puede usar dos enfoques distintos para el inicio de sesión del usuario final.

### <a name="using-the-oauth-2.0-pop-up"></a>Uso de la ventana emergente de OAuth 2.0
La aplicación puede desencadenar una ventana emergente de autorización de OAuth 2.0 en la que el usuario final puede escribir sus credenciales. Esta ventana emergente también funciona con el proceso de autenticación en dos pasos (2FA) de Azure AD, si es necesario. 

> [!NOTE]
> Este método todavía no es compatible con la Biblioteca de autenticación de Active Directory (ADAL) para Python o Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Transmisión directa de credenciales de usuario
Su aplicación puede proporcionar directamente las credenciales de usuario a Azure AD. Este método solo funciona con cuentas de usuario de id. organizativo; no es compatible con cuentas de usuario personales o de "live ID", incluidas las que finalizan en @outlook.com o @live.com.. Además, este método no es compatible con cuentas de usuario que requieren autenticación en dos fases de Azure AD (2FA).

### <a name="what-do-i-need-to-use-this-approach?"></a>¿Qué se necesita para usar este enfoque?
* El nombre de dominio de Azure AD (que ya aparece en los requisitos previos de este artículo).
* La **aplicación cliente nativa**de Azure AD. 
* El identificador de cliente para la aplicación cliente nativa de Azure AD.
* El URI de redirección para la aplicación cliente nativa de Azure AD. 

Para instrucciones sobre cómo crear una aplicación de Azure AD y recuperar el identificador de cliente, consulte [Creación de una aplicación de Active Directory](../resource-group-create-service-principal-portal.md#create-an-active-directory-application). 

> [!NOTE]
> Las instrucciones de los vínculos anteriores son para una aplicación web de Azure AD. Sin embargo, los pasos son exactamente iguales, aunque decida crear una aplicación cliente nativa.
> 
> 

## <a name="service-to-service-authentication"></a>Autenticación entre servicios
Es el enfoque recomendado si desea que su aplicación se autentique automáticamente con Azure AD sin la necesidad de que un usuario final proporcione sus credenciales. Su aplicación se podrá autenticar siempre que sus credenciales sean válidas. Se puede personalizar para que sea una cuestión de años.

### <a name="what-do-i-need-to-use-this-approach?"></a>¿Qué se necesita para usar este enfoque?
* El nombre de dominio de Azure AD (que ya aparece en los requisitos previos de este artículo).
* La **aplicación web**de Azure AD.
* El identificador de cliente de la aplicación web de Azure AD.
  
  > [!NOTE]
  > Para instrucciones sobre cómo crear una aplicación de Azure AD y recuperar el identificador de cliente, consulte [Creación de una aplicación de Active Directory](../resource-group-create-service-principal-portal.md#create-an-active-directory-application).
  > 
  > 
* Configure la aplicación web de Azure AD para usar el secreto de cliente o un certificado. Para crear una aplicación web con un certificado, consulte [Creación de una entidad de servicio con certificado](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).
* Habilite el acceso para la aplicación web de Azure AD en el archivo o carpeta de Data Lake Store o en la cuenta de Data Lake Analytics con que desea trabajar. Para instrucciones sobre cómo proporcionar acceso a una aplicación de Azure AD a un archivo o carpeta de Data Lake Store, consulte [Asignación de grupos de seguridad o usuarios como ACL al sistema de archivos de Azure Data Lake Store](data-lake-store-secure-data.md#filepermissions).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Almacén de Azure Data Lake mediante SDK de .NET](data-lake-store-get-started-net-sdk.md)
* [Introducción a Azure Data Lake Store con el SDK de Java](data-lake-store-get-started-java-sdk.md)

<!--HONumber=Oct16_HO2-->


