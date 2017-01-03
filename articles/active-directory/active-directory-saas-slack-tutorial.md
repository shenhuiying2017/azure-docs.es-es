---
title: "Tutorial: Integración de Azure Active Directory con Slack | Microsoft Docs"
description: "Aprenda a usar Slack con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c7a0b761-75b7-4e6b-9980-71d645643a78
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4d5144c6a690c2338dec45b27dcb308328b6fecb
ms.openlocfilehash: 37440a8ba397c4dc227a448dfa574cebd14be49c


---

# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Tutorial: Integración de Azure Active Directory con Slack

El objetivo de este tutorial es mostrar cómo integrar Slack con Azure Active Directory (Azure AD).

La integración de Slack con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Slack
- Puede permitir que los usuarios inicien sesión automáticamente en Slack (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Slack, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Slack


> [!NOTE]
>  Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Slack desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-slack-from-the-gallery"></a>Adición de Slack desde la galería
Para configurar la integración de Slack en Azure AD, tendrá que agregar Slack desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Slack desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
    
    ![Applications][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
    
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Slack**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

7. En el panel de resultados, seleccione **Slack** y luego haga clic en **Completar** para agregar la aplicación.

    ![Selección de la aplicación en la galería](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Slack utilizando un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD tiene que saber cuál es el usuario homólogo de Slack para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Slack.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Slack.

Para configurar y probar el inicio de sesión único de Azure AD con Slack, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Slack](#creating-a-slack-test-user)**: para tener un homólogo de Britta Simon en Slack que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Slack.

La aplicación Slack espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar el valor de estos atributos desde la pestaña**Atributo**de la aplicación. La siguiente captura de pantalla le muestra un ejemplo de esto. 

![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

**Para configurar el inicio de sesión único de Azure AD con Slack, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Slack**, en el menú de la parte superior, haga clic en **Atributos**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

2. En el cuadro de diálogo **Atributos de token de SAML** , para cada fila de la tabla siguiente, realice los pasos que se indican a continuación:

    | Nombre del atributo | Valor de atributo |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a. Haga clic en **agregar atributo de usuario** para abrir el cuadro de diálogo **Agregar atributo de usuario**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)
    
    b. En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo que se muestra para la fila.
    
    c. En la lista **Valor de atributo**, escriba el valor de atributo que se muestra para esa fila.
    
    d. Haga clic en **Completar**

3. En el menú de la parte superior, haga clic en **Inicio rápido**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png) 

4. En la página **¿Cómo desea que los usuarios inicien sesión en Slack?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)

5. En el cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes y haga clic en **Siguiente**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<company name>.slack.com`.

    b. Haga clic en **Siguiente**.

    > [!NOTE] 
    > Tenga en cuenta que tiene que actualizar este valor con el valor real de URL de inicio de sesión. Para obtener este valor, póngase en contacto con el equipo de soporte técnico de Slack.

6. En la página **Configurar inicio de sesión único en Slack**, haga clic en **Descargar certificado** y guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

7.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Slack como administrador.

8.  Vaya a **Microsoft Azure AD** y luego a **Configuración del equipo**.

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

9.  En la sección **Configuración del equipo**, haga clic en la pestaña **Autenticación** y luego en **Cambiar configuración**.

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

10. En el cuadro de diálogo **Configuración de la autenticación SAML** , realice los pasos siguientes:

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.  En el cuadro de texto **SAML 2.0 Endpoint (HTTP)** [Punto de conexión SAML 2.0 (HTTP)], coloque el valor de **Dirección URL de inicio de sesión único de SAML** del Asistente para configuración de la aplicación Azure AD.

    b.  En el cuadro de texto **Identity Provider Issuer** (Emisor del proveedor de identidades), coloque el valor de **URL del emisor** del Asistente para configuración de aplicaciones de Azure AD.

    c.  Abra el archivo de certificado descargado en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado público**.

    d.  Anule la selección de **Permitir a los usuarios cambiar su dirección de correo electrónico**.

    e.  Seleccione **Permitir a los usuarios elegir su propio nombre de usuario**.

    f.  En **Authentication for your team must be used by** (Miembros de su equipo que tienen que usar la autenticación) seleccione **It’s optional** (Es opcional).

    g.  Haga clic en **Guardar configuración**.

11. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
    
    ![Inicio de sesión único de Azure AD ][10]

12. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
    
    ![Inicio de sesión único de Azure AD ][11]



### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_09.png)

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_05.png)

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_06.png)

    a. En el cuadro de texto **Nombre**, escriba **Britta**.  

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_07.png)

8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_08.png)

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.   



### <a name="creating-a-slack-test-user"></a>Creación de usuario de prueba de Slack

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Slack. Slack admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a Slack se creará un nuevo usuario, en caso de que no exista.

> [!NOTE] 
> Si necesita crear manualmente un usuario, es preciso que se ponga en contacto con el equipo de soporte técnico de Slack.


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Slack.
    
![Asignar usuario][200]

**Para asignar a Britta Simon a Slack, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
    
    ![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Slack**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. En el menú de la parte superior, haga clic en **Usuarios**.
    
    ![Asignar usuario][203]

4. En la lista Usuarios, seleccione **Britta Simon**.

5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
    
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.
 
Al hacer clic en el icono de Slack en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Slack.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-slack-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-slack-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-slack-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-slack-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-slack-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


