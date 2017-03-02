---
title: "Tutorial: Integración de Azure Active Directory con Bonus.ly | Microsoft Docs"
description: "Aprenda cómo usar Bonus.ly con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2c45278318c154051469b4e4c9e4e7b63463ff1e
ms.openlocfilehash: a527286cf3d51263faf67a59ed6efeeb62b05b9d
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Integración de Azure Active Directory con Bonus.ly
El objetivo de este tutorial es mostrar la integración de Azure y Bonus.ly. 

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de prueba en Bonus.ly

La situación descrita en este tutorial consta de los siguientes bloques de creación:

* Habilitación de la integración de aplicaciones para Bonus.ly
* Configuración del inicio de sesión único (SSO)
* Configuración del aprovisionamiento de usuario
* Asignación de usuarios

![Escenario](./media/active-directory-saas-bonus-tutorial/IC773679.png "Escenario")

## <a name="enable-the-application-integration-for-bonusly"></a>Habilitación de la integración de aplicaciones para Bonus.ly
El objetivo de esta sección es describir cómo se habilita la integración de las aplicaciones para Bonus.ly.

**Siga estos pasos para habilitar la integración de aplicaciones para Bonus.ly:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Habilitar inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC773680.png "Habilitar inicio de sesión único")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-bonus-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-bonus-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-bonus-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **Bonus.ly**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-bonus-tutorial/IC773681.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **Bonus.ly** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
   
## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Bonus.ly con su cuenta de Azure AD mediante la federación basada en el protocolo SAML.  

La configuración de un inicio de sesión único para Bonus.ly requiere la recuperación de un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

**Siga estos pasos para configurar el inicio de sesión único:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Bonus.ly**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC749323.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Bonus.ly?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC773683.png "Configurar inicio de sesión único")
*3. En la página **Configurar dirección URL** de la aplicación, en el cuadro de texto **Dirección URL de inquilino de Bonus.ly**, escriba su dirección URL con el patrón siguiente **"https://\<nombre-de-inquilino\>.Bonus.ly"** y, después, haga clic en **Siguiente**: 
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-bonus-tutorial/IC773684.png "Configurar dirección URL de la aplicación")
4. En la página **Configurar inicio de sesión único en Bonus.ly**, haga clic en **Descargar certificado** y, después, guarde el archivo de certificado de forma local en el equipo como **c:\\Bonusly.cer**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC773685.png "Configurar inicio de sesión único")
5. En una ventana de explorador web diferente, inicie sesión en su inquilino de **Bonus.ly** .
6. En la barra de herramientas de la parte superior, haga clic en **Configuración** y seleccione **Integraciones y aplicaciones**.
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")
7. En **Inicio de sesión único**, seleccione **SAML**.
8. En la página de diálogo **SAML** , realice los pasos siguientes:
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")   
   1. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Bonus.ly**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **URL de destino de SSO de IdP**.
   2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Bonus.ly**, copie el valor de **Id. del emisor** y péguelo en el cuadro de texto **Emisor de IdP**.
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Bonus.ly**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **URL de inicio de sesión de IdP**.
   4. Copie el valor de **Huella digital** del certificado exportado y, después, péguelo en el cuadro de texto **Huella digital del certificado**.
   
    >[!TIP]
    >Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).
    > 
9. Haga clic en **guardar**.
10. En el Portal de Microsoft Azure clásico, seleccione la confirmación de configuración y, después, haga clic en **Completar** para cerrar el diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC773689.png "Configurar inicio de sesión único")
    
## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

Para permitir que los usuarios de Azure AD inicien sesión en Bonus.ly, tienen que aprovisionarse en Bonus.ly. 

* En el caso de Bonus.ly, el aprovisionamiento es una tarea manual.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. En una ventana de explorador web diferente, inicie sesión en su inquilino de Bonus.ly.
2. Haga clic en **Configuración**.
 
   ![Configuración](./media/active-directory-saas-bonus-tutorial/IC781041.png "Configuración")
3. Haga clic en la pestaña **Usuarios y bonificaciones** .
   
   ![Usuarios y bonificaciones](./media/active-directory-saas-bonus-tutorial/IC781042.png "Usuarios y bonificaciones")
4. Haga clic en **Administrar usuarios**.
   
   ![Administración de usuarios](./media/active-directory-saas-bonus-tutorial/IC781043.png "Administración de usuarios")
5. Haga clic en **Agregar usuario**.
   
   ![Agregar usuario](./media/active-directory-saas-bonus-tutorial/IC781044.png "Agregar usuario")
6. En el cuadro de diálogo **Agregar usuario** , realice los pasos siguientes:
   
   ![Agregar usuario](./media/active-directory-saas-bonus-tutorial/IC781045.png "Agregar usuario")  
   1. Escriba el “**Correo electrónico**, el **Nombre** y los **Apellidos**” de una cuenta de AAD válida que quiera aprovisionar en los cuadros de texto correspondientes.
   2. Haga clic en **Guardar**.
   
     >[!NOTE]
     >El titular de la cuenta de AAD recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.
     >  

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Bonus.ly que proporcione Bonus.ly para aprovisionar cuentas de usuario de AAD.
>  

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a Bonus.ly, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de Bonus.ly, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-bonus-tutorial/IC773690.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-bonus-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


