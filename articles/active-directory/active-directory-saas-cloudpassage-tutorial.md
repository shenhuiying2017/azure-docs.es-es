<properties
    pageTitle="Tutorial: integración de Azure Active Directory con CloudPassage | Microsoft Azure"
    description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y CloudPassage."
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



# <a name="tutorial:-azure-active-directory-integration-with-cloudpassage"></a>Tutorial: integración de Azure Active Directory con CloudPassage

El objetivo de este tutorial es mostrar cómo integrar CloudPassage con Azure Active Directory (Azure AD).  
La integración de CloudPassage con Azure AD proporciona las siguientes ventajas: 

- Puede controlar en Azure AD quién tiene acceso a CloudPassage. 
- Puede permitir que los usuarios inicien sesión automáticamente en CloudPassage (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, Azure Active Directory. 


Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos 

Para configurar la integración de Azure AD con CloudPassage, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en CloudPassage


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una suscripción de prueba a Azure gratuita durante un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.  
La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de CloudPassage desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-cloudpassage-from-the-gallery"></a>Adición de CloudPassage desde la galería
Para configurar la integración de CloudPassage en Azure AD, deberá agregar CloudPassage desde la galería a la lista de aplicaciones SaaS administradas.

### <a name="to-add-cloudpassage-from-the-gallery,-perform-the-following-steps:"></a>Para agregar CloudPassage desde la galería, realice los pasos siguientes:

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
 
    ![Active Directory][1]

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.

    ![Applications][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **CloudPassage**.

    ![Aplicaciones][5]

7. En el panel de resultados, seleccione **CloudPassage** y, a continuación, haga clic en **Completar** para agregar la aplicación.

    ![Aplicaciones][6]



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con CloudPassage con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de CloudPassage para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de CloudPassage.  
Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en CloudPassage.
 
Para configurar y probar el inicio de sesión único de Azure AD con CloudPassage, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de CloudPassage](#creating-a-halogen-software-test-user)** : para tener un homólogo de Britta Simon en CloudPassage que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure AD clásico y configurar el inicio de sesión único en la aplicación CloudPassage.  
La aplicación CloudPassage espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla le muestra un ejemplo de esto.

![Configurar inicio de sesión único][21]

**Para configurar el inicio de sesión único de Azure AD con CloudPassage, realice los pasos siguientes:**

1. En el Portal de Azure AD clásico, en la página de integración de la aplicación **CloudPassage**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único][7]

2. En la página **¿Cómo desea que los usuarios inicien sesión en CloudPassage?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único][8]

