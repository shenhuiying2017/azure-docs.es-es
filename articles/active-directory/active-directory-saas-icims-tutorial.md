---
title: "Tutorial: integración de Azure Active Directory con ICIMS | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory e ICIMS."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 72dbd649-e4b1-4d72-ad76-636d84922596
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 52cb334ba91a696a9b5d901a05eda9a5d562bc0e
ms.openlocfilehash: 80a859805fad5fb07963cb6251eb38e0a9af720d
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-icims"></a>Tutorial: integración de Azure Active Directory con ICIMS
El objetivo de este tutorial es mostrar cómo integrar ICIMS con Azure Active Directory (Azure AD).

Integrar ICIMS con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a ICIMS.
* Puede permitir que los usuarios inicien sesión automáticamente en ICIMS (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con ICIMS, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en ICIMS

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.  

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de ICIMS desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-icims-from-the-gallery"></a>Adición de ICIMS desde la galería
Para configurar la integración de ICIMS en Azure AD, deberá agregar ICIMS desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar ICIMS desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **ICIMS**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-icims-tutorial/tutorial_icims_01.png)
7. En el panel de resultados, seleccione **ICIMS** y, después, haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-icims-tutorial/tutorial_icims_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con ICIMS con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de ICIMS para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ICIMS.  

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en ICIMS.

Para configurar y probar el inicio de sesión único de Azure AD con ICIMS, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de ICIMS](#creating-a-icims-test-user)** : para tener un homólogo de Britta Simon en ICIMS que esté vinculado a la representación de esta en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurarlo en la aplicación ICIMS.

**Para configurar el inicio de sesión único de Azure AD con ICIMS, siga estos pasos:**

1. En el Portal de Azure clásico, en la página de integración de la aplicación **ICIMS**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en ICIMS?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-icims-tutorial/tutorial_icims_03.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-icims-tutorial/tutorial_icims_04.png) 
  1. En el cuadro de texto URL de inicio de sesión, escriba la dirección URL que usan los usuarios para iniciar sesión en su aplicación de ICIMS con el siguiente patrón: `https://<tenant name>.icims.com`
  2. Haga clic en **Siguiente**.
4. En la página **Configurar inicio de sesión único en ICIMS** , siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-icims-tutorial/tutorial_icims_05.png)   
  1. Haga clic en **Descargar metadatos**y luego guarde el archivo en el equipo. 
  2. Haga clic en **Siguiente**.
5. Para configurar el inicio de sesión único para la aplicación, póngase en contacto con el equipo de soporte técnico de ICIMS y envíe el archivo de metadatos descargado adjunto por correo electrónico. Además, proporcione la dirección URL del emisor, la dirección URL de inicio de sesión único de SAML y la dirección URL de cierre de sesión para que se puedan configurar para la integración de SSO.
6. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.  

 * En la lista Usuarios, seleccione **Britta Simon**.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-icims-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-icims-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-icims-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-icims-tutorial/create_aaduser_05.png) 
  1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
  2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
  3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-icims-tutorial/create_aaduser_06.png)  
  1. En el cuadro de texto **Nombre**, escriba **Britta**.   
  2. En el cuadro de texto **Apellidos**, escriba **Simon**.
  3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  4. En la lista **Rol**, seleccione **Usuario**.
  5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-icims-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-icims-tutorial/create_aaduser_08.png)  
  1. Anote el valor del campo **Nueva contraseña**.
  2. Haga clic en **Completo**.   

### <a name="create-a-icims-test-user"></a>Creación de un usuario de prueba de ICIMS
El objetivo de esta sección es crear una usuaria llamada llamado Britta Simon en ICIMS. Trabaje con el equipo de soporte técnico de ICIMS para agregar usuarios a la cuenta de ICIMS. 

>[!NOTE]
>Si necesita crear manualmente un usuario, es preciso que se ponga en contacto con el equipo de soporte técnico de ICIMS.
> 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo cual se le concederá acceso a ICIMS.

![Asignar usuario][200] 

**Para asignar la usuaria Britta Simon a ICIMS, siga estos pasos:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en la opción **Aplicaciones** del menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **ICIMS**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-icims-tutorial/tutorial_icims_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  

Al hacer clic en el icono de ICIMS en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de ICIMS.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-icims-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-icims-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-icims-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-icims-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-icims-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-icims-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-icims-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-icims-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-icims-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-icims-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-icims-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-icims-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-icims-tutorial/tutorial_general_205.png

