---
title: "Tutorial: Integración de Azure Active Directory con UserEcho | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y UserEcho."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: c6ea5ae5f51ac5584840a18b2d995f52297ebcf9
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Tutorial: integración de Azure Active Directory con UserEcho
El objetivo de este tutorial es mostrar cómo integrar UserEcho con Azure Active Directory (Azure AD).

Integrar UserEcho con Azure AD proporciona las siguientes ventajas: 

* Puede controlar en Azure AD quién tiene acceso a UserEcho. 
* Puede permitir que los usuarios inicien sesión automáticamente en UserEcho (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con UserEcho, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en UserEcho

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.  

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de UserEcho desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-userecho-from-the-gallery"></a>Adición de UserEcho desde la galería
Para configurar la integración de UserEcho en Azure AD, deberá agregar UserEcho desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar UserEcho desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **UserEcho**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_01.png)
7. En el panel de resultados, seleccione **UserEcho** y haga clic en **Completa**r para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con UserEcho con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de UserEcho para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de UserEcho.  

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en UserEcho.

Para configurar y probar el inicio de sesión único de Azure AD con UserEcho, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de UserEcho](#creating-a-userecho-test-user)** : para tener un homólogo de Britta Simon en UserEcho que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurarlo en la aplicación UserEcho. 

**Para configurar el inicio de sesión único de Azure AD con UserEcho, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **UserEcho**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en UserEcho?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_03.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_04.png) 
  1. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL utilizada por los usuarios para iniciar sesión en su aplicación de UserEcho (p. ej.: *https://fabrikam.UserEcho.com/*).
  2. Haga clic en **Siguiente**.
4. En la página **Configurar inicio de sesión único en UserEcho** , siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_05.png) 
  1. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
  2. Haga clic en **Siguiente**.
5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de UserEcho como administrador.
6. En la barra de herramientas de la parte superior, haga clic en el nombre de usuario para expandir el menú y, después, haga clic en **Instalación**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 
7. Haga clic en **Integraciones**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) 
8. Haga clic en **Sitio web** y, después, en **Inicio de sesión único (SAML2)**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) 
9. Siga estos pasos en la página **Inicio de sesión único (SAML)** :
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png) 
  1. En **Habilitado para SAML**, seleccione **Sí**. 
  2. En el Portal de Azure clásico, en la página de diálogo Configurar inicio de sesión único en UserEcho, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión único de SAML**.
  3. En el Portal de Azure clásico, en la página de diálogo Configurar inicio de sesión único en UserEcho, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión remoto**. 
  4. Abra el certificado descargado en el Bloc de notas, copie el contenido y luego péguelo en el cuadro de texto **Certificado X.509** .    
  5. Haga clic en **Guardar**.
10. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. 
    
     ![Inicio de sesión único de Azure AD ][10]
11. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
    
     ![Inicio de sesión único de Azure AD ][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_09.png)  
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes: 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_05.png)  
  1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
  2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
  3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes: 
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_06.png) 
  1. En el cuadro de texto **Nombre**, escriba **Britta**.  
  2. En el cuadro de texto **Apellidos**, escriba **Simon**.
  3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  4. En la lista **Rol**, seleccione **Usuario**.
  5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_08.png) 
  1. Anote el valor del campo **Nueva contraseña**.
  2. Haga clic en **Completo**.   

### <a name="create-a-userecho-test-user"></a>Creación de un usuario de prueba de UserEcho
El objetivo de esta sección es crear una usuaria de prueba llamada Britta Simon en UserEcho.

**Para crear una usuaria llamada Britta Simon en UserEcho, realice los pasos siguientes:**

1. Inicie sesión en el sitio de compañía de UserEcho como administrador.
2. En la barra de herramientas de la parte superior, haga clic en el nombre de usuario para expandir el menú y, después, haga clic en **Instalación**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 
3. Haga clic en **Usuarios** para expandir la sección **Usuarios**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png) 
4. Haga clic en **Usuarios**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png) 
5. Haga clic en **Invitar a un nuevo usuario**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png)
6. En el cuadro de diálogo **Invitar a un nuevo usuario** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png) 
  1. En el cuadro de texto **Nombre**, escriba **Britta Simon**.
  2. En el cuadro de texto **Correo electrónico** , escriba la dirección de correo electrónico de Britta en el Portal de Azure clásico.
  3. Haga clic en **Invitar**.

Se envía una invitación a Britta que le permite empezar a usar UserEcho. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo cual se le concederá acceso a UserEcho.

![Asignar usuario][200] 

**Para asignar a Britta Simon a UserEcho, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** , que se encuentra en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **UserEcho**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  

Al hacer clic en el icono de UserEcho en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación UserEcho.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_205.png







