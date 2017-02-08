---
title: "Tutorial: integración de Azure Active Directory con Anaplan | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Anaplan."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4a9c2914-6c8c-4a88-93e3-3753afb40e6b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 95369b2a0c1172e072aed33d075255fdcafe8a9c


---
# <a name="tutorial-azure-active-directory-integration-with-anaplan"></a>Tutorial: Integración de Azure Active Directory con Anaplan
El objetivo de este tutorial es mostrar cómo integrar Anaplan con Azure Active Directory (Azure AD).

Integrar Anaplan con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Anaplan.
* Puede permitir que los usuarios inicien sesión automáticamente en Anaplan (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Anaplan, se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en Anaplan

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

1. Incorporación de Anaplan desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-anaplan-from-the-gallery"></a>Incorporación de Anaplan desde la galería
Para configurar la integración de Anaplan en Azure AD, es preciso agregar Anaplan desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Anaplan desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Anaplan**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-anaplan-tutorial/tutorial_anaplan_01.png)
7. En el panel de resultados, seleccione **Anaplan** y luego haga clic en **Completar** para agregar la aplicación.
   
    ![Selección de la aplicación en la galería](./media/active-directory-saas-anaplan-tutorial/tutorial_anaplan_001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Anaplan con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Anaplan para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Anaplan.

Dicha relación se establece asignando el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario** en Anaplan.

Para configurar y probar el inicio de sesión único de Azure AD con Anaplan, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Anaplan](#creating-a-anaplan-test-user)** : para tener un homólogo de Britta Simon en Anaplan que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Anaplan.

**Para configurar el inicio de sesión único de Azure AD con Anaplan, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Anaplan**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en Anaplan?**, seleccione **Inicio de sesión único de Azure AD** y después haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-anaplan-tutorial/tutorial_anaplan_03.png) 
3. En el cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-anaplan-tutorial/tutorial_anaplan_04.png)
   
    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:  `https://sdp.anaplan.com/frontdoor/saml/<tenant name>`.
   
    b. Haga clic en **Siguiente**

    > [AZURE.NOTE] El valor de la dirección URL de inicio de sesión de este tutorial es solo un marcador de posición. Para obtener el valor real para su entorno, póngase en contacto con el soporte técnico de Anaplan.


1. En la página **Configurar inicio de sesión único en Anaplan**, realice los pasos siguientes y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-anaplan-tutorial/tutorial_anaplan_05.png)
   
    a. Haga clic en **Descargar metadatos**y luego guarde el archivo en el equipo.
   
    b. Haga clic en **Siguiente**.
2. Para que SSO se configure para su aplicación, póngase en contacto con el equipo de soporte técnico de Anaplan a través de [support@anaplan.com](mailto:support@anaplan.com) y especifique:
   
   * El archivo de metadatos descargado
   * El **identificador de entidad**
   * La **dirección URL de inicio de sesión único de SAML** 
   * La **dirección URL del servicio de cierre de sesión único**
3. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
4. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-anaplan-tutorial/create_aaduser_09.png)
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-anaplan-tutorial/create_aaduser_03.png)
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-anaplan-tutorial/create_aaduser_04.png)
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-anaplan-tutorial/create_aaduser_05.png)
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-anaplan-tutorial/create_aaduser_06.png)
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-anaplan-tutorial/create_aaduser_07.png)
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-anaplan-tutorial/create_aaduser_08.png)
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-anaplan-test-user"></a>Creación de un usuario de prueba de Anaplan
En esta sección, creará un usuario llamado Britta Simon en Anaplan. Colabore con el equipo de soporte técnico de Anaplan a través de <mailto:support@anaplan.com> para agregar los usuarios a la plataforma Anaplan.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Anaplan.

![Asignar usuario][200]

**Para asignar a Britta Simon a Anaplan, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **Anaplan**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-anaplan-tutorial/tutorial_anaplan_50.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Anaplan en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Anaplan.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-anaplan-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-anaplan-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-anaplan-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-anaplan-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-anaplan-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-anaplan-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-anaplan-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-anaplan-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-anaplan-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-anaplan-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-anaplan-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-anaplan-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-anaplan-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO4-->


