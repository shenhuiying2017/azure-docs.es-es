---
title: 'Inicio rápido: requerir la autenticación multifactor (MFA) para aplicaciones específicas con acceso condicional a Azure Active Directory | Microsoft Docs'
description: En esta guía de inicio rápido obtendrá información sobre cómo se pueden asociar los requisitos de autenticación al tipo de aplicación en la nube a la que se accede mediante el acceso condicional a Azure Active Directory (Azure AD).
services: active-directory
keywords: acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 475b0229b9e627a56b02d2299ee2e5400aa0ede1
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156116"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Inicio rápido: requerir MFA para aplicaciones específicas con acceso condicional a Azure Active Directory 

Para simplificar la experiencia de inicio de sesión de los usuarios, puede permitirles iniciar sesión en sus aplicaciones en la nube mediante un nombre de usuario y una contraseña. Sin embargo, muchos entornos tienen al menos algunas aplicaciones para las que es preferible requerir una forma más segura para verificar la cuenta, como la autenticación multifactor (MFA). Esto podría ser true, por ejemplo, para tener acceso a las aplicaciones de recursos humanos o al sistema de correo electrónico de su organización. En Azure Active Directory (Azure AD), puede lograr este objetivo con una directiva de acceso condicional.    

En esta guía de inicio rápido se muestra cómo configurar una [directiva de acceso condicional a Azure AD](active-directory-conditional-access-azure-portal.md) que requiere la autenticación multifactor para una aplicación en la nube específica en su entorno.

![Creación de directiva](./media/active-directory-conditional-access-app-based-mfa/32.png)


Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.



## <a name="prerequisites"></a>requisitos previos 

Para completar el escenario en este inicio rápido, necesita:

- **Acceso a una edición de Azure AD Premium**: el acceso condicional a Azure AD es una funcionalidad de Azure AD Premium. 

