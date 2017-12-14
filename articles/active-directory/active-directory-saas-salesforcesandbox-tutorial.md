---
title: "Tutorial: Integración de Azure Active Directory con Salesforce Sandbox | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Salesforce Sandbox."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 801088bd63f025ca7cb1f9e4fe66b9c6f7f93453
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integración de Azure Active Directory con Salesforce Sandbox

En este tutorial, aprenderá a integrar Salesforce Sandbox con Azure Active Directory (Azure AD).

La integración de Salesforce Sandbox con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Salesforce Sandbox.
- Puede permitir que los usuarios inicien sesión automáticamente en Salesforce Sandbox (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Salesforce Sandbox, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Salesforce Sandbox

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Salesforce Sandbox desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Adición de Salesforce Sandbox desde la galería
Para configurar la integración de Salesforce Sandbox, deberá agregar Salesforce Sandbox desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Salesforce Sandbox desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Salesforce Sandbox**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_search.png)

5. En el panel de resultados, seleccione **Salesforce Sandbox** y, luego, haga clic en **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Salesforce Sandbox con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Salesforce Sandbox para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Salesforce Sandbox.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Salesforce Sandbox.

Para configurar y probar el inicio de sesión único de Azure AD con Salesforce Sandbox, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Salesforce Sandbox](#creating-a-salesforce-sandbox-test-user)**: el objetivo es tener un homólogo de Britta Simon en Salesforce Sandbox que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Salesforce Sandbox.

**Para configurar el inicio de sesión único de Azure AD con Salesforce Sandbox, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Salesforce Sandbox** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. En la sección **Dominio y direcciones URL de Salesforce Sandbox**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_url.png)

     En el cuadro de texto **URL de inicio de sesión**, escriba el valor con el siguiente patrón: `https://<subdomain>.my.salesforce.com`

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de atención al cliente de Salesforce Sandbox](https://help.salesforce.com/support) para obtener este valor.


4. Si ya configuró un inicio de sesión único para otra instancia de Salesforce Sandbox en su directorio, también debe configurar el **Identificador** para que tenga el mismo valor que la **URL de inicio de sesión**. 
    
    >[!Note]
    >El campo **Identificador** puede encontrarse al activar la casilla **Mostrar configuración avanzada** en la página **Configure App URL** (Configurar dirección URL de la aplicación) del cuadro de diálogo. 


5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_400.png)

7. En la sección **Configuración de Salesforce Sandbox**, haga clic en **Configurar Salesforce Sandbox** para abrir la ventana **Configurar inicio de sesión**. Copie **SAML Entity ID and SAML Single Sign-On Service URL** (URL del servicio de inicio de sesión único de SAML e Identificador de entidad de SAML) de la sección **Referencia rápida**.

    ![Configuración del inicio de sesión único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_configure.png) 
<CS>
8. Abra una nueva pestaña en el explorador e inicie sesión en su cuenta de administrador de Salesforce Sandbox.

9. En el menú de la parte superior, haga clic en **Configuración**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781024.png)
10. En el panel de navegación de la izquierda, haga clic en **Controles de seguridad** y luego haga clic en **Configuración de inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781025.png)
11. En la sección Configuración de inicio de sesión único, siga estos pasos: ![Configurar el inicio de sesión único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781026.png)
     
     a.  Seleccione **SAML habilitado**. 

     b.  Haga clic en **Nuevo**.

12. En la sección Configuración del inicio de sesión único de SAML siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781027.png)

    En el cuadro de texto Name (Nombre), escriba el nombre de la configuración (por ejemplo, *SPSSOWAAD\_Test*). 

    b. Pegue el valor de **id. de entidad de SMAL** en el cuadro de texto **Issuer** (Emisor).

    c. En el cuadro de texto **Entity Id** (Id. de entidad), escriba **https://test.salesforce.com** si se trata de la primera instancia de Salesforce Sandbox que va a agregar a su directorio. Si ya agregó una instancia de Salesforce Sandbox, para el **Id. de entidad** escriba la **URL de inicio de sesión**, que debe tener este formato: `http://company.my.salesforce.com`  
 
    d. Haga clic en **Examinar** para cargar el certificado descargado.  

    e. Como **SAML Identity Type** (Tipo de identidad SAML), seleccione **Assertion contains the Federation ID from the User object** (La aserción contiene el id. de federación del objeto Usuario).
 
    f. Como **SAML Identity Location**(Ubicación de identidad de SAML), seleccione **Identity is in the NameIdentifier element of the Subject statement** (La identidad está en el elemento NameIdentifier de la instrucción de sujeto).

    g. Pegue la **dirección URL de servicio de inicio de sesión único** en el cuadro de texto **Identity Provider Login URL** (URL de inicio de sesión del proveedor de identidades). 

    h. SFDC no admite el cierre de sesión SAML.  Como alternativa, pegue "https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0" en el cuadro de texto **URL de cierre de sesión del proveedor de identidades**.

    i. Para **Service Provider Initiated Request Binding** (Enlace de solicitud iniciada por el proveedor de servicio), seleccione **HTTP POST** (Método HTTP POST). 

    j. Haga clic en **Guardar**.

### <a name="enable-your-domain"></a>Habilitación del dominio
En esta sección se supone que ya ha creado un dominio.  Para más información, consulte [Defining Your Domain Name](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US) (Definición del nombre de dominio).

**Realice los pasos siguientes para habilitar su dominio:**

1. En el panel de navegación de la izquierda, haga clic en **Administración de dominios** y, luego, en **Mi dominio**.
   
     ![Configurar inicio de sesión único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781029.png)
   
   >[!NOTE]
   >Asegúrese de que su dominio se ha configurado correctamente. 

2. En la sección **Configuración de la página de inicio de sesión**, haga clic en**Editar**, luego, como **Servicio de autenticación**, seleccione el nombre de la configuración de inicio de sesión único de SAML en la sección anterior y finalmente haga clic en **Guardar**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781030.png)

Tan pronto como tenga un dominio configurado, los usuarios deberán usar la dirección URL de dominio para iniciar sesión en el espacio aislado de Salesforce.  

Para obtener el valor de la dirección URL, haga clic en el perfil de SSO que haya creado en la sección anterior.    

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-salesforce-sandbox-test-user"></a>Creación de un usuario de prueba de Salesforce Sandbox

En esta sección, creará un usuario llamado a Britta Simon en Salesforce Sandbox. Salesforce Sandbox admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.
No hay ningún elemento de acción para usted en esta sección. Si un usuario ya no existe en Salesforce Sandbox, se crea uno nuevo cuando se intenta acceder a esta aplicación.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Salesforce Sandbox.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Salesforce Sandbox, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Salesforce Sandbox**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

Si desea probar la configuración de inicio de sesión único (SSO), abra el panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del aprovisionamiento de usuarios](active-directory-saas-salesforce-sandbox-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_203.png

