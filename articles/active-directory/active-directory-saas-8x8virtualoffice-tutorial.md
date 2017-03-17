---
title: "Tutorial: integración de Azure Active Directory con 8x8 Virtual Office | Microsoft Azure"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y 8x8 Virtual Office."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 149896e820d7873752fc40ee8270a8fc00ee0b0c
ms.openlocfilehash: 96c5d5aafaff24d618f930a1b5fcedcc5c52a9a9
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Tutorial: Integración de Azure Active Directory con 8x8 Virtual Office
El objetivo de este tutorial es mostrar cómo integrar 8x8 Virtual Office con Azure Active Directory (Azure AD).

La integración de 8x8 Virtual Office con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a 8x8 Virtual Office.
* Puede permitir que los usuarios inicien sesión automáticamente en 8x8 Virtual Office (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con 8x8 Virtual Office, se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en 8x8 Virtual Office

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

    To test the steps in this tutorial, you should follow these recommendations:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Microsoft Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de 8x8 Virtual Office desde la galería
2. Configuración y prueba del inicio de sesión único de Microsoft Azure AD

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Incorporación de 8x8 Virtual Office desde la galería
Para configurar la integración de 8x8 Virtual Office en Azure AD, es preciso agregar 8x8 Virtual Office desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar 8x8 Virtual Office desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **8x8 Virtual Office**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_01.png)
7. En el panel de resultados, seleccione **8x8 Virtual Office** y, después, haga clic en **Completar** para agregar la aplicación.
   
    ![Selección de la aplicación en la galería](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_0001.png)

## <a name="configure-and-test-microsoft-azure-ad-sso"></a>Configuración y prueba del inicio de sesión único de Microsoft Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Microsoft Azure AD con 8x8 Virtual Office con una usuaria de prueba llamada "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de 8x8 Virtual Office para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de 8x8 Virtual Office.

Esta relación de vínculo se establece mediante la asignación del valor de **nombre de usuario** en Azure AD como valor de **nombre de usuario** en 8x8 Virtual Office.

Para configurar y probar el inicio de sesión único de Microsoft Azure AD con 8x8 Virtual Office, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Microsoft Azure AD](#configuring-azure-ad-single-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Microsoft Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de 8x8 Virtual Office](#creating-a-8x8-virtual-office-test-user)** : para tener un homólogo de Britta Simon en 8x8 Virtual Office que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para que Britta Simon pueda usar el inicio de sesión único de Microsoft Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configuring-microsoft-azure-ad-sso"></a>Configuración del inicio de sesión único de Microsoft Azure AD
En esta sección, habilitará el inicio de sesión único de Microsoft Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación 8x8 Virtual Office.

**Para configurar el inicio de sesión único de Microsoft Azure AD con 8x8 Virtual Office, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **8x8 Virtual Office**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en 8x8 Virtual Office?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_03.png) 
3. En el cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_04.png)
  1. En el cuadro de texto **URL de respuesta**, escriba: `https://sso.8x8.com/saml2`
  2. Haga clic en **Siguiente**.
4. En la página **Configurar inicio de sesión único en 8x8 Virtual Office**, realice los pasos que se indican a continuación y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_05.png)
  1. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
  2. Haga clic en **Siguiente**.
5. Inicie la sesión en el inquilino de 8x8 Virtual Office como administrador.
6. Seleccione el **administrador de cuentas de Virtual Office** en el panel de la aplicación.
   
    ![Configurar en la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)
7. Seleccione la cuenta **empresarial** que se va a administrar y haga clic en el botón **Iniciar sesión**.
   
    ![Configurar en la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)
8. Haga clic en la pestaña **Cuentas** en la lista de menús.
   
    ![Configurar en la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)
9. Haga clic en **Inicio de sesión único** en la lista de cuentas.
   
    ![Configurar en la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)
10. Seleccione **Inicio de sesión único** en el método Autenticación y haga clic en **SAML**.
    
    ![Configurar en la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)
11. Copie la **dirección URL de inicio de sesión único de SAML**, la **dirección URL del servicio de cierre de sesión único** y la **dirección URL del emisor** de Azure AD a **Sign In URL** (URL de inicio de sesión), **Sign Out URL** (URL de cierre de sesión) e **Issuer URL** (URL de emisor) en 8x8 Virtual Office. 
    
    ![Configurar en la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)
    
    ![Configurar en la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_007.png)
12. Haga clic en el botón **Explorador** para cargar el certificado que descargó de Azure AD y luego haga clic en el botón **Guardar**.
13. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
    
    ![Inicio de sesión único de Azure AD ][10]
14. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
    
    ![Inicio de sesión único de Azure AD ][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_09.png)
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_03.png)
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_04.png)
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_05.png)
  1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
  2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
  3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_06.png)
  1. En el cuadro de texto **Nombre**, escriba **Britta**.  
  2. En el cuadro de texto **Apellidos**, escriba **Simon**.
  3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  4. En la lista **Rol**, seleccione **Usuario**.
  5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_07.png)
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_08.png)
  1. Anote el valor del campo **Nueva contraseña**.
  2. Haga clic en **Completo**.   

### <a name="create-a-8x8-virtual-office-test-user"></a>Creación de un usuario de prueba de 8x8 Virtual Office
El objetivo de esta sección es crear un usuario llamado Britta Simon en 8x8 Virtual Office. 8x8 Virtual Office admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a 8x8 Virtual Office se creará un nuevo usuario, en caso de que no exista. 

>[!NOTE]
>Si necesita crear manualmente un usuario, es preciso que se ponga en contacto con el equipo de soporte técnico de 8x8 Virtual Office. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a 8x8 Virtual Office.

![Asignar usuario][200]

**Para asignar a Britta Simon a 8x8 Virtual Office, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **8x8 Virtual Office**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_50.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Microsoft Azure AD mediante el panel de acceso.

Al hacer clic en el icono de 8x8 Virtual Office en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación 8x8 Virtual Office.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_205.png

