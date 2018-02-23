---
title: "Tutorial: Integración de Azure Active Directory con Palo Alto Networks - Aperture | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Palo Alto Networks - Aperture."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 75633cbf13756b4b2be3e4be055b12021cc396d2
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Tutorial: Integración de Azure Active Directory con Palo Alto Networks - Aperture

En este tutorial, obtendrá información sobre cómo integrar Palo Alto Networks - Aperture con Azure Active Directory (Azure AD).

La integración de Palo Alto Networks - Aperture con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Palo Alto Networks - Aperture.
- Puede permitir que los usuarios inicien sesión automáticamente en Palo Alto Networks - Aperture (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Palo Alto Networks - Aperture, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción con inicio de sesión único de Palo Alto Networks - Aperture

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Palo Alto Networks - Aperture desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Adición de Palo Alto Networks - Aperture desde la galería
Para configurar la integración de Palo Alto Networks - Aperture en Azure AD, es preciso agregar Palo Alto Networks - Aperture desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Palo Alto Networks - Aperture desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Palo Alto Network - Aperture**, seleccione **Palo Alto Networks - Aperture** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Palo Alto Networks - Aperture en la lista de resultados](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Palo Alto Networks - Aperture con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Palo Alto Networks - Aperture para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Palo Alto Networks - Aperture.

Para configurar y probar el inicio de sesión único de Azure AD con Palo Alto Networks - Aperture, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Palo Alto Networks - Aperture](#create-a-palo-alto-networks---aperture-test-user)**: para tener un homólogo de Britta Simon en Palo Alto Networks - Aperture que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Palo Alto Networks - Aperture.

**Para configurar el inicio de sesión único de Azure AD con Palo Alto Networks - Aperture, siga estos pasos:**

1. En la página de integración de la aplicación **Palo Alto Networks - Aperture** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_samlbase.png)

3. En la sección **Dominio y direcciones URL de Palo Alto Networks - Aperture**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de inicio de sesión único con Dominio y direcciones URL de Palo Alto Networks - Aperture](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`.

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de inicio de sesión único con Dominio y direcciones URL de Palo Alto Networks - Aperture](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con [el equipo de soporte técnico del cliente de Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) para obtener estos valores. 

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_400.png)


7. En la sección **Palo Alto Networks - Aperture Configuration** (Configuración de Palo Alto Networks - Aperture), haga clic en **Configure Palo Alto Networks - Aperture** (Configurar Palo Alto Networks - Aperture) para abrir la ventana **Configurar inicio de sesión**. Copie los valores de **SAML Entity ID y SAML Single Sign-On Service URL** (Identificador de entidad de SAML y Dirección URL del servicio de inicio de sesión único de SAML) de la sección **Referencia rápida**.

    ![El vínculo Configurar](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_configure.png)

8. En otra ventana del explorador web, inicie sesión en el sitio web de Palo Alto Networks - Aperture como administrador.

9. En la barra de menús superior, haga clic en **SETTINGS** (CONFIGURACIÓN).

    ![La pestaña Configuración](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

10. Vaya a la sección **APPLICATION** (APLICACIÓN) y haga clic en el formulario **Authentication** (Autenticación) en el lado izquierdo del menú.

    ![La pestaña Autenticación](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
11. En la página **Authentication** (Autenticación), realice los siguientes pasos:
    
    ![La pestaña Autenticación](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Active **Enable Single Sign-On(Supported SSP Providers are Okta, Onelogin)** (Habilitar inicio de sesión único [Los proveedores SSP admitidos son Okta, Onelogin]) en el campo **Single Sign-On** (Inicio de sesión único).

    b. En el cuadro de texto **Identity Provider ID** (Id. de proveedor de identidad), pegue el valor del **identificador de entidad de SAML** que ha copiado de Azure Portal.

    c. Haga clic en **Choose File** (Elegir archivo) para cargar el certificado descargado de Azure AD en **Identity Provider Certificate** (Certificado del proveedor de identidades).

    d. En el cuadro de texto **Identity provider SSO URL** (Dirección URL de inicio de sesión único del proveedor de identidades), pegue el valor de **dirección URL del servicio de inicio de sesión único de SAML**, que ha copiado de Azure Portal.

    e. Revise la información del proveedor de identidades en la sección **Aperture Info** (Información de Aperture) y descargue el certificado del campo **Aperture Key** (Clave de Aperture).

    f. Haga clic en **Save**(Guardar).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-palo-alto-networks---aperture-test-user"></a>Creación de un usuario de prueba de Palo Alto Networks: usuario de prueba de la interfaz de usuario de administrador

En esta sección, creará un usuario llamado Britta Simon en Palo Alto Networks - Aperture. Trabaje con el [equipo de soporte técnico del cliente de Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) para agregar los usuarios a la plataforma Palo Alto Networks - Aperture. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para usar el inicio de sesión único de Azure al concederle acceso a Palo Alto Networks: Aperture.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Palo Alto Networks: Aperture, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Palo Alto Networks - Aperture**.

    ![El vínculo a Palo Alto Networks - Aperture en la lista de aplicaciones](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Palo Alto Networks - Aperture en el Panel de acceso, debe iniciar sesión automáticamente en la aplicación Palo Alto Networks - Aperture.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_203.png

