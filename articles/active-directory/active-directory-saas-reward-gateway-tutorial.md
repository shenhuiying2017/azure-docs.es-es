---
title: "Tutorial: Integración de Azure Active Directory con Reward Gateway | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Reward Gateway."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 34336386-998a-4d47-ab55-721d97708e5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 4d4cc0c13fb0d05ecad6c81c4f7b704fa3152ed5
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-reward-gateway"></a>Tutorial: Integración de Azure Active Directory con Reward Gateway
En este tutorial, aprenderá a integrar Reward Gateway con Azure Active Directory (Azure AD).

Integrar Reward Gateway con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Reward Gateway.
* Puede permitir que los usuarios inicien sesión automáticamente en Reward Gateway (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Reward Gateway, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en Reward Gateway

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Reward Gateway desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-reward-gateway-from-the-gallery"></a>Adición de Reward Gateway desde la galería
Para configurar la integración de Reward Gateway en Azure AD, deberá agregar Reward Gateway desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Reward Gateway desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Reward Gateway**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_01.png)
7. En el panel de resultados, seleccione **Reward Gateway** y haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Reward Gateway con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Reward Gateway para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Reward Gateway.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Reward Gateway.

Para configurar y probar el inicio de sesión único de Azure AD con Reward Gateway, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Reward Gateway](#creating-a-reward-gateway-test-user)**: para tener un homólogo de Britta Simon en Reward Gateway que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Reward Gateway.

**Para configurar el inicio de sesión único de Azure AD con Reward Gateway, siga estos pasos:**

1. En el Portal clásico, en la página de integración de aplicaciones de **Reward Gateway**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en Reward Gateway?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_03.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_04.png) 
  1. En el cuadro de texto **URL del identificador**, escriba la dirección URL que usan los usuarios para iniciar sesión en la aplicación de Reward Gateway con el siguiente patrón: 
   
    | URL del identificador |
    | --- |
    | `https://<company name>.rewardgateway.com/` |
    | `https://<company name>.rewardgateway.co.uk/` |
    | `https://<company name>.rewardgateway.co.nz/` |
    | `https://<company name>.rewardgateway.com.au/` |
  2. En el cuadro de texto **URL de respuesta**, escriba la dirección URL con el siguiente patrón: 

    | URL de respuesta |
    | --- |
    | `https://<company name>.rewardgateway.com/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<company name>.rewardgateway.co.uk/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<company name>.rewardgateway.co.nz/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<company name>.rewardgateway.com.au/Authentication/EndLogin?idp=<Unique Id>` |

  3. Haga clic en **Siguiente**.

4. En la página **Configurar inicio de sesión único en Reward Gateway**, siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_05.png)
  * Haga clic en **Descargar metadatos**y luego guarde el archivo en el equipo.
5. Para configurar el inicio de sesión único para su aplicación, póngase en contacto con el [equipo de soporte técnico](mailTo:clientsupport@rewardgateway.com) de Reward Gateway y proporcione lo siguiente:
   
    • Los **metadatos**
6. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
6. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_05.png)    
  1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
  2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
  3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:

   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_06.png)    
  1. En el cuadro de texto **Nombre**, escriba **Britta**.   
  2. En el cuadro de texto **Apellidos**, escriba **Simon**. 
  3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  4. En la lista **Rol**, seleccione **Usuario**.
  5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_08.png) 
  1. Anote el valor del campo **Nueva contraseña**. 
  2. Haga clic en **Completo**.   

### <a name="create-an-reward-gateway-test-user"></a>Creación de un usuario de prueba de Reward Gateway
En esta sección, creará un usuario denominado Britta Simon en Reward Gateway. Trabaje con el [equipo de soporte técnico](mailTo:clientsupport@rewardgateway.com) de Reward Gateway para agregar los usuarios a la plataforma de Reward Gateway.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, se habilita que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Reward Gateway.

![Asignar usuario][200] 

**Para asignar Britta Simon a Reward Gateway, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **Reward Gateway**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Reward Gateway en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Reward Gateway.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_205.png

