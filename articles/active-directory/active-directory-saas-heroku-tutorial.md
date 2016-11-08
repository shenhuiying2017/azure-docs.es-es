---
title: 'Tutorial: Integración de Azure Active Directory con Heroku | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Heroku.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2016
ms.author: jeedes

---
# Tutorial: Integración de Azure Active Directory con Heroku
En este tutorial, obtendrá información sobre cómo integrar Heroku con Azure Active Directory (Azure AD).

Integrar Heroku con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Heroku.
* Puede permitir que los usuarios inicien sesión automáticamente en Heroku (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos
Para configurar la integración de Azure AD con Heroku, se necesitan los siguientes elementos:

* Una suscripción de Azure
* Una suscripción habilitada para el inicio de sesión único en Heroku

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Heroku desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## Incorporación de Heroku desde la galería
Para configurar la integración de Heroku en Azure AD, es preciso agregar Heroku desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Heroku desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.
   
    ![Aplicaciones][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Heroku**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_01.png)
7. En el panel de resultados, seleccione **Heroku** y, después, haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_02.png)

## Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Heroku con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Heroku para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Heroku. Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Heroku.

Para configurar y probar el inicio de sesión único de Azure AD con Heroku, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Heroku](#creating-an-heroku-test-user)**: para tener un homólogo de Britta Simon en Heroku que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Heroku.

**Para configurar el inicio de sesión único de Azure AD con Heroku, realice los pasos siguientes:**

1. En el Portal clásico, en la página de integración de aplicaciones de **Heroku**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6]
2. En la página **¿Cómo desea que los usuarios inicien sesión en Heroku?**, seleccione **Inicio de sesión único de Azure AD** y, después, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_03.png)
3. En la página de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_04.png)
   
   > [!NOTE]
   > Si no sabe cuáles son los valores correctos para las direcciones URL de inicio de sesión y del identificador, consulte [Para habilitar SSO en Heroku, realice los pasos siguientes](#x123) con el fin de recibir instrucciones sobre cómo obtenerlos.
   > 
   > 

    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que los usuarios utilizan para iniciar sesión en su aplicación de Heroku con el siguiente patrón: **https://sso.heroku.com/saml/<nombreDeCompañía>/init**.

    b. En el cuadro de texto **Identificador**, escriba una URL con el siguiente patrón: **https://sso.heroku.com/saml/\<nombreDeCompañía>**.

    c. Haga clic en **Siguiente**.


1. En la página **Configurar inicio de sesión único en Heroku**, siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_05.png)
   
    a. Haga clic en **Descargar metadatos** y luego guarde el archivo en el equipo.
   
    b. Haga clic en **Siguiente**.
2. Realice los pasos siguientes para habilitar el inicio de sesión único en Heroku:
   
    a. Inicie sesión en la cuenta de Heroku como administrador.
   
    b. Haga clic en la pestaña **Configuración**.
   
    c. En la **página de inicio de sesión único**, haga clic en **Cargar metadatos**.
   
    d. Cargue el archivo de metadatos que ha descargado desde el Portal de Azure clásico.
   
    e. Cuando la instalación se realice correctamente, los administradores verán un cuadro de diálogo de confirmación y se mostrará la dirección URL de inicio de sesión único para los usuarios finales.
   
    f. <a name="x123"></a>Copie la **dirección URL de inicio de sesión de Heroku** y el **identificador de entidad de Heroku** y, después, en el Portal clásico de Azure AD, vuelva a la página **Configurar las opciones de la aplicación** y pegue los valores en los cuadros de texto correspondientes.

    ![Configurar inicio de sesión único](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_52.png)

    g. Haga clic en **Siguiente**.

1. Seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD][10]
2. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
   
    ![Inicio de sesión único de Azure AD][11]

### Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_09.png)
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_03.png)
4. Para abrir el diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_04.png)
5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_05.png)
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página del cuadro de diálogo **Perfil de usuario**, realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_06.png)
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_07.png)
8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_08.png)
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.

### Creación de un usuario de prueba de Heroku
En esta sección, creará un usuario llamado Britta Simon en Heroku. Heroku admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Se crea un nuevo usuario al acceder a Heroku en caso de que el usuario todavía no exista. Después de que se aprovisione la cuenta, el usuario final recibe un mensaje de correo electrónico de comprobación y debe hacer clic en el vínculo de confirmación.

> [!NOTE]
> Si necesita crear manualmente un usuario, es preciso que se ponga contacto con el equipo de soporte técnico de Heroku.
> 
> 

### Asignación del usuario de prueba de Azure AD
En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Heroku.

![Asignar usuario][200]

**Para asignar a Britta Simon a Heroku, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **Heroku**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_50.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### Prueba del inicio de sesión único
En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso. Al hacer clic en el icono de Heroku en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Heroku.

## Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->