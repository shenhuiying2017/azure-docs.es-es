---
title: "Tutorial: Integración de Azure Active Directory con 23 Video | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y 23 Video."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5e73dd1d-3995-4a73-b9cf-1b2318d49cb3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a59a0782176f5221bb8b2f590faeee660f4d1101
ms.openlocfilehash: 2ebc4571cb7ff763449f192f5140c79a65d69833
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-23-video"></a>Tutorial: Integración de Azure Active Directory con 23 Video
El objetivo de este tutorial es mostrar cómo integrar 23 Video con Azure Active Directory (Azure AD).

Integrar 23 Video con Azure AD proporciona las siguientes ventajas: 

* Puede controlar en Azure AD quién tiene acceso 23 Video. 
* Puede permitir que los usuarios inicien sesión automáticamente en 23 Video (inicio de sesión único) con sus cuentas de Azure AD.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con 23 Video, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en 23 Video

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de 23 Video desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-23-video-from-the-gallery"></a>Adición de 23 Video desde la galería
Para configurar la integración de 23 Video en Azure AD, deberá agregar 23 Video desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar 23 Video desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **23 Video**.
   
    ![Aplicaciones][5]
7. En el panel de resultados, seleccione **23 Video** y haga clic en **Completar** para agregar la aplicación.
   
    ![Aplicaciones][25]

## <a name="configuring-and-testing-azure-ad-sso"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con 23 Video con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de 23 Video para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de 23 Video.  

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en 23 Video.

Para configurar y probar el inicio de sesión único de Azure AD con 23 Video, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de 23 Video](#creating-a-23-video-test-user)** : para tener un homólogo de Britta Simon en 23 Video que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación 23 Video.

**Para configurar el inicio de sesión único de Azure AD con 23 Video, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **23 Video**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en 23 Video?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][7] 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Inicio de sesión único de Azure AD ][8] 
  1. En el cuadro de texto **URL de respuesta**, escriba la dirección URL que los usuarios usan para iniciar sesión en el sitio de 23 Video (p. ej.: *https://britta-simon.23Video.com/saml/login*).
   
    >[!NOTE]
    >La integración de Active Directory mediante SAML 2.0 está disponible para todos los usuarios de 23 Video. Si necesita los metadatos relacionados, póngase en contacto con el soporte técnico en [support@23company.com](mailto:support@23company.com) . 
    > 
 
  2. Haga clic en **Siguiente**.
4. En la página **Configurar inicio de sesión único en 23 Video** , siga estos pasos:
   
    ![Inicio de sesión único de Azure AD ][9] 
 1. Haga clic en Descargar certificado y después guarde el archivo en el equipo.
 2. Póngase en contacto con el equipo de soporte técnico de 23 Video a través de [support@23company.com](mailto:support@23company.com), proporcióneles el certificado descargado y los valores de **URL del emisor**, **Dirección URL del servicio de inicio de sesión único** y **Dirección URL de inicio de sesión único**, y pídales que configuren el inicio de sesión único para su aplicación 23 Video.    
 3. Haga clic en **Siguiente**.
5. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. 
   
    ![Inicio de sesión único de Azure AD ][10]
6. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_09.png)  
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes: 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_05.png)  
 1. En Tipo de usuario, seleccione Nuevo usuario de la organización. 
 2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**. 
 3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes: 
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_06.png)  
 1. En el cuadro de texto **Nombre**, escriba **Britta**.   
 2. En el cuadro de texto **Apellidos**, escriba **Simon**. 
 3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**. 
 4. En la lista **Rol**, seleccione **Usuario**.
 5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_08.png)  
 1. Anote el valor del campo **Nueva contraseña**. 
 2. Haga clic en **Completo**.   

### <a name="create-a-23-video-test-user"></a>Creación de un usuario de prueba de 23 Video
El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en 23 Video.

**Para crear un usuario llamado Britta Simon en 23 Video, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la compañía de 23 Video como administrador.
2. Vaya a **Configuración**.
3. En la sección **Usuarios**, haga clic en **Configurar**.
   
    ![Asignar usuario][400]
4. Haga clic en **Agregar un nuevo usuario**. 
   
    ![Asignar usuario][401]
5. En la sección **Invite someone to join this site** (Invitar a alguien a unirse a este sitio), realice los siguientes pasos:
   
    ![Asignar usuario][402]
 1. En el cuadro de texto **Direcciones de correo electrónico** , escriba la dirección de correo electrónico de Britta Simon en Azure AD.  
 2. Haga clic en **Agregar un usuario**.   

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo cual se le concederá acceso a 23 Video.

![Asignar usuario][200] 

**Para asignar a Britta Simon a 23 Video, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones. seleccione **23 Video**.
   
    ![Asignar usuario][202] 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de 23 Video en el panel de acceso, debería iniciar sesión automáticamente en su aplicación 23 Video.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-23video-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-23video-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-23video-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-23video-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_01.png
[25]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_02.png

[6]: ./media/active-directory-saas-23video-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_03.png
[8]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_04.png
[9]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_05.png
[10]: ./media/active-directory-saas-23video-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-23video-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-23video-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-23video-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-23video-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_07.png
[203]: ./media/active-directory-saas-23video-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-23video-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-23video-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_10.png
[401]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_11.png
[402]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_12.png





