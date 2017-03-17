---
title: "Tutorial: Integración de Azure Active Directory con Boomi | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Boomi."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: e5d37e9e723d97d1e6844f0b89ad1c2aae258565
ms.openlocfilehash: 9ab3ac2cd6643b0e53f1f7853239c90e78f4bf77
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Tutorial: Integración de Azure Active Directory con Boomi

En este tutorial, aprenderá a integrar Boomi con Azure Active Directory (Azure AD).

La integración de Boomi con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Boomi.
- Puede permitir que los usuarios inicien sesión automáticamente en Boomi (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure.

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

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Boomi**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_000.png)

5. En el panel de resultados, seleccione **Boomi** y luego haga clic en **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_0001.png)


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

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación Boomi.

**Para configurar el inicio de sesión único de Azure AD con Boomi, siga estos pasos:**

1. En el Portal de administración de Azure, en la página de integración de aplicaciones de **Boomi**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_01.png)

3. En la sección **Dominio y direcciones URL de Boomi**, en el cuadro de texto **Dirección URL de respuesta**, escriba una dirección URL usando el patrón siguiente:`https://platform.boomi.com/sso/<account name>/saml`.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_02.png)

    > [!NOTE] 
    > Tenga en cuenta que este no es el valor real. Tendrá que actualizar este valor con la dirección URL de respuesta real. Póngase en contacto con el equipo de soporte técnico de Boomi para obtener este valor. 

4. La aplicación Boomi espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_03.png)

5. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, para cada fila que se muestra en la tabla siguiente, realice los pasos siguientes:
    
    | Nombre del atributo | Valor de atributo |
    | --- | --- |    
    | FEDERATION_ID | user.mail |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_05.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**

6. En la sección **Certificado de firma de SAML**, haga clic en **Crear nuevo certificado**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_06.png)     

7. En el cuadro de diálogo **Crear nuevo certificado**, haga clic en el icono del calendario y seleccione una valor en **Fecha de expiración**. Luego haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_general_300.png)

8. En la sección **Certificado de firma de SAML**, seleccione **Make new certificate active** (Activar el nuevo certificado) y haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_07.png)

9. En la ventana emergente **Rollover certificate** (Certificado de sustitución), haga clic en **Aceptar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_general_400.png)

10. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_08.png) 

11. En la sección **Configuración de Boomi**, haga clic en **Configurar Boomi** para abrir la ventana **Configurar inicio de sesión**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_09.png) 

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_10.png)

12. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Boomi. 

13. Vaya a **Company Name** (Nombre de la compañía) y haga clic en **Set up** (Configurar).

14. Haga clic en la pestaña **SSO options** (Opciones de SSO) y realice los siguientes pasos.

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_11.png)

    a. Marque la casilla **Enable SAML Single Sign-On** (Habilitar el inicio de sesión único de SAML).

    b. Haga clic en **Import** (Importar) para cargar el certificado descargado de Azure AD en **Identity Provider Certificate** (Certificado del proveedor de identidades).
    
    c. En el cuadro de texto **URL de inicio de sesión del proveedor de identidades**, coloque el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) en la ventana de configuración de aplicaciones de Azure AD.

    d. Como **ubicación del identificador de federación**, seleccione el botón de radio **Federation Id is in FEDERATION_ID Attribute element** (El id. de federación está en el elemento de atributo FEDERATION_ID). 

    e. Haga clic en el botón **Guardar** .
  

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**. 



### <a name="creating-a-boomi-test-user"></a>Creación de un usuario de prueba de Boomi

Para permitir que los usuarios de Azure AD inicien sesión en Boomi, tienen que aprovisionarse en Boomi. En el caso de Boomi, el aprovisionamiento es una tarea manual.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:

1. Inicie sesión en su sitio de la compañía de Boomi como administrador.

2. Después de iniciar sesión, vaya a **User Management** (Administración de usuarios) y vaya a **Users** (Usuarios).

    ![Usuarios](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "Usuarios")

3. Haga clic en el icono **+**, se abre el cuadro de diálogo **Add/Maintain User Roles** (Agregar o mantener roles de usuario).

    ![Usuarios](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "Usuarios")

    ![Usuarios](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "Usuarios")

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

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y seleccione **Aplicaciones empresariales**. Después, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Boomi**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_50.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    


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

[100]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png
