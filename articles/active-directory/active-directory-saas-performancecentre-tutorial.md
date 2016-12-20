---
title: "Tutorial: Integración de Azure Active Directory con PerformanceCentre | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y PerformanceCentre."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9b8c63d512f49a9b1478642f9f6733c849e3f48d


---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Tutorial: Integración de Azure Active Directory con PerformanceCentre
El objetivo de este tutorial es mostrar cómo integrar PerformanceCentre con Azure Active Directory (Azure AD).  
La integración de PerformanceCentre con Azure AD proporciona las siguientes ventajas: 

* Puede controlar en Azure AD quién tiene acceso a PerformanceCentre. 
* Puede permitir que los usuarios inicien sesión automáticamente en PerformanceCentre (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con PerformanceCentre, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en PerformanceCentre

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

1. Incorporación de PerformanceCentre desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-performancecentre-from-the-gallery"></a>Incorporación de PerformanceCentre desde la galería
Para configurar la integración de PerformanceCentre en Azure AD, deberá agregar PerformanceCentre desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar PerformanceCentre desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **PerformanceCentre**.
   
    ![Aplicaciones][5]
7. En el panel de resultados, seleccione **PerformanceCentre** y haga clic en **Completar** para agregar la aplicación.
   
    ![Aplicaciones][500]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con PerformanceCentre según un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de PerformanceCentre para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de PerformanceCentre.  
Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario **en Azure AD como el valor del **nombre de usuario** en PerformanceCentre.

Para configurar y probar el inicio de sesión único de Azure AD con PerformanceCentre, debe completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de PerformanceCentre](#creating-a-halogen-software-test-user)** : para tener un homólogo de Britta Simon en PerformanceCentre que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure AD clásico y configurar el inicio de sesión único en la aplicación de PerformanceCentre.

**Para configurar el inicio de sesión único de Azure AD con PerformanceCentre, realice los pasos siguientes:**

1. En el Portal de Azure AD clásico, en la página de integración de la aplicación **PerformanceCentre**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en PerformanceCentre?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][7] 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Inicio de sesión único de Azure AD][8] 
   
     a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL utilizada por los usuarios para iniciar sesión en el sitio de PerformanceCentre (p. ej.: *http://companyname.performancecentre.com/saml/SSO*).
   
     b. Haga clic en **Next**.
4. En la página **Configurar inicio de sesión único en PerformanceCentre** , siga estos pasos:
   
    ![Inicio de sesión único de Azure AD][9] 
   
    a. Haga clic en **Descargar metadatos**y después guarde el archivo en el equipo.
5. Inicie sesión en su sitio de la empresa **PerformanceCentre** como administrador.
6. En la pestaña en el lado izquierdo, haga clic en **Configurar**.
   
    ![Inicio de sesión único de Azure AD ][10]
7. En la pestaña en el lado izquierdo, haga clic en **Varios** y, después, en **Inicio de sesión único**.
   
    ![Inicio de sesión único de Azure AD ][11]
8. En **Protocolo**, seleccione **SAML**.
   
    ![Inicio de sesión único de Azure AD ][12]
9. Abra el archivo de metadatos descargado en el Bloc de notas, copie el contenido, péguelo en el cuadro de texto **Metadatos del proveedor de identidades** y haga clic en **Guardar**.
   
    ![Inicio de sesión único de Azure AD ][13]
10. Compruebe que los valores de **Dirección URL de base de entidad** y **Dirección URL de Id. de entidad** son correctos.
    
     ![Inicio de sesión único de Azure AD ][14]
11. En el Portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. 
    
     ![Inicio de sesión único de Azure AD ][15]
12. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
    
     ![Inicio de sesión único de Azure AD ][16]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.  

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png)  
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes: 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png)  
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes: 
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) 
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-performancecentre-test-user"></a>Creación de un usuario de prueba de PerformanceCentre
El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en PerformanceCentre.

**Para crear un usuario llamado Britta Simon en PerformanceCentre, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la empresa PerformanceCentre como administrador.
2. En el menú de la izquierda, haga clic en **Interrelate** (Interrelacionar) y, a continuación, en **Create Participant** (Crear participante).
   
    ![Crear usuario][400]
3. En el cuadro de diálogo **Interrelate - Create Participant** (Interrelacionar- Crear participante), realice los pasos siguientes:
   
    ![Crear usuario][401]
   
    a. Escriba los atributos necesarios para Britta Simon en los cuadros de texto correspondientes.
   
   > [!IMPORTANT]
   > El atributo del nombre del usuario de Britta en PerformanceCentre debe ser el mismo que el nombre del usuario en Azure AD.
   > 
   > 

    b. Seleccione **Administrador de cliente** como **Elegir rol**. 

    c. Haga clic en **Guardar**.   


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo que se le concederá acceso a PerformanceCentre.

![Asignar usuario][200] 

**Para asignar un usuario llamado Britta Simon a PerformanceCentre, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **PerformanceCentre**.
   
    ![Asignar usuario][202]
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  
Al hacer clic en el icono de PerformanceCentre en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de PerformanceCentre.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png






<!--HONumber=Nov16_HO3-->