3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes: 

    ![Configurar las opciones de la aplicación][9]
 
    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL utilizada por los usuarios para iniciar sesión en la aplicación CloudPassage (por ejemplo: *https://portal.cloudpassage.com/saml/init/accountid*). 

    b. En el cuadro de texto **URL de respuesta**, escriba su dirección URL de AssertionConsumerService (por ejemplo: *https://portal.cloudpassage.com/saml/consume/accountid*). Para obtener el valor de este atributo, haga clic en **SSO Setup documentation** (Documentación de instalación de inicio de sesión única) en la sección **Single Sign-on Settings** (Configuración de inicio de sesión único) del portal de CloudPassage.  
    ![Configurar inicio de sesión único][10]

    C. Haga clic en **Siguiente**.



4. En la página **Configurar inicio de sesión único en CloudPassage**, haga clic en **Descargar certificado** y guarde el archivo de certificado localmente en el equipo. 

    ![Configurar inicio de sesión único][11]

5. En otra ventana del explorador, inicie sesión en su sitio de la compañía de CloudPassage como administrador.

6. En el menú de la parte superior, haga clic en **Settings** (Configuración) y en **Site Administration** (Administración del sitio). 

    ![Configurar inicio de sesión único][12]

7. Haga clic en la pestaña **Authentication Settings** (Configuración de autenticación). 

    ![Configurar inicio de sesión único][13]


8. En la sección **Single Sign-on Settings** (Configuración del inicio de sesión único), siga estos pasos: 

    ![Configurar inicio de sesión único][14]


    a. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en CloudPassage**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **SAML issuer URL** (URL del emisor de SAML).

    b. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en CloudPassage**, copie el valor de **Extremo iniciado por el proveedor de servicios** y péguelo en el cuadro de texto **SAML endpoint URL** (URL de punto de conexión de SAML).

    c. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en CloudPassage**, copie el valor de **URL de cierre de sesión** y péguelo en el cuadro de texto **Logout landing page** (Página de aterrizaje de cierre de sesión).

    d. Cree un archivo codificado en **base 64** a partir del certificado descargado. 
          
    >[AZURE.TIP] Para más información, vea [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    e. Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y, a continuación, péguelo en el cuadro de texto **certificado X.509** .

    f. Haga clic en **Guardar**.


9. En el Portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. 

    ![Configurar inicio de sesión único][15]


10. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**. 

    ![Configurar inicio de sesión único][16]



11. En el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**. 

    ![Configurar inicio de sesión único][17]

12. Para agregar los atributos de usuario necesarios en cada fila de la tabla siguiente, realice los pasos que se indican a continuación: 

  	| Nombre del atributo | Valor de atributo |
  	| --- | --- |
  	| firstname | user.givenname |
  	| lastname | user.surname |
  	| email | user.mail |

 

    a. Haga clic en **agregar atributo de usuario**. 

    ![Configurar inicio de sesión único][18]

    b. En el cuadro de texto **Nombre de atributo**, escriba el valor de nombre de atributo y en **Valor de atributo**, seleccione el valor del atributo que se muestra para esa fila. 

    ![Configurar inicio de sesión único][19]
     
    c. Haga clic en **Completo**.


13. En la barra de herramientas de la parte inferior, haga clic en **Aplicar cambios**. 

    ![Configurar inicio de sesión único][20]



### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.  

![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png) 
 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. 

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes: 

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png) 

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
  
    c. Haga clic en Siguiente.

6.  En la página de diálogo **Perfil de usuario** , realice los pasos siguientes: 

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png) 
  
    a. En el cuadro de texto **Nombre**, escriba **Britta**.  
  
    b. En el cuadro de texto **Apellidos**, escriba **Simon**.
  
    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  
    d. En la lista **Rol**, seleccione **Usuario**.
  
    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png) 
 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png) 
  
    a. Anote el valor del campo **Nueva contraseña**.
  
    b. Haga clic en **Completo**.   


  
 
### <a name="creating-a-cloudpassage-test-user"></a>Creación de un usuario de prueba de CloudPassage

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en CloudPassage.

#### <a name="to-create-a-user-called-britta-simon-in-cloudpassage,-perform-the-following-steps:"></a>Para crear un usuario llamado Britta Simon en CloudPassage, realice los pasos siguientes:

1.  Inicie sesión en su sitio de la compañía de **CloudPassage** como administrador. 

2.  En la barra de herramientas de la parte superior, haga clic en **Settings** (Configuración) y en **Site Administration** (Administración del sitio). 

    ![Creación de un usuario de prueba de CloudPassage][22] 

3.  Haga clic en la pestaña **Users** (Usuarios) y, a continuación, en **Add New User** (Agregar nuevo usuario). 

    ![Creación de un usuario de prueba de CloudPassage][23]
    
4.  En la sección **Agregar nuevo usuario** , lleve a cabo estos pasos: 

    ![Creación de un usuario de prueba de CloudPassage][24]

    a. En el cuadro de texto **First Name** (Nombre), escriba Britta.

    b. En el cuadro de texto **Last Name** (Apellidos), escriba Simon.

    c. En el cuadro de texto **Username** (Nombre de usuario), el cuadro de texto **Email** (Correo electrónico) y el cuadro de texto **Retype Email** (Volver a escribir correo electrónico), escriba el nombre de usuario de Britta en Azure AD.

    d. En **Access Type** (Tipo de acceso), seleccione **Enable Halo Portal Access** (Habilitar el acceso de Portal de Halo).

    e. Haga clic en **Agregar**.










### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a CloudPassage.

![Asignar usuario][30]

**Para asignar un usuario llamado Britta Simon a CloudPassage, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

    ![Asignar usuario][26]

2. En la lista de aplicaciones, seleccione **CloudPassage**.

    ![Asignar usuario][27]

1. En el menú de la parte superior, haga clic en **Usuarios**.

    ![Asignar usuario][25]

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

    ![Asignar usuario][29]



### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  
Al hacer clic en el icono de CloudPassage en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de CloudPassage.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_01.png
[6]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_02.png
[7]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_05.png
[8]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_03.png
[9]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_04.png
[10]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png
[11]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_06.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[16]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_11.png
[17]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_10.png
[18]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_11.png
[19]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_12.png
[20]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_14.png
[21]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_14.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png
[25]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_12.png
[27]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_13.png
[28]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[29]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_17.png
























<!--HONumber=Oct16_HO2-->


