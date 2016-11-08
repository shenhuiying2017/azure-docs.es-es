---
title: 'Tutorial: integración de Azure Active Directory con Intralinks | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Intralinks.
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
# Tutorial: integración de Azure Active Directory con Intralinks
En este tutorial, obtendrá información sobre cómo integrar Intralinks con Azure Active Directory (Azure AD).

Integrar Intralinks con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Intralinks.
* Puede permitir que los usuarios inicien sesión automáticamente en Intralinks (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos
Para configurar la integración de Azure AD con Intralinks, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para inicio de sesión único en Intralinks

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.

El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Intralinks desde la Galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## Adición de Intralinks desde la Galería
Para configurar la integración de Intralinks en Azure AD, será preciso que agregue Intralinks desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Intralinks desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.
   
    ![Aplicaciones][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Intralinks**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_01.png)
7. En el panel de resultados, seleccione **Intralinks** y luego haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_02.png)

## Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Intralinks con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Intralinks para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Intralinks.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Intralinks.

Para configurar y probar el inicio de sesión único de Azure AD con Intralinks, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Intralinks](#creating-an-intralinks-test-user)**: para tener un homólogo de Britta Simon en Intralinks que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Intralinks.

**Para configurar el inicio de sesión único de Azure AD con Intralinks, realice los pasos siguientes:**

1. En el Portal clásico, en la página de integración de aplicaciones de **Intralinks**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6]
2. En la página **¿Cómo desea que los usuarios inicien sesión en Intralinks?**, seleccione **Inicio de sesión único de Azure AD** y después haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_03.png)
3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_04.png)
   
    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que usan los usuarios para iniciar sesión en la aplicación de Intralinks con el siguiente patrón: **https://\<nombreDeCompañía>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<ID de inquilino de AzureAD>/**.
   
    b. Haga clic en **Siguiente**.
4. En la página **Configurar inicio de sesión único en Intralinks**, siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_05.png)
   
    a. Haga clic en **Descargar metadatos** y luego guarde el archivo en el equipo.
   
    b. Haga clic en **Siguiente**.
5. Para configurar el inicio de sesión único para la aplicación, póngase en contacto con el equipo de soporte técnico de Intralinks y envíe el archivo de metadatos descargado adjunto por correo electrónico.
6. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD][10]
7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
   
    ![Inicio de sesión único de Azure AD][11]

### Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

En la lista Usuarios, seleccione **Britta Simon**.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_09.png)
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_03.png)
4. Para abrir el diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_04.png)
5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, siga estos pasos:![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_05.png)
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página del cuadro de diálogo **Perfil de usuario**, siga estos pasos: ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_06.png)
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_07.png)
8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_08.png)
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.

### Creación de un usuario de prueba de Intralinks
En esta sección, creará un usuario llamado Britta Simon en Intralinks. Trabaje con el equipo de soporte técnico de Intralinks para agregar usuarios a la plataforma de Intralinks.

### Asignación del usuario de prueba de Azure AD
En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Intralinks.

![Asignar usuario][200]

**Para asignar Britta Simon a Intralinks, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **Intralinks**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_50.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### Adición de la aplicación VIA o Elite de Intralinks
Intralinks utiliza la misma plataforma de identidad de inicio de sesión único para las demás aplicaciones de Intralinks excluyendo la aplicación Deal Nexus. Por tanto si piensa usar cualquier otra aplicación Intralinks, primero tendrá que configurar el inicio de sesión único para una aplicación Intralinks principal mediante el procedimiento descrito anteriormente.

Después, podrá realizar el procedimiento siguiente para agregar otra aplicación Intralinks en el inquilino que puede aprovechar esta aplicación principal de inicio de sesión único.

> [!NOTE]
> Tenga en cuenta que esta característica solo está disponible para los clientes de la SKU Premium de Azure AD y no está disponible para los clientes de SKU Básico o Gratis.
> 
> 

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.
   
    ![Aplicaciones][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En la pestaña izquierda, haga clic en la pestaña **Personalizado**.
   
    ![Adición de la aplicación VIA o Elite de Intralinks](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_51.png)
7. Asigne un nombre adecuado para la aplicación, por ejemplo, **Intralinks Elite** y haga clic en botón Finalizar.
8. Haga clic en el botón **Configurar inicio de sesión único**.
9. Seleccione la opción **Inicio de sesión único existente**.
   
    ![Adición de la aplicación VIA o Elite de Intralinks](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_52.png)
10. Obtenga la dirección URL de SSO iniciado por el proveedor de servicios del equipo de Intralinks para la otra aplicación Intralinks y escríbala tal como se muestra a continuación.
    
    ![Adición de la aplicación VIA o Elite de Intralinks](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_53.png)
    
    a. En el cuadro de texto URL de inicio de sesión, escriba la dirección URL que usan los usuarios para iniciar sesión en la aplicación de Intralinks con el siguiente patrón: **https://\<nombreDeCompañía>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<IDInquilinoDeAzureAD>/**.
11. Haga clic en **Siguiente**.
12. Asigne la aplicación al usuario o a grupos, como se muestra en la sección **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**.

### Prueba del inicio de sesión único
En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Intralinks en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Intralinks.

## Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->