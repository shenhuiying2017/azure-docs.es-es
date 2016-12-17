---
title: "Tutorial: Integración de Azure Active Directory con Origami | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Origami."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d5499636ff1c560dd5a50924f0fd67d8dac50203


---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Tutorial: Integración de Azure Active Directory con Origami
En este tutorial, obtendrá información sobre cómo integrar Origami con Azure Active Directory (Azure AD).

Integrar Origami con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Origami
* Puede permitir que los usuarios inicien sesión automáticamente en Origami (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Origami, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en Origami

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Origami desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-origami-from-the-gallery"></a>Incorporación de Origami desde la galería
Para configurar la integración de Origami en Azure AD, deberá agregar Origami desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Origami desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Origami**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_01.png)
7. En el panel de resultados, seleccione **Origami** y haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Origami con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Origami para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Origami.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Origami.

Para configurar y probar el inicio de sesión único de Azure AD con Origami, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Origami](#creating-a-origami-test-user)**: para tener un homólogo de Britta Simon en Origami que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Origami.

**Para configurar el inicio de sesión único de Azure AD con Origami, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Origami**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en Origami?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_03.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_04.png) 
   
    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL utilizada por los usuarios para iniciar sesión en la aplicación de Origami con el siguiente patrón: **https://live.origamirisk.com/origami/account/login?account=\<nombre de la compañía\>**.
   
    b. click **Siguiente**
4. En la página **Configurar inicio de sesión único en Origami** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_05.png)
   
    a. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
   
    b. Haga clic en **Siguiente**.
5. Inicie sesión en la cuenta de Origami con derechos de administrador.
6. En el menú de la parte superior, haga clic en **Administrador**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)
7. En la página del cuadro de diálogo Single Sign On Setup (Configuración de inicio de sesión único), siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/123.png)
   
    a. Seleccione **Enable Single Sign On**(Habilitar el inicio de sesión único).
   
    b. En el Portal de Azure clásico, copie el valor de **Dirección URL de inicio de sesión único de SAM**L y péguelo en el cuadro de texto **Identity Provider's Sign-in Page URL** (Dirección URL de página de inicio de sesión del proveedor de identidades).
   
    c. En el Portal de Azure clásico, copie el valor de **DIRECCIÓN URL DEL SERVICIO DE CIERRE DE SESIÓN ÚNICO** y péguelo en el cuadro de texto **Identity Provider's Sign-out Page URL** (Dirección URL de página de cierre de sesión del proveedor de identidades).
   
    d. Haga clic en **Browse** (Examinar) para cargar el certificado que ha descargado desde el Portal de Azure clásico.
   
    e. Haga clic en **Guardar cambios**.
8. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
9. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_04.png) 
5. En el cuadro de diálogo **Proporcione información sobre este usuario**, siga estos pasos:  ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En el cuadro de diálogo **Perfil de usuario**, siga estos pasos: ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_06.png) 
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-an-origami-test-user"></a>Creación de un usuario de prueba de Origami
En esta sección, creará un usuario llamado Britta Simon en Origami. 

1. Inicie sesión en la cuenta de Origami con derechos de administrador.
2. En el menú de la parte superior, haga clic en **Administrador**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)
3. En el cuadro de diálogo **Users and Security** (Usuarios y seguridad), haga clic en **Users** (Usuarios).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_54.png)
4. Haga clic en **Add New User**(Agregar nuevo usuario).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_55.png)
5. En el cuadro de diálogo Add New User (Agregar nuevo usuario), realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_56.png)
   
    a. En el cuadro de texto **User Name** (Nombre de usuario), escriba el nombre de usuario de Britta Simon en el Portal de Azure clásico.
   
    b. En el cuadro de texto **Password** (Contraseña), escriba una contraseña.
   
    c. En el cuadro de texto **Confirm Password** (Confirmar contraseña), escriba la contraseña de nuevo.
   
    d. En el cuadro de texto **Nombre**, escriba **Britta**.
   
    e. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
    f. Haga clic en **Guardar**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_57.png)
6. Asigne **User Roles** (Roles de usuario) y **Client Access** (Acceso de cliente) al usuario. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Origami.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Origami, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **Origami**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Origami en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Origami.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-origami-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-origami-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-origami-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-origami-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-origami-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-origami-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-origami-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-origami-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-origami-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-origami-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-origami-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-origami-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-origami-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


