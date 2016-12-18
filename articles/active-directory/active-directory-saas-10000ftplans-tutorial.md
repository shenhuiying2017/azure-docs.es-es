---
title: "Tutorial: Integración de Azure Active Directory con Novatus | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y 10,000ft Plans."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b60c955e-8fa3-4872-a897-c4e81fd7beac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e5ed7edef886c9a3308f3f30f983124c46db3452


---
# <a name="tutorial-azure-active-directory-integration-with-10000ft-plans"></a>Tutorial: integración de Azure Active Directory con 10,000ft Plans
El objetivo de este tutorial es mostrar cómo integrar 10,000ft Plans con Azure Active Directory (Azure AD).  
Integrar 10,000ft Plans con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a 10,000ft Plans
* Puede permitir que los usuarios inicien sesión automáticamente en 10,000ft Plans (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con 10,000ft Plans, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en 10,000ft Plans

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.  
La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de 10,000ft Plans desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-10000ft-plans-from-the-gallery"></a>Incorporación de 10,000ft Plans desde la galería
Para configurar la integración de 10,000ft Plans en Azure AD, será preciso que agregue 10,000ft Plans desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar 10,000ft Plans desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![aplicaciones][4]
6. En el cuadro de búsqueda, escriba **10,000ft Plans**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_01.png)
7. En el panel de resultados, seleccione **10,000ft Plans** y, luego, haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con 10,000ft Plans con una usuaria de prueba llamada "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de 10,000ft Plans para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de 10,000ft Plans.  
Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en 10,000ft Plans.

Para configurar y probar el inicio de sesión único de Azure AD con 10,000ft Plans, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de 10,000ft Plans](#creating-a-10000ft-plans-test-user)** : para tener un homólogo de Britta Simon en 10,000ft Plans que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación 10,000ft Plans.

**Para configurar el inicio de sesión único de Azure AD con 10,000ft Plans, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **10,000ft Plans**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en 10,000ft Plans?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_03.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_04.png) 

    a. En el cuadro de texto **URL de inicio de sesión**, escriba `https://app.10000ft.com`.

    b. En el cuadro de texto **Identificador**, escriba: `https://app.10000ft.com/saml/metadata`.

    > [AZURE.NOTE] El valor de **Identificador** es distinto si tiene un dominio personalizado. Si necesita ayuda, póngase en contacto con el [equipo de soporte técnico de 10,000ft Plans](mailto:support@10000ft.com).  

    c. Haga clic en **Siguiente**


1. En la página **Configurar inicio de sesión único en 10,000ft Plans**, lleve a cabo estos pasos y, luego, haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_05.png) 
   
    a. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
   
    b. Haga clic en **Next**.
2. Con el fin de que se configure el SSO para la aplicación, póngase en contacto con el [equipo de soporte técnico de 10,000ft Plans](mailto:support@10000ft.com), adjunte el certificado descargado y proporcione la dirección URL del emisor, la dirección URL de inicio de sesión único de SAML y la dirección URL de cierre de sesión.
3. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
4. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.  

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba de SECURE DELIVER en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_06.png) 
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-10000ft-plans-test-user"></a>Creación de un usuario de prueba de 10,000ft Plans
El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en 10,000ft Plans. 10,000ft Plans admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Durante un intento de obtener acceso a 10,000ft Plans se creará un nuevo usuario, en caso de que no exista. 

> [!NOTE]
> Si necesita crear manualmente un usuario, debe ponerse en contacto con el equipo de soporte técnico de Certify.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a 10,000ft Plans.

![Asignar usuario][200] 

**Para asignar Britta Simon a 10,000ft Plans, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en la opción **Aplicaciones** del menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **10,000ft Plans**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  
Al hacer clic en el icono de 10,000ft Plans en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de 10,000ft Plans.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


