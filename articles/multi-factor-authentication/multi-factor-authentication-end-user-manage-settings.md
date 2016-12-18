---
title: "Administrar la configuración de verificación en dos pasos | Microsoft Docs"
description: "Administre cómo usar Azure Multi-Factor Authentication, incluida la modificación de la información de contacto o la configuración de los dispositivos."
services: multi-factor-authentication
keywords: "cliente de multi-factor authentication, problema de autenticación, identificador de correlación"
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 830eb6627cae71f358b9790791b1d86f7c82c566
ms.openlocfilehash: 8156a7e9d9cbe1631c2e777454d04519b413d551


---
# <a name="manage-your-settings-for-two-step-verification"></a>Administración de la configuración de la comprobación en dos pasos
Este artículo ofrece respuestas a preguntas acerca de cómo actualizar la configuración de actualización en dos pasos o la autenticación multifactor. Si tiene problemas para iniciar sesión en su cuenta, consulte [Problemas con la comprobación en dos pasos](multi-factor-authentication-end-user-troubleshoot.md) para obtener ayuda para la solución de problemas.

## <a name="where-to-find-the-settings-page"></a>Dónde encontrar la página de configuración
Según cómo su compañía configure Azure Multi-Factor Authentication, existen algunos lugares donde puede cambiar los ajustes, como su número de teléfono.

Si el administrador de TI envía una dirección URL específica o pasos para administrar la comprobación en dos pasos, siga las instrucciones. De lo contrario, las instrucciones siguientes deben funcionar para los demás. Si se siguen estos pasos, pero no ve las mismas opciones, significa que su trabajo o escuela personalizó su propio portal. Consulte al administrador para obtener el vínculo a su portal de Azure Multi-Factor Authentication.

1. Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com)  
2. En la parte superior, seleccione el **perfil**.  
3. Seleccione **Comprobación de seguridad adicional**.  

    ![MyApps](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. La página de comprobación de seguridad adicional se carga con la configuración.

    ![Página de proofup](./media/multi-factor-authentication-end-user-manage/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Deseo cambiar mi número de teléfono o agregar un número secundario
Es importante configurar un número de teléfono de autenticación secundario.  Debido a que el número de teléfono principal y la aplicación móvil probablemente se encuentran en el mismo teléfono, el número de teléfono secundario es la única forma que tiene para volver a tener acceso a su cuenta si se le pierde el teléfono o si se lo robaron.

> [!NOTE]
> Si no dispone de acceso a su número de teléfono principal y necesita ayuda para encontrar su cuenta, vea nuestros temas de ayuda [Problemas con la comprobación en dos pasos](multi-factor-authentication-end-user-troubleshoot.md).
>
>

**Para cambiar el número de teléfono principal:**  

1. En la página de comprobación de seguridad adicional, seleccione el cuadro de texto con el número de teléfono actual y modifíquelo con el nuevo número de teléfono.  
2. Seleccione **Guardar**.  
3. Si este es el número que se utiliza para la opción de comprobación preferida, tiene que comprobar el nuevo número para poder guardarlo.  

**Para agregar un número de teléfono secundario:**  

1. En la página de comprobación de seguridad adicional, active la casilla junto al **teléfono de autenticación alternativo.**  
2. En el cuadro de texto, escriba el número de teléfono secundario.  
3. Seleccione **Guardar** y los cambios habrán finalizado.  

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>¿Cómo puedo quitar Microsoft Authenticator del dispositivo anterior y moverlo a uno nuevo?
Cuando desinstala la aplicación del dispositivo o restablece el dispositivo, no se quita la activación en el back-end. Para más información, vea [Microsoft Authenticator](multi-factor-authentication-microsoft-authenticator.md).

## <a name="next-steps"></a>Pasos siguientes
* Obtener sugerencias para solucionar problemas y necesita ayuda [Problemas con la comprobación en dos pasos](multi-factor-authentication-end-user-troubleshoot.md)
* Configure [contraseñas de aplicación](multi-factor-authentication-end-user-app-passwords.md) para las aplicaciones que no admiten la comprobación en dos pasos.



<!--HONumber=Nov16_HO3-->