- **Una cuenta de prueba denominada Isabella Simonsen**: si no sabe cómo crear una cuenta de prueba, consulte [Agregar usuarios basados en la nube](add-users-azure-active-directory.md#add-cloud-based-users).



## <a name="create-your-conditional-access-policy"></a>Creación de la directiva de acceso condicional 

En esta sección se muestra cómo crear la directiva de acceso condicional que se requiere. En el escenario de esta guía de inicio rápido se usa:

- Azure Portal como marcador de posición para una aplicación en la nube que requiere MFA. 
- El usuario de ejemplo para probar la directiva de acceso condicional.  

En la directiva, establezca:

|Configuración |Valor|
|---     | --- |
|Usuarios y grupos | Isabella Simonsen |
|Aplicaciones de nube | Microsoft Azure Management (Administración de Microsoft Azure) |
|Conceder acceso | Requerir autenticación multifactor |
 

![Creación de directiva](./media/active-directory-conditional-access-app-based-mfa/31.png)

 


**Para configurar la directiva de acceso condicional:**

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global.

2. En Azure Portal, en la barra de navegación izquierda, haga clic en **Azure Active Directory**. 

    ![Azure Active Directory](./media/active-directory-conditional-access-app-based-mfa/02.png)

3. En la página **Azure Active Directory**, en la sección **Administrar**, haga clic en **Acceso condicional**.

    ![Acceso condicional](./media/active-directory-conditional-access-app-based-mfa/03.png)
 
4. En la página **Acceso condicional**, en la barra de herramientas de la parte superior, haga clic en **Agregar**.

    ![Sumar](./media/active-directory-conditional-access-app-based-mfa/04.png)

5. En la página **Nuevo**, en el cuadro de texto **Nombre**, escriba **Requerir MFA para obtener acceso a Azure Portal**.

    ![NOMBRE](./media/active-directory-conditional-access-app-based-mfa/05.png)

6. En la sección **Asignación**, haga clic en **Usuarios y grupos**.

    ![Usuarios y grupos](./media/active-directory-conditional-access-app-based-mfa/06.png)

7. En la página **Usuarios y grupos**, siga estos pasos:

    ![Usuarios y grupos](./media/active-directory-conditional-access-app-based-mfa/24.png)

    a. Haga clic en **Seleccionar usuarios y grupos** y, luego, seleccione **Usuarios y grupos**.

    b. Haga clic en **Seleccionar**.

    c. En la página **Seleccionar**, seleccione **Isabella Simonsen** y, luego, haga clic en **Seleccionar**.

    d. En la página **Usuarios y grupos**, haga clic en **Listo**.

8. Haga clic en **Aplicaciones en la nube**.

    ![Aplicaciones de nube](./media/active-directory-conditional-access-app-based-mfa/08.png)

9. En la página **Aplicaciones en la nube**, siga estos pasos:

    ![Seleccionar aplicaciones en la nube](./media/active-directory-conditional-access-app-based-mfa/26.png)

    a. Haga clic en **Seleccionar aplicaciones**.

    b. Haga clic en **Seleccionar**.

    c. En la página **Seleccionar**, seleccione **Microsoft Azure Management** (Administración de Microsoft Azure) y, luego, haga clic en **Seleccionar**.

    d. En la página **Aplicaciones en la nube**, haga clic en **Listo**.


10. En la sección **Controles de acceso**, haga clic en **Conceder**.

    ![Controles de acceso](./media/active-directory-conditional-access-app-based-mfa/10.png)

11. En la página **Conceder**, siga estos pasos:

    ![Conceder](./media/active-directory-conditional-access-app-based-mfa/11.png)

    a. Seleccione **Conceder acceso**.

    a. Seleccione **Requerir autenticación multifactor**.

    b. Haga clic en **Seleccionar**.

12. En la sección **Habilitar directiva**, haga clic en **Activar**.

    ![Habilitar directiva](./media/active-directory-conditional-access-app-based-mfa/18.png)

13. Haga clic en **Create**(Crear).


## <a name="evaluate-a-simulated-sign-in"></a>Evaluación de un inicio de sesión simulado

Ahora que ha configurado la directiva de acceso condicional, probablemente quiere saber si funciona según lo previsto. Como primer paso, use la herramienta de directivas What If del acceso condicional para simular un inicio de sesión del usuario de prueba. La simulación calcula el impacto que este inicio de sesión tiene en las directivas y genera un informe de simulación.  

Para inicializar la herramienta de evaluación de directivas What If, establezca:

- **Isabella Simonsen** como usuario 
- **Microsoft Azure Management** (Administración de Microsoft Azure) como aplicación en la nube

 Al hacer clic en **What If**, se crea un informe de simulación que muestra:

- **Requerir MFA para obtener acceso a Azure Portal** en **Directivas que se aplicarán** 
- **Requerir autenticación multifactor** para **Conceder controles**.

![Herramienta de directivas What If](./media/active-directory-conditional-access-app-based-mfa/23.png)



**Para evaluar la directiva de acceso condicional:**

1. En la página [Acceso condicional - Directivas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies), en la barra de herramientas de la parte superior, haga clic en **What If**.  
 
    ![What If](./media/active-directory-conditional-access-app-based-mfa/14.png)

2. Haga clic en **Usuarios**, seleccione **Isabella Simonsen** y, luego, haga clic en **Seleccionar**.

    ![Usuario](./media/active-directory-conditional-access-app-based-mfa/15.png)

2. Para seleccionar una aplicación en la nube, siga los pasos siguientes:

    ![Aplicaciones de nube](./media/active-directory-conditional-access-app-based-mfa/16.png)

    a. Haga clic en **Aplicaciones en la nube**.

    b. En la **página Aplicaciones en la nube**, haga clic en **Seleccionar aplicaciones**.

    c. Haga clic en **Seleccionar**.

    d. En la página **Seleccionar**, seleccione Microsoft Azure Management** (Administración de Microsoft Azure**) y, luego, haga clic en **Seleccionar**.

    e. En la página Aplicaciones en la nube, haga clic en **Listo**.

3. Haga clic en **What If**.


## <a name="test-your-conditional-access-policy"></a>Prueba de la directiva de acceso condicional

En la sección anterior, ha aprendido a evaluar un inicio de sesión de simulado. Además de una simulación, también debe probar la directiva de acceso condicional para asegurarse de que funciona según lo previsto. 

Para probar la directiva, intente iniciar sesión en [Azure Portal](https://portal.azure.com) con su cuenta de prueba **Isabella Simonsen**. Debería ver un cuadro de diálogo que le requiera establecer la cuenta para una verificación de seguridad adicional.

![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/22.png)


## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sea necesario, elimine el usuario de prueba y la directiva de acceso condicional:

- Si no sabe cómo eliminar un usuario de Azure AD, consulte [Eliminación de usuarios desde Azure AD](add-users-azure-active-directory.md#delete-users-from-azure-ad).

- Para eliminar la directiva, selecciónela y, a continuación, haga clic en **Eliminar** en la barra de herramientas de acceso rápido.

    ![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/33.png)


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el acceso condicional, consulte [Acceso condicional de Azure Active Directory](active-directory-conditional-access-azure-portal.md).

