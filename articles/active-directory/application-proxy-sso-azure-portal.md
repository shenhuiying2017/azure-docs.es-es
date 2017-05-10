---
title: "Inicio de sesión único en aplicaciones mediante el proxy de aplicación de Azure AD | Microsoft Docs"
description: "Active el inicio de sesión único para sus aplicaciones locales publicadas mediante el proxy de aplicación de Azure AD en Azure Portal."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: asteen
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: b0454ad51a2d0edecb6f7e7748ec3b74eeefa1da
ms.contentlocale: es-es
ms.lasthandoff: 04/28/2017

---


# <a name="provide-single-sign-on-with-azure-ad-application-proxy---public-preview"></a>Provisión de inicio de sesión único mediante el proxy de aplicación de Azure AD: versión preliminar pública

El proxy de aplicación de Azure Active Directory le ayuda a mejorar la productividad mediante la publicación de aplicaciones locales de manera que los empleados remotos puedan tener acceso seguro a estas. En Azure Portal, puede configurar también el inicio de sesión único (SSO) para estas aplicaciones. Ahora, los usuarios solo necesitan autenticarse con Azure AD y podrán acceder a la aplicación de empresa sin necesidad de volver a iniciar sesión.

En este artículo, vamos a usar el ejemplo de una aplicación basada en contraseña para mostrar cómo el almacenamiento de contraseñas proporciona una experiencia sencilla de SSO. 

Debe haber publicado y probado ya la aplicación con el proxy de aplicación. Si no es así, siga los pasos de [Publish applications using Azure AD Application Proxy - Public Preview](application-proxy-publish-azure-portal.md) (Publicación de aplicaciones mediante el proxy de aplicación de Azure AD: versión preliminar pública) y luego vuelva a este punto. 

O bien, si no está familiarizado con el proxy de aplicación, obtenga más información sobre esta característica con el artículo [Provisión de acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md).

## <a name="set-up-password-vaulting-for-your-application"></a>Configuración del almacenamiento de contraseñas para la aplicación

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
2. Seleccione **Azure Active Directory** > **Aplicaciones empresariales** > **Todas las aplicaciones**.
3. En la lista, seleccione la aplicación para la que desea configurar el SSO. Si tiene una gran cantidad de aplicaciones, puede usar el cuadro de búsqueda para filtrarlas.  
4. En la sección Administrar, seleccione **Inicio de sesión único**.

   ![Seleccionar inicio de sesión único](./media/application-proxy-sso-azure-portal/select-sso.png)

5. Para el modo SSO, elija **Inicio de sesión basado en contraseña**.
6. Para la URL de inicio de sesión, escriba la dirección URL de la página donde los usuarios escriben su nombre de usuario y contraseña para iniciar sesión en la aplicación. Debe ser la dirección URL externa que creó al publicar la aplicación a través del proxy de aplicación. 

   ![Elegir inicio de sesión basado en contraseña y escribir la URL](./media/application-proxy-sso-azure-portal/password-sso.png)

7. Seleccione **Guardar**.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Prueba de la aplicación

Vaya al [sitio MyApps](https://myapps.microsoft.com) y seleccione la aplicación que acaba de configurar. Inicie sesión con sus credenciales para esa aplicación (o las credenciales para la cuenta de prueba que configuró con acceso). Una vez que inicie sesión correctamente, debería poder salir de la aplicación y volver sin necesidad de volver a especificar sus credenciales. 

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre otras maneras de implementar el [inicio de sesión único con el proxy de aplicación](active-directory-application-proxy-sso-using-kcd.md)

