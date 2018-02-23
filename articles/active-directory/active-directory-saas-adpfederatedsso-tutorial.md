---
title: "Tutorial: Integración de Azure Active Directory con ADP Federated SSO | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y ADP Federated SSO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: jeedes
ms.openlocfilehash: 76bbee514eac36aec8eaf84e8a20f85a27559fc7
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adp-federated-sso"></a>Tutorial: Integración de Azure Active Directory con ADP Federated SSO

En este tutorial, obtendrá información sobre cómo integrar ADP Federated SSO con Azure Active Directory (Azure AD).

La integración de ADP Federated SSO con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a ADP Federated SSO.
- Puede permitir que los usuarios inicien sesión automáticamente en ADP Federated SSO (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con ADP Federated SSO, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para ADP Federated SSO

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregar ADP Federated SSO desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-adp-federated-sso-from-the-gallery"></a>Agregar ADP Federated SSO desde la galería
Para configurar la integración de ADP Federated SSO en Azure AD, tiene que agregar ADP Federated SSO desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar ADP Federated SSO desde la galería, siga estos pasos:**

1.  Inicie sesión como administrador en el entorno del proveedor de identidades de Microsoft Azure.

2. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

3. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
4. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

5. En el cuadro de búsqueda, escriba **ADP Federated SSO**, seleccione **ADP Federated SSO** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![ADP Federated SSO en la lista de resultados](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con ADP Federated SSO mediante un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de ADP Federated SSO para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ADP Federated SSO.

Para establecer la relación de vínculo, vaya a ADP Federated SSO y asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con ADP Federated SSO, es preciso completar los siguientes bloques de compilación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de ADP Federated SSO](#create-an-adp-federated-sso-test-user)**: para tener un homólogo de Britta Simon en ADP Federated SSO que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación ADP Federated SSO.

**Para configurar el inicio de sesión único de Azure AD con ADP Federated SSO, realice los pasos siguientes:**

1. En Azure Portal, vaya a la página de integración de la aplicación **ADP Federated SSO**, haga clic en la **pestaña Propiedades** y realice los pasos siguientes: 

    ![Propiedades del inicio de sesión único](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_prop.png)

    a. Establezca el valor del campo **Habilitado para que los usuarios inicien sesión** en **Sí**.

    b. Copie la **dirección URL de acceso de usuario** y péguela en la **sección Configurar dirección URL de inicio de sesión** (este paso se explica más adelante en el tutorial).

    c. Establezca el valor del campo **Asignación de usuarios necesaria** en **Sí**.

    d. Establezca el valor del campo **Visible para los usuarios** en **No**.

2. Haga clic en la opción **Inicio de sesión único** de la página de integración de la aplicación **ADP Federated SSO**.

    ![Vínculo Configurar inicio de sesión único][4]

3. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_samlbase.png)

4. En la sección **Dominio y direcciones URL de ADP Federated SSO**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de ADP Federated SSO](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_url.png)

    En el cuadro de texto **Identificador**, escriba una dirección URL como: `https://fed.adp.com/` 
    
