---
title: "Tutorial: integración de Azure Active Directory con Optimizely | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Optimizely."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4d7900a30d2489dac345dcd8977f48e07d2ee963


---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Tutorial: integración de Azure Active Directory con Optimizely
En este tutorial, obtendrá información sobre cómo integrar Optimizely con Azure Active Directory (Azure AD).

La integración de Optimizely con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Optimizely.
* Puede permitir que los usuarios inicien sesión automáticamente en Optimizely (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Optimizely, se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en **Optimizely**

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Optimizely desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-optimizely-from-the-gallery"></a>Adición de Optimizely desde la galería
Para configurar la integración de Optimizely en Azure AD, es preciso agregar Optimizely desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Optimizely desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Optimizely**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_01.png)
7. En el panel de resultados, seleccione **Optimizely** y, después, haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Optimizely con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Optimizely para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Optimizely.
Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en Optimizely.

Para configurar y probar el inicio de sesión único de Azure AD con Optimizely, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Optimizely](#creating-an-optimizely-test-user)** : para tener un homólogo de Britta Simon en Optimizely que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Optimizely.

La aplicación de Optimizely espera que las aserciones SAML contengan un atributo denominado "email". El valor de "email" debe ser un correo electrónico reconocido de Optimizely autenticado mediante Azure AD. Configure las notificaciones "email" para esta aplicación. Puede administrar el valor de estos atributos desde la pestaña **"Atributo"** de la aplicación. La siguiente captura de pantalla le muestra un ejemplo de esto. 

![Configurar inicio de sesión único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_03.png) 

**Para configurar el inicio de sesión único de Azure AD con Optimizely, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de la aplicación **Optimizely**, en el menú de la parte superior, haga clic en **Atributos**.
   
    ![Configurar inicio de sesión único][5]
2. En el cuadro de diálogo de atributos de token SAML, agregue el atributo "email".
   
    a. Haga clic en **add user attribute** to open the **Agregar atributo de usuario** . 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_05.png)
   
    b. En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo "email".
   
    c. En la lista **Valor de atributo** , seleccione el valor de atributo "userprincipalname" o cualquier otro que contenga un correo electrónico reconocido de Azure AD y Optimizely.
   
    d. Haga clic en **Completo**.
3. En el menú de la parte superior, haga clic en **Inicio rápido**.
   
    ![Configurar inicio de sesión único][6]
4. En el portal clásico, en la página de integración de la aplicación de **Optimizely**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][7] 
5. En la página **¿Cómo desea que los usuarios inicien sesión en Optimizely?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_06.png)
6. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes: 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba: `https://app.optimizely.net/contoso`

    b. En el cuadro de texto **Identificador**, escriba: `urn:auth0:optimizely:contoso`

    c. Haga clic en **Next**. 


    > [AZURE.NOTE] Los valores de **URL de inicio de sesión** e **Identificador** solo son marcadores de posición de los valores reales. Más adelante en este tutorial podrá encontrar instrucciones para obtener los valores reales de Optimizely.

1. En la página **Configurar inicio de sesión único en Optimizely** , siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_08.png)
   
    a. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
   
    b. Copie la **Dirección URL del servicio de inicio de sesión único**.
2. Para configurar el inicio de sesión único para su aplicación, póngase en contacto con el administrador de cuentas de Optimizely y proporcione la siguiente información:
   
   * El certificado descargado 
   * La dirección URL de servicio de inicio de sesión único
     
     En respuesta a su correo electrónico, Optimizely proporciona la URL de inicio de sesión (SSO iniciado por el proveedor de servicios) y los valores de Identificador (Id. de entidad del proveedor de servicios).
3. Vaya a la página de diálogo **Configurar las opciones de la aplicación** y realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)
   
    a. En el cuadro de texto **URL de inicio de sesión**, escriba la **dirección URL de inicio de sesión único iniciada por el proveedor de servicios** que proporciona Optimizely.
   
    b. En el cuadro de texto **Identificador**, escriba el **id. de entidad del proveedor de servicios** que proporciona Optimizely.
   
    c. Haga clic en **Next**.
4. En la página **Configurar inicio de sesión único en Optimizely** , siga estos pasos:
   
   ![Inicio de sesión único de Azure AD][10]
   
   a. Seleccione la confirmación de la configuración de inicio de sesión único.
   
   b. Haga clic en **Siguiente**.
5. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
   ![Inicio de sesión único de Azure AD ][11]
6. En una ventana de explorador diferente, inicie sesión en su aplicación de Optimizely como administrador.
7. Haga clic en el nombre de la cuenta de la parte superior derecha y, después, en **Configuración de la cuenta**.
   
   ![Inicio de sesión único de Azure AD ](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)
8. En la pestaña Cuenta, active la casilla **Habilitar SSO** que se encuentra debajo de Inicio de sesión único en la sección **Información general**.
   
   ![Inicio de sesión único de Azure AD ](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.
En la lista Usuarios, seleccione **Britta Simon**.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_06.png) 
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-an-optimizely-test-user"></a>Creación de un usuario de prueba de Optimizely
En esta sección, creará un usuario llamado Britta Simon en Optimizely.

1. En la página principal, seleccione la pestaña **Colaboradores** .
2. Haga clic en **New Collaborator** (Nuevo colaborador) para agregar un nuevo colaborador al proyecto.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)
3. Escriba la dirección de correo electrónico y asígnele un rol. Haga clic en **Invitar**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

1. El colaborador recibirá un correo electrónico de invitación Tendrá que iniciar sesión en Optimizer con la dirección de correo electrónico.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Optimizely.

![Asignar usuario][200] 

**Para asignar Britta Simon a Optimizely, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **Optimizely**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Todos los usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Optimizely en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Optimizely.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-optimizely-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


