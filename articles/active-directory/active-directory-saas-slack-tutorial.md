---
title: "Tutorial: Integración de Azure Active Directory con Slack | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Slack."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 14972d3e1212fb0cf6653bd4a693470425294d2c
ms.openlocfilehash: c975231ea18c7c1853d9b20fc49542c10ef9abcc


---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Tutorial: Integración de Azure Active Directory con Slack

En este tutorial, obtendrá información sobre cómo integrar Slack con Azure Active Directory (Azure AD).

La integración de Slack con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Slack
- Puede permitir que los usuarios inicien sesión automáticamente en Slack (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Slack, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Slack


> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Slack desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-slack-from-the-gallery"></a>Adición de Slack desde la galería
Para configurar la integración de Slack en Azure AD, tendrá que agregar Slack desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Slack desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Slack**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_000.png)

5. En el panel de resultados, seleccione **Slack** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Slack con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Slack para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Slack.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Slack.

Para configurar y probar el inicio de sesión único de Azure AD con Slack, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Slack](#creating-a-slack-test-user)**: para tener un homólogo de Britta Simon en Slack que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación Slack.

**Para configurar el inicio de sesión único de Azure AD con Slack, realice los pasos siguientes:**

1. En el Portal de administración de Azure, en la página de integración de la aplicación **Slack**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

3. En la sección **Dominio y direcciones URL de Slack**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_02.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<company name>.slack.com`.

    b. En el cuadro de texto **Identificador**, escriba: `https://slack.com`

    > [!NOTE] 
    > Tenga en cuenta que estos no son valores reales. Tendrá que actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Aquí le recomendamos que utilice el valor único de URL en el identificador. Póngase en contacto con el [equipo de soporte técnico de Slack](https://slack.com/help/contact) para obtener estos valores. 

4. La aplicación Slack espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

5. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, seleccione **user.mail** como **identificador de usuario**, y para cada fila se muestra en la tabla siguiente, realice los pasos siguientes:
    
    | Nombre del atributo | Valor de atributo |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**

6. En la sección **Certificado de firma de SAML**, haga clic en **Crear nuevo certificado**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)     

7. En el cuadro de diálogo **Crear nuevo certificado**, haga clic en el icono del calendario y seleccione una valor en **Fecha de expiración**. Luego haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_general_300.png)

8. En la sección **Certificado de firma de SAML**, seleccione **Make new certificate active** (Activar el nuevo certificado) y haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

9. En la ventana emergente **Rollover certificate** (Certificado de sustitución), haga clic en **Aceptar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

10. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png) 

11. En la sección **Configuración de Slack**, haga clic en **Configurar Slack** para abrir la ventana **Configurar inicio de sesión**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png) 

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_10.png)

12.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Slack como administrador.

13.  Vaya a **Microsoft Azure AD** y luego a **Configuración del equipo**.

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

14.  En la sección **Configuración del equipo**, haga clic en la pestaña **Autenticación** y luego en **Cambiar configuración**.

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

15. En el cuadro de diálogo **Configuración de la autenticación SAML** , realice los pasos siguientes:

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.  En el cuadro de texto **SAML 2.0 Endpoint (HTTP)** (Punto de conexión SAML 2.0 [HTTTP]), coloque el valor de **Dirección URL del servicio de inicio de sesión único** en la ventana de configuración de aplicaciones de Azure AD.

    b.  En el cuadro de texto **Identity Provider Issuer** (Emisor del proveedor de identidades), coloque el valor de **ID de identidad de SAML** de la ventana de configuración de aplicaciones de Azure AD.

    c.  Abra el archivo de certificado descargado en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado público**.

    d. Configure las tres opciones anteriores según corresponda para su equipo de Slack. Para obtener más información sobre la configuración, busque la **Guía de configuración de SSO de Slack** aquí. `https://get.slack.help/hc/en-us/articles/220403548-Guide-to-single-sign-on-with-Slack`

    e.  Haga clic en **Guardar configuración**.
     
    <!-- Anule la selección de **Permitir a los usuarios cambiar su dirección de correo electrónico**.

    e.  Seleccione **Permitir a los usuarios elegir su propio nombre de usuario**.

    f.  En **Authentication for your team must be used by** (Miembros de su equipo que tienen que usar la autenticación) seleccione **It’s optional** (Es opcional). -->
  

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**. 



### <a name="creating-a-slack-test-user"></a>Creación de usuario de prueba de Slack

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Slack. Slack admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a Slack se creará un nuevo usuario, en caso de que no exista.

> [!NOTE]
> Si necesita crear manualmente un usuario, es preciso que se ponga en contacto con el [equipo de soporte técnico de Slack](https://slack.com/help/contact).


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Slack.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Slack, realice los pasos siguientes:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y seleccione **Aplicaciones empresariales**. Después, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Slack**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    


### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Slack en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Slack.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png


<!--HONumber=Feb17_HO1-->


