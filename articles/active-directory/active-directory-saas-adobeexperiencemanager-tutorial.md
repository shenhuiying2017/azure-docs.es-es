---
title: "Tutorial: Integración de Azure Active Directory con Adobe Experience Manager | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Adobe Experience Manager."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: c366e314b77cd3344a90826b22b96a45e35b0b4e
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Tutorial: Integración de Azure Active Directory con Adobe Experience Manager

En este tutorial, aprenderá a integrar Adobe Experience Manager con Azure Active Directory (Azure AD).

La integración de Adobe Experience Manager con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a Adobe Experience Manager.
- Puede habilitar que los usuarios inicien sesión automáticamente en Adobe Experience Manager con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Adobe Experience Manager, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en Adobe Experience Manager

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, [obtenga una versión de prueba gratuita durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Adobe Experience Manager desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-adobe-experience-manager-from-the-gallery"></a>Incorporación de Adobe Experience Manager desde la galería
Para configurar la integración de Adobe Experience Manager en Azure AD, será preciso que lo agregue desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Adobe Experience Manager desde la galería, realice los pasos siguientes:**

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), seleccione el icono **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Adobe Experience Manager**. En el panel de resultados, seleccione **Adobe Experience Manager** y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Adobe Experience Manager en la lista de resultados](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Adobe Experience Manager utilizando un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Adobe Experience Manager para un usuario de Azure AD. Es decir, es necesario establecer un vínculo entre un usuario de Azure AD y el usuario relacionado de Adobe Experience Manager.

En Adobe Experience Manager, asigne al valor **Nombre de usuario** el mismo valor de **nombre de usuario** en Azure AD. Ahora ya ha establecido el vínculo entre los dos usuarios. 

Para configurar y probar el inicio de sesión único de Azure AD con Adobe Experience Manager, complete los siguientes bloques de creación:

1. [Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on), para permitir que los usuarios utilicen esta característica.
2. [Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user), para probar el inicio de sesión único de Azure AD con Britta Simon.
3. [Cree un usuario de prueba en Adobe Experience Manager](#create-an-adobe-experience-manager-test-user) para tener un homólogo de Britta Simon en Adobe Experience Manager que esté vinculado a su representación en Azure AD.
4. [Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user), para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. [Prueba del inicio de sesión único](#test-single-sign-on), para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Adobe Experience Manager.

**Para configurar el inicio de sesión único de Azure AD con Adobe Experience Manager, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Adobe Experience Manager**, seleccione **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en el menú desplegable **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. En la sección **Dominio y direcciones URL de Adobe Experience Manager**, realice los siguientes pasos, si desea configurar la aplicación en el modo **IdP**:

    ![Información de direcciones URL de inicio de sesión único y dominio de Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. En el cuadro de texto **Identificador**, escriba un valor único que se define también en el servidor AEM. 

    b. En el cuadro **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<AEM Server Url>/saml_login`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Para obtener estos valores, póngase en contacto con el [equipo de soporte técnico de Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html).
 
4. Active **Mostrar configuración avanzada de URL**. Después, realice los siguientes pasos si quiere configurar la aplicación en modo iniciado por **SP**:

    ![Información de direcciones URL de inicio de sesión único y dominio de Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    En el cuadro **Dirección URL de inicio de sesión**, escriba la dirección URL de servidor de Adobe Experience Manager. 

5. En la sección **Certificado de firma de SAML**, seleccione **Certificado (Base64)**. A continuación, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. Para abrir la ventana Configurar inicio de sesión, en la sección Configuración de Adobe Experience Manager, seleccione **Configurar Adobe Experience Manager**. Copie la **dirección URL del servicio de inicio de sesión de SAML**, el **identificador de entidad de SAML** y el **identificador de cierre de sesión** de la sección Referencia rápida.

    ![Vínculo a la sección de configuración](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Seleccione **Guardar**.

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. En otra ventana del explorador, abra el portal de administración de **Adobe Experience Manager**.

9. Seleccione **Settings** >  (Configuración) **Security** >  (Seguridad) **Users** (Usuarios).

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Seleccione **Administrator** (Administrador) o cualquier otro usuario pertinente.

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Seleccione **Account Settings** > **Manage TrustStore** (Configuración de la cuenta > Administrar TrustStore).

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. En **Add Certificate from CER file** (Agregar certificado desde archivo CER), haga clic en **Select Certificate File** (Seleccionar archivo de certificado). Busque el archivo de certificado que ha descargado de Azure Portal y selecciónelo.

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. El certificado se agrega a TrustStore. Anote el alias del certificado.

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. En la página **Users** (Usuarios), seleccione **authentication-service**.

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Seleccione **Account Settings** >  (Configuración de la cuenta) **Create/Manage KeyStore** (Crear o administrar KeyStore). Proporcione una contraseña para crear KeyStore.

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Vuelva a la pantalla de administración. Después, seleccione **Settings** > **Operations** > **Web Console** (Configuración > Operaciones > Consola web).

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Se abre la página de configuración.

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

17. Busque **Adobe Granite SAML 2.0 Authentication Handler** (Controlador de autenticación de Adobe Granite SAML 2.0). Después, seleccione el botón **Agregar**.

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. En esta página, realice las acciones siguientes.

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. En el cuadro **Path** (Ruta de acceso), escriba  **/**.

    b. En el cuadro **IDP URL** (URL de IDP), escriba el valor de **SAML Sign-On Service URL** (URL de servicio de inicio de sesión único de SAML) que copió de Azure Portal.

    c. En el cuadro **IDP Certificate Alias** (Alias de certificado de IDP), introduzca el valor del **alias de certificado** que agregó en TrustStore.

    d. En el cuadro de texto **Security Provided Entity ID** (Identificador de entidad de seguridad proporcionado), escriba el valor único de **SAM Entity ID** (Identificador de entidad SAML), que se ha configurado en Azure Portal.

    e. En el cuadro **Assertion Consumer Service URL** (URL del Servicio de consumidor de aserciones), escriba el valor de **Reply URL** (URL de respuesta) que se ha configurado en Azure Portal.

    f. En el cuadro **Password of Key Store** (Contraseña del almacén de claves), escriba la **contraseña**, que ha establecido en el almacén de claves.

    g. En el cuadro **User Attribute ID** (Identificador de atributo de usuario), escriba **Name ID** (Identificador de nombre) u otro identificador de usuario que sean pertinentes en su caso.

    h. Seleccione **Autocreate CRX Users** (Crear automáticamente usuarios de CRX).

    i. En el cuadro **Logout URL** (URL de cierre de sesión), pegue la dirección de la **URL de cierre de sesión** que obtuvo de Azure Portal.

    j. Seleccione **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación. Después de agregar esta aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, seleccione la pestaña **Inicio de sesión único**. A continuación, consulte la documentación insertada en la sección **Configuración** en la parte inferior. Puede leer más sobre la característica de documentación insertada en la [documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, seleccione el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, seleccione **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Para abrir el cuadro de diálogo **Usuario**, en la parte superior del cuadro de diálogo **Todos los usuarios**, seleccione **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario**, realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Show Password** (Mostrar contraseña). Y, después, anote el valor que se muestra en el cuadro **Password** (Contraseña).

    d. Seleccione **Crear**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Creación de un usuario de prueba de Adobe Experience Manager

En esta sección, se crea un usuario denominado Britta Simon en Adobe Experience Manager. Si ha seleccionado la opción **Autocreate CRX Users** (Crear automáticamente usuarios de CRX), los usuarios se crearán automáticamente tras la autenticación correcta. 

Si desea crear los usuarios manualmente, trabaje con el [equipo de soporte técnico de Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) para agregar los usuarios en la plataforma de Adobe Experience Manager. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, se habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Adobe Experience Manager.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Adobe Experience Manager, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones. Después, vaya a la vista de directorios, seleccione **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Adobe Experience Manager**.

    ![Vínculo a Adobe Experience Manager en la lista de aplicaciones](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. En el menú de la izquierda, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Seleccione el botón **Agregar**. Después, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon**.

6. En el cuadro de diálogo **Usuarios y grupos**, haga clic en el botón **Seleccionar**.

7. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el panel de acceso.

Al seleccionar el icono de Adobe Experience Manager en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Adobe Experience Manager.

Para más información sobre el panel de acceso, consulte [Introducción al panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_203.png

