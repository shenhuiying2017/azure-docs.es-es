---
title: "Tutorial: integración de Azure Active Directory con Alcumus Info Exchange | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Alcumus Info Exchange."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d26034b8-f0d5-4f65-aa56-0fc168ceec8c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 97834f5d3b9bf466f867d5cc7bcaa825983b252f


---
# <a name="tutorial-azure-active-directory-integration-with-alcumus-info-exchange"></a>Tutorial: Integración de Azure Active Directory con Alcumus Info Exchange
El objetivo de este tutorial es mostrar cómo integrar Alcumus Info Exchange con Azure Active Directory (Azure AD).  
La integración de Alcumus Info Exchange con Azure AD proporciona las siguientes ventajas: 

* Puede controlar en Azure AD quién tiene acceso a Alcumus Info Exchange. 
* Puede permitir que los usuarios inicien sesión automáticamente en Alcumus Info Exchange (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Alcumus Info Exchange, necesita los siguientes elementos:

* Una suscripción de [Azure AD](https://azure.microsoft.com/)
* Una suscripción habilitada para inicio de sesión único en [Alcumus Info Exchange](http://www.alcumusgroup.com/)

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.  
El escenario descrito en este tutorial consta de tres bloques de creación principales:

1. Adición de Alcumus Info Exchange de la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-alcumus-info-exchange-from-the-gallery"></a>Adición de Alcumus Info Exchange de la galería
Para configurar la integración de Alcumus Info Exchange en Azure AD, deberá agregar Alcumus Info Exchange desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Alcumus Info Exchange desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Alcumus Info Exchange**.
   
    ![Aplicaciones][5]
7. En el panel de resultados, seleccione **Alcumus Info Exchange** y, después, haga clic en **Completar** para agregar la aplicación.
   
    ![Aplicaciones][400]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Alcumus Info Exchange con una usuaria de prueba llamada "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Alcumus Info Exchange para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Alcumus Info Exchange.  
Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** de Azure AD como el valor del **nombre de usuario** en Alcumus Info Exchange.

Para configurar y probar el inicio de sesión único de Azure AD con Alcumus Info Exchange, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Alcumus Info Exchange](#creating-a-alcumus-info-exchange-test-user)** : para tener un homólogo de Britta Simon en Alcumus Info Exchange que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Alcumus Info Exchange.

**Para configurar el inicio de sesión único de Azure AD con Alcumus Info Exchange, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de la aplicación **Alcumus Info Exchange**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6]
2. En la página **¿Cómo desea que los usuarios inicien sesión en Alcumus Info Exchange?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][7]
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes: 
   
    ![Inicio de sesión único de Azure AD][8]
   
    a. En el cuadro de texto **URL de respuesta** , escriba la dirección URL de consumidor que configuró el equipo de soporte técnico de Alcumus Info Exchange.
   
   > [!NOTE]
   > Si desconoce el valor correcto, póngase en contacto con el equipo de soporte técnico de Alcumus Info Exchange en [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com).
   > 
   > 
   
    b. Haga clic en **Next**.
4. En la página **Configuración de inicio de sesión único en Alcumus Info Exchange**, haga clic en **Descargar metadatos** y, después, guarde el archivo de metadatos localmente en el equipo.
   
    ![Qué es Azure AD Connect][9]
5. Póngase en contacto con el equipo de soporte técnico de Alcumus Info Exchange en [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com), proporcióneles el archivo de metadatos y hágales saber que deberían habilitar el SSO.
6. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. 
   
    ![Qué es Azure AD Connect][10]
7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Qué es Azure AD Connect][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.  

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_02.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes: 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en Siguiente.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes: 
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_06.png) 

    a. En el cuadro de texto **Nombre**, escriba **Britta**.  

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.


1. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_07.png) 
2. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-alcumus-info-exchange-test-user"></a>Creación de un usuario de prueba de Alcumus Info Exchange
El objetivo de esta sección es crear una usuaria de prueba llamada Britta Simon en Alcumus Info Exchange.

**Para crear una usuaria llamada Britta Simon en Alcumus Info Exchange, realice los pasos siguientes:**

1. Póngase en contacto con el equipo de soporte técnico de Alcumus Info Exchange en [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com).

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Alcumus Info Exchange.

![Asignar usuario][200]

**Para asignar a una usuaria llamada Britta Simon a Alcumus Info Exchange, realice los pasos siguientes:**

1. En el Portal de Azure, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **Alcumus Info Exchange**.
   
    ![Asignar usuario][202]
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  
Al hacer clic en el icono de Alcumus Info Exchange en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Alcumus Info Exchange.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_01.png
[6]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_02.png
[7]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_03.png
[8]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_04.png
[9]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_05.png
[10]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_06.png
[11]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_07.png
[20]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_08.png
[203]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_205.png
[400]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_402.png



<!--HONumber=Nov16_HO3-->