5. La aplicación ADP Federated SSO espera que las aserciones de SAML estén en un formato específico, así que es necesario agregar asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla le muestra un ejemplo de esto. El nombre de la notificación siempre será **"PersonImmutableID"** cuyo valor hemos asignado a **employeeid**. 

    Aquí se realizará la asignación de usuarios desde Azure AD a ADP Federated SSO en el valor **employeeid**, pero puede asignarlo a un valor diferente que también se base en la configuración de la aplicación. Por tanto, colabore primero con el **equipo de soporte técnico de ADP** para usar el identificador correcto de un usuario y asignar ese valor a la notificación ["PersonImmutableID"](https://www.adp.com/contact-us/overview.aspx).

    ![Configurar inicio de sesión único](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_attribute.png)

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo token de SAML como muestra la imagen y siga estos pasos:
    
    | Nombre del atributo | Valor de atributo |
    | ------------------- | -------------------- |    
    | PersonImmutableID | user.employeeid |
    
    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.

    > [!NOTE] 
    > Antes de configurar la aserción SAML, debe ponerse en contacto con el [equipo de soporte técnico de ADP](https://www.adp.com/contact-us/overview.aspx) y solicitar el valor del atributo de identificador único del inquilino. Necesitará este valor para configurar la notificación personalizada para su aplicación. 

7. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_certificate.png) 

8. Para configurar el inicio de sesión único en **ADP Federated SSO**, debe cargar el archivo **XML de metadatos** que descargó en el [sitio web de ADP Federated SSO](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Este proceso puede tardar unos días. 

### <a name="configure-your-adp-services-for-federated-access"></a>Configurar los servicios de ADP para obtener acceso federado

>[!Important]
> Los empleados que necesiten obtener acceso federado a los servicios de ADP deben asignarse a la aplicación de servicio de ADP y, posteriormente, deben volver a asignarse al servicio ADP específico.
Una vez recibida la confirmación del representante de ADP, configure los servicios de ADP y asigne o administre los usuarios para controlar su acceso al servicio de ADP especificado.

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **ADP Federated SSO**, seleccione **ADP Federated SSO** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![ADP Federated SSO en la lista de resultados](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_addservicegallery.png)

5. En Azure Portal, vaya a la página de integración de la aplicación **ADP Federated SSO**, haga clic en la **pestaña Propiedades** y realice los pasos siguientes:  

    ![Propiedades vinculadas de inicio de sesión único](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linkedproperties.png)

    a.  Establezca el valor del campo **Habilitado para que los usuarios inicien sesión** en **Sí**.

    b.  Establezca el valor del campo **Asignación de usuarios necesaria** en **Sí**.

    c.  Establezca el valor del campo **Visible para los usuarios** en **Sí**.

6. Haga clic en la opción **Inicio de sesión único** de la página de integración de la aplicación **ADP Federated SSO**.

    ![Vínculo Configurar inicio de sesión único][4]

7. En el cuadro de diálogo **Inicio de sesión único**, seleccione **Modo** como **Inicio de sesión vinculado** para vincular la aplicación a **ADP Federated SSO**.
<!---Loc Comment:Sentence "to link your application to ADP Federated SSO." should be concatenated with the previous sentence--->

    ![Single sign-on linked](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linked.png)

8. Navegue hasta la sección **Configurar la dirección URL de inicio de sesión** y realice los pasos siguientes:

    ![Propiedades del inicio de sesión único](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linkedsignon.png)
                                                              
    a. Pegue la **dirección URL de acceso de usuarios** que copió antes en la **pestaña Propiedades** (de la aplicación principal ADP Federated SSO).
                                                             
    b. Estas son las 5 aplicaciones que admiten diferentes **direcciones URL del estado de la retransmisión**. Tiene que anexar de forma manual el valor adecuado de la **dirección URL del estado de la retransmisión** de una aplicación en particular a la **dirección URL de acceso de usuarios**.
    
    * **ADP Workforce Now**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Guarde** los cambios.

10. Tras recibir la confirmación del representante de ADP, comience la prueba con uno o dos usuarios.

    a. Asigne unos cuantos usuarios a la aplicación del servicio de ADP para que prueben el acceso federado.

    b. La prueba es correcta cuando los usuarios acceden a la aplicación del servicio de ADP en la galería y pueden obtener acceso a su servicio ADP.
 
11. Cuando se confirme una prueba correcta, asigne el servicio de ADP federado a usuarios individuales o a grupos de usuarios (esto se explica más adelante en el tutorial) e implántelo entre sus empleados. 

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-an-adp-federated-sso-test-user"></a>Crear un usuario de prueba de ADP Federated SSO

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en ADP Federated SSO. Colabore con el [equipo de soporte técnico de ADP](https://www.adp.com/contact-us/overview.aspx) para agregar usuarios a la cuenta de ADP Federated SSO.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure y concederá acceso a ADP Federated SSO.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a ADP Federated SSO, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **ADP Federated SSO**.

    ![Vínculo a ADP Federated SSO en la lista de aplicaciones](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de ADP Federated SSO del panel de acceso, debería iniciar sesión automáticamente en su aplicación ADP Federated SSO.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_203.png

