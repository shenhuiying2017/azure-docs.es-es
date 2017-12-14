---
title: "Tutorial: Integración de Azure Active Directory con Kantega SSO para Confluence | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Kantega SSO para Confluence."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d0d99c14-a6ca-45f2-bb84-633126095e7a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: d98aad7d2b4bb26dbd04088ffd133fe96ffe0dd5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Tutorial: Integración de Azure Active Directory con Kantega SSO para Confluence

En este tutorial obtendrá información sobre cómo integrar Kantega SSO para Confluence con Azure Active Directory (Azure AD).

La integración de Kantega SSO para Confluence con Azure AD le proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a Kantega SSO para Confluence.
- Puede permitir que los usuarios inicien sesión automáticamente en Kantega SSO para Confluence (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Kantega SSO para Confluence, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único de Kantega SSO para Confluence

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Kantega SSO para Confluence desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Incorporación de Kantega SSO para Confluence desde la galería
Para configurar la integración de Kantega SSO para Confluence en Azure AD, tiene que agregar Kantega SSO para Confluence desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Kantega SSO para Confluence desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Kantega SSO para Confluence**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_search.png)

5. En el panel de resultados, seleccione **Kantega SSO para Confluence** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Kantega SSO para Confluence con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Kantega SSO para Confluence para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Kantega SSO para Confluence.

Para establecer la relación de vínculo, en Kantega SSO para Confluence, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Kantega SSO para Confluence, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Kantega SSO para Confluence](#creating-a-kantega-sso-for-confluence-test-user)**: para tener un homólogo de Britta Simon en Kantega SSO para Confluence que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Kantega SSO para Confluence.

**Para configurar el inicio de sesión único de Azure AD con Kantega SSO para Confluence, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Kantega SSO para Confluence**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_samlbase.png)

3. En el modo de inicio de **IDP**, en la sección **Dominio y direcciones URL de Kantega SSO para Confluence** realice el paso siguiente:

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_url1.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`.

4. En el modo de inicio de **SP**, active la casilla **Mostrar configuración avanzada de URL** y realice el siguiente paso:

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_url2.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Estos valores se reciben durante la configuración del complemento de Confluence, que se explica más adelante en el tutorial.

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_general_400.png)
    
7. En otra ventana del explorador web, regístrese en el **Portal de administración de Confluence** como administrador.

8. Mantenga el mouse encima del icono de engranaje y haga clic en **Complementos**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon1.png)

9. En la pestaña **ATLASSIAN MARKETPLACE** (MARKETPLACE DE ATLASSIAN), haga clic en **Find new add-ons** (Buscar nuevos complementos). 

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon.png)

10. Busque **Kantega SSO para Confluence (SAML Kerberos)** y haga clic en el botón **Instalar** para instalar el nuevo complemento SAML.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon2.png)

11. Se inicia la instalación del complemento.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon3.png)

12. Una vez completada la instalación. Haga clic en **Cerrar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon33.png)

13. Haga clic en **Administrar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon34.png)
    
14. Haga clic en **Configurar** para configurar el nuevo complemento.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon35.png)

15. Este nuevo complemento también puede encontrarse en la pestaña **USERS & SECURITY** (USUARIOS Y SEGURIDAD).

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon36.png)
    
16. En la sección **SAML**. Seleccione **Azure Active Directory (Azure AD)** en la lista desplegable **Agregar proveedor de identidades**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon4.png)

17. Seleccione el nivel de suscripción **Básica**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon5.png)       

18. En la sección **Agregar propiedades**, siga estos pasos: 

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon6.png)

    a. Copie el valor **URI de id. de aplicación** y úselo como **Identificador, Dirección URL de respuesta y Dirección URL de inicio de sesión** en la sección **Dominio y direcciones URL de Kantega SSO para Confluence** en Azure Portal.

    b. Haga clic en **Siguiente**.

19. En la sección **Importar metadatos**, siga estos pasos: 

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon7.png)

    a. Seleccione **Archivo de metadatos en el equipo** y cargue el archivo de metadatos que descargó desde Azure Portal.

    b. Haga clic en **Siguiente**.

20. En la sección**Name and SSO location** (Nombre y ubicación de SSO), siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon8.png)
    
    a. Agregue el nombre del proveedor de identidades en el cuadro de texto **Nombre del proveedor de identidades** (por ejemplo, Azure AD).

    b. Haga clic en **Siguiente**.

21. Compruebe el certificado de firma y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon9.png)

22. En la sección **Cuentas de usuario de Confluence**, siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon10.png)

    a. Seleccione **Create users in Confluence's internal Directory if needed** (Crear usuarios en el directorio interno de Confluence si es necesario) y escriba el nombre adecuado del grupo de usuarios (puede ser un número múltiple de grupos separados por coma).

    b. Haga clic en **Siguiente**.

23. Haga clic en **Finalizar**   

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon11.png)

24. En la sección **Known domains for Azure AD** (Dominios conocidos para Azure AD), siga estos pasos: 

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/addon12.png)

    a. Seleccione **Known domains** (Dominios conocidos) en el panel izquierdo de la página.

    b. Escriba el nombre de dominio en el cuadro de texto **Known domains** (Dominios conocidos).

    c. Haga clic en **Guardar**. 

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-kantegassoforconfluence-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-kantegassoforconfluence-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-kantegassoforconfluence-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-kantegassoforconfluence-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-kantega-sso-for-confluence-test-user"></a>Creación de un usuario de prueba de Kantega SSO para Confluence

Para permitir que los usuarios de Azure AD inicien sesión en Confluence, tienen que aprovisionarse en Confluence. En el caso de Kantega SSO para Confluence, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en el sitio de la empresa de Kantega SSO para Confluence como administrador.

2. Mantenga el mouse encima del icono de engranaje y haga clic en **Administración de usuarios**.

    ![Agregar empleado](./media/active-directory-saas-kantegassoforconfluence-tutorial/user1.png) 

3. En la sección Usuarios, haga clic en la pestaña **Agregar usuarios**. En la página del cuadro de diálogo **Agregar un usuario**, realice los siguientes pasos:

    ![Agregar empleado](./media/active-directory-saas-kantegassoforconfluence-tutorial/user2.png) 

    a. En el cuadro de texto **Nombre de usuario**, escriba el correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

    b. En el cuadro de texto **Nombre completo**, escriba el nombre completo de un usuario, por ejemplo, Britta Simon.

    c. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

    d. En el cuadro de texto **Contraseña**, escriba la contraseña del usuario.

    e. Haga clic en **Confirmar contraseña** y vuelva a escribir la contraseña.
    
    f. Haga clic en el botón **Agregar**.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Kantega SSO para Confluence.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Kantega SSO para Confluence, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Kantega SSO para Confluence**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Kantega SSO para Confluence en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Kantega SSO para Confluence.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kantegassoforconfluence-tutorial/tutorial_general_203.png

