---
title: "Tutorial: Integración de Azure Active Directory con Boomi | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Boomi."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a60db808388bf1fdf9441518920f2eb4e5fcc4d
ms.openlocfilehash: bafbfb710d8cdb370d21d9299c447a0dfed3c468


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Tutorial: Integración de Azure Active Directory con Boomi

En este tutorial, aprenderá a integrar Boomi con Azure Active Directory (Azure AD).

La integración de Boomi con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Boomi.
- Puede permitir que los usuarios inicien sesión automáticamente en Boomi (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Boomi, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Boomi.


> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Boomi desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-boomi-from-the-gallery"></a>Adición de Boomi desde la galería
Para configurar la integración de Boomi en Azure AD, deberá agregar Boomi desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Boomi desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.

    ![Applications][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Boomi**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_01.png)

7. En el panel de resultados, seleccione **Boomi** y, después, haga clic en **Completar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Boomi con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Boomi para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Boomi.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Boomi.

Para configurar y probar el inicio de sesión único de Azure AD con Boomi, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Boomi](#creating-a-boomi-test-user)**: para tener un homólogo de Britta Simon en Boomi que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Boomi.

La aplicación Boomi espera la aserción de SAML en un formato específico, lo que requiere establecer el valor del atributo NameIdentifier con el id. de federación del usuario. De forma predeterminada, Azure AD usa el valor de UserPrincipalName como el atributo NameIdentifier. Pero para la correcta integración, debe ajustar este valor para que coincida con el id. de federación del usuario en Boomi. Puede cambiarlo en la pestaña "**Atributo**" como se muestra en la siguiente captura de pantalla. La integración solo funcionará después de completar la asignación correcta.

![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

**Para configurar el inicio de sesión único de Azure AD con Boomi, siga estos pasos:**

1. En el portal clásico, en la página de integración de aplicaciones de **Boomi**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único][6] 

2. En la página **How would you like users to sign on to Boomi** (¿Cómo desea que los usuarios inicien sesión en Boomi?), seleccione **Inicio de sesión único de Azure AD** y después haga clic en **Siguiente**.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_03.png)

3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_04.png)

    a. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://platform.boomi.com/sso/<account name>/saml`.

    b. Haga clic en **Siguiente**.

    > [!NOTE] 
    > Tenga en cuenta que este no es el valor real. Tendrá que actualizar este valor con la dirección URL de respuesta real. Póngase en contacto con el equipo de soporte técnico de Boomi para obtener este valor.

4. En la página **Configurar inicio de sesión único en Boomi**, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_05.png) 

    > [!NOTE]
    > Tenga en cuenta que el valor de notificación de NameID en la respuesta debe coincidir con el id. de federación configurado en el sistema Boomi. Por tanto, trabaje con el equipo de soporte técnico de Boomi para asignar el identificador de usuario adecuado en su organización como id. de federación. De forma predeterminada, Azure AD establecerá NameIdentifier como valor UPN. Puede cambiarlo en la pestaña Atributo, como se muestra en la captura de pantalla siguiente. La integración solo funcionará después de completar la asignación correcta. 
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Boomi. 

6. Vaya a **Company Name** (Nombre de la compañía) y haga clic en **Set up** (Configurar).

7. Haga clic en la pestaña **SSO options** (Opciones de SSO) y realice los siguientes pasos.

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_06.png)

    a. Marque la casilla **Enable SAML Single Sign-On** (Habilitar el inicio de sesión único de SAML).

    b. Haga clic en **Import** (Importar) para cargar el certificado descargado de Azure AD en **Identity Provider Certificate** (Certificado del proveedor de identidades).
    
    c. En el cuadro de texto **Identity Provider Login URL** (URL de inicio de sesión del proveedor de identidades), coloque el valor de **Remote login URL** (URL de inicio de sesión remoto) desde el Asistente para configuración de aplicaciones de Azure AD.

    d. En **Federation Id Location** (Ubicación del id. de federación), seleccione el botón de opción **Federation Id is in NameID element of the Subject** (El id. de federación está en el elemento NameID del sujeto). 

    e. Haga clic en el botón **Guardar** .

8. En el Portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.

    ![Inicio de sesión único de Azure AD ][10]

9. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
  
    ![Inicio de sesión único de Azure AD ][11]


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_09.png) 

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_05.png) 

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_06.png) 

    a. En el cuadro de texto **Nombre**, escriba **Britta**.  

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_08.png) 

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.   



### <a name="creating-a-boomi-test-user"></a>Creación de un usuario de prueba de Boomi

Para permitir que los usuarios de Azure AD inicien sesión en Boomi, tienen que aprovisionarse en Boomi. En el caso de Boomi, el aprovisionamiento es una tarea manual.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:

1. Inicie sesión en su sitio de la compañía de Boomi como administrador.

2. Después de iniciar sesión, vaya a **User Management** (Administración de usuarios) y vaya a **Users** (Usuarios).

    ![Usuarios](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "Users")

3. Haga clic en el icono **+**, se abre el cuadro de diálogo **Add/Maintain User Roles** (Agregar o mantener roles de usuario).

    ![Usuarios](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "Users")

    ![Usuarios](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "Users")

4. Escriba la **dirección de correo electrónico del usuario**.

5. Escriba el **nombre** y el **apellido** del usuario.

6. Escriba el **id. de federación** del usuario. Cada usuario debe tener un id. de federación que identifica de forma única al usuario dentro de la cuenta. 

7. Asigne el rol **Usuario estándar** al usuario. No asigne el rol Administrador porque le proporcionaría acceso normal a Atmosphere, así como acceso de inicio de sesión único.

8. Haga clic en **OK**.

    > [!NOTE]
    > El usuario no recibirá un correo electrónico de notificación de bienvenida con una contraseña que se puede usar para iniciar sesión en la cuenta de AtomSphere porque su contraseña se administrará mediante el proveedor de identidades. Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Boomi que proporcione Boomi para aprovisionar cuentas de usuario de AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Boomi.

![Asignar usuario][200] 

**Para asignar Britta Simon a Boomi, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Boomi**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_50.png) 

3. En el menú de la parte superior, haga clic en **Usuarios**.

    ![Asignar usuario][203] 

4. En la lista Usuarios, seleccione **Britta Simon**.

5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
    
    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Boomi en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Boomi.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


