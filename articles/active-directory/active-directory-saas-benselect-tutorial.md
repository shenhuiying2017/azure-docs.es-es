---
title: "Tutorial: Integración de Azure Active Directory con BenSelect | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y BenSelect."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: ffa17478-3ea1-4356-a289-545b5b9a4494
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 296a7ce8f84c226dfa899788bd4e0581f0a80eae


---
# <a name="tutorial-azure-active-directory-integration-with-benselect"></a>Tutorial: Integración de Azure Active Directory con BenSelect
En este tutorial, obtendrá información sobre cómo integrar BenSelect con Azure Active Directory (Azure AD).

La integración de BenSelect con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a BenSelect.
* Puede permitir que los usuarios inicien sesión automáticamente en BenSelect (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con BenSelect, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en BenSelect

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

1. Adición de BenSelect desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-benselect-from-the-gallery"></a>Adición de BenSelect desde la galería
Para configurar la integración de BenSelect en Azure AD, es preciso agregar BenSelect desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar BenSelect desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **BenSelect**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_01.png)
7. En el panel de resultados, seleccione **BenSelect** y luego haga clic en **Completar** para agregar la aplicación.
   
    ![Selección de la aplicación en la galería](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con BenSelect con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de BenSelect para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de BenSelect.

Dicha relación se establece asignando el valor de **nombre de usuario** de Azure AD como el valor de **nombre de usuario** en BenSelect.

Para configurar y probar el inicio de sesión único de Azure AD con BenSelect, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de BenSelect](#creating-a-benselect-test-user)**: para tener un homólogo de Britta Simon en BenSelect vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación BenSelect.

La aplicación BenSelect espera las aserciones de SAML en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar el valor de estos atributos desde la pestaña**Atributo**de la aplicación. La siguiente captura de pantalla le muestra un ejemplo de esto. 

![Configurar inicio de sesión único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_06.png)

**Para configurar el inicio de sesión único de Azure AD con BenSelect, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **BenSelect**, en el menú de la parte superior, haga clic en **Atributos**.
   
     ![Configurar inicio de sesión único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_07.png)
2. En el cuadro de diálogo **Atributos de token de SAML** , para cada fila de la tabla siguiente, realice los pasos que se indican a continuación:
   
   | Nombre del atributo | Valor de atributo |
   | --- | --- |
   | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier |extractmailprefix([userprincipalname]) |
   
    a. Haga clic en **agregar atributo de usuario** para abrir el cuadro de diálogo **Agregar atributo de usuario**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_08.png)
   
    b. En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo que se muestra para la fila.
   
    c. En la lista **Valor del atributo** , escriba ExtractMailPrefix().
   
    d. En la lista **Correo** , escriba User.userprincipalname.
   
    e. Haga clic en **Completar**
3. En el menú de la parte superior, haga clic en **Inicio rápido**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_09.png)
4. En la página **¿Cómo desea que los usuarios inicien sesión en BenSelect?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_03.png) 
5. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_04.png) 
   
    a. En el cuadro de texto **URL de respuesta**, escriba la dirección URL con el siguiente patrón: `https://www.benselect.com/enroll/login.aspx?Path={<tenant name>}`.
   
    b. click **Siguiente**
6. En la página **Configurar inicio de sesión único en BenSelect** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_05.png)
   
    a. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
   
    b. Haga clic en **Siguiente**.
7. Para que SSO se configure para su aplicación, póngase en contacto con el equipo de soporte técnico de BenSelect a través de [support@selerix.com](mailto:support@selerix.com) y especifique:
   
   * El certificado descargado
   * La dirección URL de inicio de sesión único de SAML
   * La dirección URL de cierre de sesión 
   * El emisor 
     
     > [!NOTE]
     > Debe mencionar que esta integración requiere el algoritmo SHA256 (no se admite SHA1) para establecer el SSO en el servidor apropiado, como app2101.
     > 
     > 
8. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
9. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_04.png) 
5. En el cuadro de diálogo **Proporcione información sobre este usuario**, siga estos pasos:  ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En el cuadro de diálogo **Perfil de usuario**, siga estos pasos: ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_06.png) 
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-an-benselect-test-user"></a>Creación de un usuario de prueba de BenSelect
El objetivo de esta sección es crear un usuario llamado Britta Simon en BenSelect. Trabaje con el equipo de soporte técnico de BenSelect para agregar los usuarios en la cuenta de BenSelect.

> [!NOTE]
> Si necesita crear un usuario manualmente, es preciso que se ponga contacto con el equipo de soporte técnico de BenSelect a través de <mailto:support@selerix.com>.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a BenSelect.

![Asignar usuario][200] 

**Para asignar Britta Simon a BenSelect, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **BenSelect**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de BenSelect en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación BenSelect.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO4-->


