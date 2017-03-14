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
ms.sourcegitcommit: 9c027decf5d982519bc9f41aa9588fa431ef3975
ms.openlocfilehash: 17ad8938cbee539e74f87210077a12c72e777edc
ms.lasthandoff: 02/24/2017


---
# <a name="tutorial-azure-ad-integration-with-slack"></a>Tutorial: Integración de Azure AD con Slack

En este tutorial, obtendrá información sobre cómo integrar Slack con Azure Active Directory (Azure AD).

Al integrar Slack con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Slack.
* Permitir a los usuarios iniciar sesión automáticamente en Slack con inicio de sesión único con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central, Azure Portal.

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Slack, se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción de inicio de sesión único de Slack habilitada

> [!NOTE]
> No se recomienda probar los pasos de este tutorial con un entorno de producción.

Para probar los pasos de este tutorial, siga estas recomendaciones:

* Solo si es necesario, utilice el entorno de producción.
* Si no dispone de un entorno de prueba de Azure AD, obtenga una [versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único (SSO) de Azure AD en un entorno de prueba. El escenario que hay que seguir consta de dos bloques principales:

* Adición de Slack desde la galería
* Configuración y prueba del inicio de sesión único de Azure AD

## <a name="add-slack-from-the-gallery"></a>Adición de Slack desde la galería
Para configurar la integración de Slack con Azure AD, agregue Slack desde la galería a la lista de aplicaciones SaaS administradas de la manera siguiente:

1. Abra el [Azure Portal](https://portal.azure.com).
2. En el panel izquierdo, haga clic en el botón **Azure Active Directory**.

    ![Botón "Azure Active Directory"][1]

3. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Botón "Todas las aplicaciones" en la hoja "Aplicaciones empresariales"][2]

4. En la parte superior del cuadro de diálogo **Todas las aplicaciones**, haga clic en **Agregar**.

    ![Botón "Agregar" del cuadro de diálogo "Todas las aplicaciones"][3]

5. En el cuadro de búsqueda, escriba **Slack**.

    ![Cuadro de búsqueda "Agregar una aplicación"](./media/active-directory-saas-slack-tutorial/tutorial_slack_000.png)

6. En el panel de resultados, seleccione **Slack** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![En el panel de resultados, seleccione "Slack".](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y prueba del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único (SSO) de Azure AD con Slack con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe establecer una relación de vínculo entre el usuario de Azure AD y su homólogo en Slack. Establece esta relación de vínculo al asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Slack.

Para configurar y probar el inicio de sesión único de Azure AD con Slack, es preciso completar los siguientes bloques de creación:

1. [Configure el inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on) para permitir al usuario usar esta característica.
2. [Cree un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user) para probar el inicio de sesión único de Azure AD con el usuario Britta Simon.
3. [Cree un usuario de prueba de Slack](#creating-a-slack-test-user) para dar a ese usuario Britta Simon de Azure AD un homólogo de Slack para vincularse.
4. [Asigne el usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user) para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. [Pruebe el inicio de sesión único](#testing-single-sign-on) para comprobar que la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Slack de la siguiente manera:

1. En Azure Portal, en la página de integración de la aplicación **Slack**, haga clic en **Inicio de sesión único**.

    ![Página de integración de la aplicación Slack][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en la lista **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Cuadro de diálogo "Inicio de sesión único"](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

3. En **Dominio y direcciones URL de Slack**, lleve a cabo los pasos siguientes:

    ![Cuadro de diálogo "Inicio de sesión único"](./media/active-directory-saas-slack-tutorial/tutorial_slack_02.png)

    a. En el cuadro **URL de inicio de sesión**, escriba una dirección URL que utilice la convención de nomenclatura *https://<company name>.slack.com*.

    b. En el cuadro **Identificador**, escriba **https://slack.com**.

    > [!NOTE]
    > Los valores anteriores no son valores reales. Aquí se recomienda usar valores exclusivos para la dirección URL y el identificador. Después, actualizará los valores con la dirección URL y el identificador reales. Para obtener los valores, póngase en contacto con el [equipo de soporte técnico de Slack](https://slack.com/help/contact).

4. La aplicación Slack espera las aserciones de Lenguaje de marcado de aserción de seguridad (SAML), que se mostrará en un formato concreto. Configure las notificaciones y administre los valores de los atributos en la sección **Atributos de usuario** de la página de integración de la aplicación Slack, tal y como se muestra en la siguiente captura de pantalla:

    ![Configuración de notificaciones en la sección Atributos de usuario](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

5. En el cuadro de diálogo **Inicio de sesión único**, en la sección **Atributos de usuario**, seleccione **user.mail** como **Identificador de usuario**. Para cada fila de la tabla, realice los pasos siguientes:

    | Nombre del atributo | Valor de atributo |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a. Haga clic en el botón **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)

    b. En el cuadro de diálogo **Agregar atributo**, en el cuadro **Nombre**, escriba el primer nombre de la columna **Nombre del atributo** de la tabla.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png)

    c. En el cuadro **Valor**, escriba el primer valor de la columna **Valor de atributo** de la tabla.

    d. Haga clic en **Aceptar**.

    e. Repita los pasos "a" a "d" para las tres filas de la tabla siguientes.

6. En **Certificado de firma de SAML**, haga clic en **Crear nuevo certificado**.

    ![Creación de un certificado](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)     

7. En el cuadro de diálogo **Crear nuevo certificado**, haga clic en el botón **Calendario**, seleccione una fecha de caducidad y haga clic en **Guardar**.

    ![Selección de una fecha de caducidad del certificado](./media/active-directory-saas-slack-tutorial/tutorial_general_300.png)

8. En **Certificado de firma de SAML**, active la casilla **Activar el certificado nuevo** y haga clic en **Guardar**.

    ![Activación del certificado de firma SAML](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

9. En la ventana emergente **Certificado de sustitución**, haga clic en **Aceptar**.

    ![Ventana emergente Certificado de sustitución](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

10. En **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en la unidad local.

    ![Almacenamiento del certificado en la unidad local](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

11. En **Configuración de Slack**, haga clic en **Configurar Slack** para abrir la ventana **Configurar inicio de sesión**.

    ![Haga clic en "Configurar Slack" para abrir la ventana Configurar inicio de sesión](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

    ![Ventana "Configurar inicio de sesión"](./media/active-directory-saas-slack-tutorial/tutorial_slack_10.png)

12. Abra una ventana del explorador e inicie sesión en su sitio de la compañía de Slack como administrador.

13. Vaya a **Microsoft Azure AD** y luego a **Configuración del equipo**.

    ![Botón de "Configuración del equipo" de Microsoft Azure AD en el sitio de empresa de Slack](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

14. En **Configuración del equipo**, haga clic en la pestaña **Autenticación** y luego en **Cambiar configuración**.

    ![Botón "Cambiar configuración" en la página Configuración del equipo](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

15. En el cuadro de diálogo **Configuración de la autenticación SAML** , realice los pasos siguientes:

    ![Cuadro de diálogo "Configuración de la autenticación SAML"](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a. En el cuadro **SAML 2.0 Endpoint (HTTP)** [Punto de conexión SAML 2.0 (HTTP)], escriba el valor de **Dirección URL del servicio de inicio de sesión único de SAML** en la ventana de configuración de la aplicación Azure AD.

    b. En el cuadro **Identity Provider Issuer** (Emisor del proveedor de identidades), especifique el valor de **ID de identidad de SAML** en la ventana de configuración de aplicaciones de Azure AD.

    c. Abra el certificado descargado en el Bloc de notas, copie el contenido y péguelo en el cuadro **Certificado público**.

    d. Configure las tres opciones anteriores según corresponda para su equipo de Slack. Para más información acerca de la configuración, consulte [Guide to single sign-on with Slack](https://get.slack.help/hc/en-us/articles/220403548-Guide-to-single-sign-on-with-Slack) (Guía de inicio de sesión único con Slack).

    e. Haga clic en **Guardar configuración**.

    <!-- Anule la selección de **Permitir a los usuarios cambiar su dirección de correo electrónico**.

    e. Seleccione **Permitir a los usuarios elegir su propio nombre de usuario**.

    f. En **Authentication for your team must be used by** (Miembros de su equipo que tienen que usar la autenticación) seleccione **It’s optional** (Es opcional). -->


### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en Azure Portal de la siguiente manera:

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón "Azure Active Directory"](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png)

2. Vaya a **Usuarios y grupos** y luego en **Todos los usuarios** para mostrar la lista de usuarios.

    ![Botón "Todos los usuarios" de Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar**.

    ![Botón "Agregar" en el cuadro de diálogo Agregar usuarios](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario**, escriba la siguiente información:

    ![Cuadro de diálogo "Usuario"](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Seleccione la casilla **Mostrar contraseña** y, después , anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.

### <a name="create-a-slack-test-user"></a>Creación de un usuario de prueba de Slack

En esta sección, se crea un usuario llamado a Britta Simon en Slack. Slack admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Si el usuario ya no existe en Slack, se crea uno nuevo cuando se intenta acceder a Slack.

> [!NOTE]
> Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de Slack](https://slack.com/help/contact).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará al usuario Britta Simon para que use el inicio de sesión único de Azure, para lo cual le concederá acceso a Slack.

![Asignación de un usuario de inicio de sesión único de Azure][200]

Para asignar el usuario Britta Simon a Slack, realice los pasos siguientes:

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio, vaya a **Aplicaciones empresariales** y haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201]

2. En la lista de **aplicaciones**, seleccione **Slack**.

    ![Lista de aplicaciones de Azure Portal](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. En el panel izquierdo, haga clic en **Usuarios y grupos**.

    ![Botón "Usuarios y grupos" del panel izquierdo][202]

4. Haga clic en el botón **Agregar** y, después, en la hoja **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Botón "Agregar" y hoja "Agregar asignación"][203]

5. En el cuadro de diálogo **Usuarios y grupos**, en la lista **Usuarios** seleccione **Britta Simon**.

6. Haga clic en el botón **Seleccionar**.

7. En la hoja **Agregar asignación**, haga clic en el botón **Asignar**.

### <a name="test-sso"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el panel de acceso.

Para probar la configuración, vaya al Panel de acceso y, a continuación, haga clic en el icono de **Slack**. El usuario debe iniciar sesión automáticamente en la aplicación Slack.


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

