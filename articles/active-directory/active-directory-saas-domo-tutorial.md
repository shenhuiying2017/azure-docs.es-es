<properties
    pageTitle="Tutorial: integración de Azure Active Directory con Domo | Microsoft Azure"
    description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Domo."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="jeedes"/>



# <a name="tutorial:-azure-active-directory-integration-with-domo"></a>Tutorial: integración de Azure Active Directory con Domo

El objetivo de este tutorial es mostrar cómo integrar Domo con Azure Active Directory (Azure AD).  
Integrar Domo con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Domo.
- Puede permitir que los usuarios inicien sesión automáticamente en Domo (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Domo, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Domo


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.  
La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Domo desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-domo-from-the-gallery"></a>Adición de Domo desde la galería
Para configurar la integración de Domo en Azure AD, deberá agregar Domo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Domo desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.

    ![Applications][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Domo**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-domo-tutorial/tutorial_domo_01.png)

7. En el panel de resultados, seleccione **Domo** y haga clic en **Completa**r para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-domo-tutorial/tutorial_domo_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Domo con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Domo para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Domo.  
Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Domo.

Para configurar y probar el inicio de sesión único de Azure AD con Domo, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de usuario de prueba de Domo](#creating-a-domo-test-user)** : para tener un homólogo de Britta Simon en Domo que esté vinculado a su representación en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Domo.

La aplicación Domo espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar el valor de estos atributos desde la pestaña **"Atributo"** de la aplicación. La siguiente captura de pantalla le muestra un ejemplo de esto. 

![Configurar inicio de sesión único](./media/active-directory-saas-domo-tutorial/tutorial_domo_06.png) 

**Para configurar el inicio de sesión único de Azure AD con Domo, realice los pasos siguientes:**


1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Domo**, en el menú de la parte superior, haga clic en **Atributos**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-domo-tutorial/tutorial_general_80.png) 


1. En el cuadro de diálogo **Atributos de token de SAML** , para cada fila de la tabla siguiente, realice los pasos que se indican a continuación:

  	| Nombre del atributo | Valor de atributo |
  	| --- | --- |    
  	| name  | user.displayname |
  	| email | user.mail |

    a. Haga clic en **agregar atributo de usuario** para abrir el cuadro de diálogo **Agregar atributo de usuario**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-domo-tutorial/tutorial_general_81.png) 

    b. En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo que se muestra para esa fila.

    c. En la lista **Valor de atributo** , seleccione el valor de atributo que se muestra para esa fila.

    d. Haga clic en **Completo**.  

2. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Domo**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único][6] 

3. En la página **¿Cómo desea que los usuarios inicien sesión en Domo?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-domo-tutorial/tutorial_domo_03.png) 

4. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-domo-tutorial/tutorial_domo_04.png) 


    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL utilizada por los usuarios para iniciar sesión en su aplicación de Domo con el siguiente patrón: `https://<company name>.domo.com`.

    b. Haga clic en **Siguiente**.

5. En la página **Configurar inicio de sesión único en Domo** , realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-domo-tutorial/tutorial_domo_05.png) 

    a. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.


6. Con el fin de que se configure el SSO para la aplicación, póngase en contacto con el equipo de soporte técnico de [support@domo.com](mailto: support@domo.com), adjunte el certificado descargado y proporcione la **Dirección URL del emisor**, la **Dirección URL de inicio de sesión único de SAML** y la **Dirección URL de cierre de sesión**.


7. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.

    ![Inicio de sesión único de Azure AD ][10]

8. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  

    ![Inicio de sesión único de Azure AD ][11]




### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.  

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_09.png) 

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_05.png) 

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_06.png) 

    a. En el cuadro de texto **Nombre**, escriba **Britta**.  

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_08.png) 

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.   



### <a name="creating-a-domo-test-user"></a>Creación de usuario de prueba de Domo

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Domo. Domo admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a Domo se creará un nuevo usuario, en caso de que no exista. [Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Si necesita crear manualmente un usuario, es preciso que se ponga en contacto con el equipo de soporte técnico de Domo.


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Domo.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Domo, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Domo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-domo-tutorial/tutorial_domo_50.png) 

1. En el menú de la parte superior, haga clic en **Usuarios**.

    ![Asignar usuario][203] 

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  
Al hacer clic en el icono de Domo en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Domo.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-domo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-domo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-domo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-domo-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-domo-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-domo-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-domo-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-domo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-domo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-domo-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-domo-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-domo-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-domo-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->


