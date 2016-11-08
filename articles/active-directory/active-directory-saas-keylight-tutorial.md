---
title: 'Tutorial: Integración de Azure Active Directory con Keylight | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Keylight.
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
# Tutorial: Integración de Azure Active Directory con Keylight
En este tutorial, obtendrá información sobre cómo integrar Keylight con Azure Active Directory (Azure AD).

Integrar Keylight con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Keylight.
* Puede permitir que los usuarios inicien sesión automáticamente en Keylight (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos
Para configurar la integración de Azure AD con Keylight, necesita los siguientes elementos:

* Una suscripción de Azure
* Una suscripción habilitada para el inicio de sesión único en Keylight

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Keylight desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## Incorporación de Keylight desde la galería
Para configurar la integración de Keylight en Azure AD, es preciso agregar Keylight desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Keylight desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.
   
    ![Aplicaciones][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Keylight**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_01.png)
7. En el panel de resultados, seleccione **Keylight** y luego haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_02.png)

## Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Keylight con un usuario de prueba llamado "Britta Simon".

Para configurar y probar el inicio de sesión único de Azure AD con Keylight, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Keylight](#creating-a-keylight-test-user)**: para tener un homólogo de Britta Simon en Keylight que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurará el inicio de sesión único en la aplicación Keylight.

**Para configurar el inicio de sesión único de Azure AD con Keylight, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Keylight**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6]
2. En la página **¿Cómo desea que los usuarios inicien sesión en Keylight?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y después haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_03.png)
3. En la página de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_04.png)

    a. En el cuadro de texto URL de inicio de sesión, escriba la dirección URL que los usuarios usan para iniciar sesión en su aplicación Keylight con el siguiente patrón: **"https://<nombre de la compañía>.keylightgrc.com/Login.aspx?saml=1"**.


1. En la página **Configurar inicio de sesión único en Keylight**, siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_05.png)
   
    a. Haga clic en **Descargar certificado** y después guarde el archivo en el equipo.
   
    b. Haga clic en **Siguiente**.
2. Realice los pasos siguientes para habilitar el inicio de sesión único en Keylight:
   
    a. Inicie sesión en su cuenta de Keylight como administrador.
   
    b. En el menú de la parte superior, haga clic en el icono de la **persona** y seleccione **Keylight Setup** (Configuración de Keylight).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/401.png)
   
    c. En la vista de árbol de la izquierda, haga clic en **SAML**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/402.png)
   
    d. En el cuadro de diálogo **SAML Settings ** (Configuración de SAML), haga clic en **Edit** (Editar).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/404.png)
3. En la página del cuadro de diálogo **Edit SAML Settings** (Editar la configuración de SAML), realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/405.png)
   
    a. Establezca **SAML authentication** (Autenticación SAML) como **Active** (Activada).

    b. En el Portal de Azure AD clásico, copie el valor de **Dirección URL de inicio de sesión único de SAML** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión del proveedor de identidades**.

    c. En el Portal de Azure AD clásico, copie el valor de **Dirección URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión del proveedor de identidades**.

    d. Haga clic en **Elegir archivo** para seleccionar el certificado de Keylight descargado y después haga clic en **Abrir** para cargarlo.


    e. Establezca **SAML User Id location** (Ubicación de id. de usuario de SAML) en **NameIdentifier element of the subject statement** (Elemento NameIdentifier de la instrucción Subject).

    f. Proporcione la información del campo **Proveedor de servicios de Keylight mediante el siguiente patrón: **https://&lt;Companynombre de la compañía&gt;.keylightgrc.com**.

    g. Establezca **Auto-provision users** (Usuarios de aprovisionamiento automático) en **Active** (Activado).

    h. Establezca **Auto-provision account type** (Tipo de cuenta de aprovisionamiento automático) en **Full User** (Usuario completo).

    i. En el campo **Auto-provision security role** (Rol de seguridad de aprovisionamiento automático), seleccione **Standard User with SAML** (Usuario estándar con SAML).

    j. En el campo **Auto-provision security config** (Configuración de seguridad de aprovisionamiento automático), seleccione **Standard User Configuration** (Configuración de usuario estándar).

    k. En el cuadro de texto Email Attribute (Atributo de correo electrónico), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    l. En el cuadro de texto **First name attribute** (Atributo de nombre), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    m. En el cuadro de texto **Last name attribute** (Atributo de apellido), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    n. Haga clic en **Guardar**.




1. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD][10]
2. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
   
    ![Inicio de sesión único de Azure AD][11]

### Creación de un usuario de prueba de Azure AD
En esta sección, se crea un usuario de prueba llamado Britta Simon en el Portal de Azure clásico.

En la lista Usuarios, seleccione **Britta Simon**.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_09.png)
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png)
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png)
5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_05.png)
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página del cuadro de diálogo **Perfil de usuario**, realice los siguientes pasos:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_06.png)
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En la página del cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_07.png)
8. En la página del cuadro de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_08.png)
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.

### Crear un usuario de prueba de Keylight
En esta sección, creará un usuario llamado Britta Simon en Keylight. Keylight admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Se crea un nuevo usuario al acceder a Keylight en caso de que el usuario todavía no exista.

> [!NOTE]
> Si necesita crear manualmente un usuario, debe ponerse en contacto con el equipo de soporte técnico de Keylight.
> 
> 

### Asignación del usuario de prueba de Azure AD
En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Keylight.

![Asignar usuario][200]

**Para asignar a Britta Simon a Keylight, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **Keylight**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_50.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### Prueba del inicio de sesión único
En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Keylight en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación Keylight.

## Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->