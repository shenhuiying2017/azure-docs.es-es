---
title: "Tutorial: Integración de Azure Active Directory con Voyance | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Voyance."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 539dc1f9-64c9-4dce-b259-2b0b49dcf857
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d316178b9d3451eaefe88a8245538aef42b57a85
ms.openlocfilehash: 466b16cf391f6de101b6d212ad51d296d77cc804


---
# <a name="tutorial-azure-active-directory-integration-with-voyance"></a>Tutorial: Integración de Azure Active Directory con Voyance

En este tutorial, aprenderá a integrar Voyance con Azure Active Directory (Azure AD).

La integración de Voyance con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Voyance.
- Puede permitir que los usuarios inicien sesión automáticamente en Voyance (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Voyance, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Voyance.


> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Voyance desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-voyance-from-the-gallery"></a>Adición de Voyance desde la galería
Para configurar la integración de Voyance en Azure AD, deberá agregar Voyance desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Voyance desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.

    ![Applications][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Voyance**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_01.png)

7. En el panel de resultados, seleccione **Voyance** y luego haga clic en **Completar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Voyance con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Voyance para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Voyance.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Voyance.

Para configurar y probar el inicio de sesión único de Azure AD con Voyance, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Voyance](#creating-a-voyance-test-user)**: para tener un homólogo de Britta Simon en Voyance que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Voyance.


**Para configurar el inicio de sesión único de Azure AD con Voyance, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de aplicaciones de **Voyance**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único][6]

2. En la página **How would you like users to sign on to Voyance** (¿Cómo desea que los usuarios inicien sesión en Voyance?), seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_02.png)

3. En el cuadro de diálogo **Configurar las opciones de la aplicación**, si desea configurar la aplicación en el **modo iniciado por el proveedor de identidades**, realice los pasos siguientes y haga clic en **Siguiente**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_03.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<company name>.nyansa.com`.

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<company name>.nyansa.com/saml/create/`.

    c. Haga clic en **Siguiente**.

4. Si quiere configurar la aplicación en el **modo iniciado por el proveedor de servicios**, en la página de diálogo **Configurar las opciones de la aplicación**, haga clic en **"Mostrar la configuración avanzada (opcional)"**, escriba la **URL de inicio de sesión** y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_04.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<company name>.nyansa.com/`.

    b. Haga clic en **Siguiente**.

    > [!NOTE]
    > Tenga en cuenta que será preciso que actualice estos valores con los valores reales de URL de inicio de sesión, Identificador y URL de respuesta. Para obtener estos valores, póngase en contacto con el [equipo de soporte técnico de Voyance](emaiLto:support@nyansa.com).

5. En la página **Configurar inicio de sesión único en Voyance**, haga clic en **Descargar certificado** y guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_05.png) 

6. En otra ventana del explorador web, inicie sesión en el inquilino de Voyance como administrador.

7. Vaya a la esquina superior derecha de la barra de navegación y haga clic en la lista desplegable que dice "**Acme Universidad**".
    
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_001.png) 

8. Haga clic en "**Admin Settings**" (Configuración de administrador).

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_002.png)

9. Haga clic en la pestaña "**User Access**" (Acceso de usuario).

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_003.png)

10. Haga clic en el botón "**SSO is disabled**" (SSO está deshabilitado) para configurar Azure AD como un IdP mediante SAML 2.0.

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_004.png)

11. Vaya a la sección **SAML v2** y realice los siguientes pasos:

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_005.png)

    a. Seleccione **Habilitado**.

    b. En el cuadro de texto **IdP Login URL** (URL de inicio de sesión de idP), coloque el valor de **SAML SSO URL** (URL de SSO de SAML) del Asistente para configuración de aplicaciones de Azure AD.

    c. Abra el certificado con codificación Base64 en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **IdP Cert** (Certificado IdP).

    d. Haga clic en el botón **Guardar** .

12. En el Portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.

    ![Inicio de sesión único de Azure AD ][10]

13. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
  
    ![Inicio de sesión único de Azure AD ][11]


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_09.png) 

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_05.png) 

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_06.png) 

    a. En el cuadro de texto **Nombre**, escriba **Britta**.  

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_08.png) 

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.   



### <a name="creating-a-voyance-test-user"></a>Creación de un usuario de prueba de Voyance

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Voyance. Voyance admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a Voyance se creará un nuevo usuario, en caso de que no exista.

> [!NOTE]
> Si necesita crear manualmente un usuario, es preciso que se ponga en contacto con el [equipo de soporte técnico de Voyance](emaiLto:support@nyansa.com).


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Voyance.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Voyance, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Voyance**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_50.png) 

3. En el menú de la parte superior, haga clic en **Usuarios**.

    ![Asignar usuario][203] 

4. En la lista Usuarios, seleccione **Britta Simon**.

5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
    
    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Voyance en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Voyance.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


