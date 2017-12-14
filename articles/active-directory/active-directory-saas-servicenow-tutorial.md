---
title: "Tutorial: Integración de Azure Active Directory con ServiceNow | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y ServiceNow."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 8b21c7b18c31f3111caa13d08efb5aa42ecc0e49
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Tutorial: Integración de Azure Active Directory con ServiceNow

En este tutorial, aprenderá a integrar ServiceNow con Azure Active Directory (Azure AD).

La integración de ServiceNow con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a ServiceNow.
- Puede habilitar que los usuarios inicien sesión automáticamente en ServiceNow (inicio de sesión único) con su cuenta de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con ServiceNow, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Para ServiceNow, una instancia o inquilino de ServiceNow, versión Calgary o superior
- Para ServiceNow Express, una instancia de ServiceNow Express, versión Helsinki o superior
- El inquilino de ServiceNow debe tener habilitado el [complemento de inicio de sesión único en varios proveedores](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0). Esto puede hacerse [enviando una solicitud de servicio](https://hi.service-now.com).

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de ServiceNow desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-servicenow-from-the-gallery"></a>Adición de ServiceNow desde la galería
Para configurar la integración de ServiceNow en Azure AD, es preciso agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar ServiceNow desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **ServiceNow**, seleccione **ServiceNow** en el panel de resultados y haga clic en el botón **Agregar** para agregar la aplicación.

    ![ServiceNow en la lista de resultados](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con ServiceNow con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de ServiceNow para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ServiceNow.

Para establecer la relación de vínculo, en ServiceNow, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con ServiceNow, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD para ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)**: para permitir a los usuarios usar esta característica.
2. **[Configuración del inicio de sesión único de Azure AD para ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)**: para permitir a los usuarios usar esta característica.
3. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de usuarios de prueba de ServiceNow](#create-a-servicenow-test-user)**: para tener un homólogo de Britta Simon en ServiceNow que esté vinculado a la representación de usuario en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
6. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Configuración del inicio de sesión único de Azure AD para ServiceNow

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación ServiceNow.

**Para configurar el inicio de sesión único de Azure AD con ServiceNow, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **ServiceNow**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. En la sección **ServiceNow Domain and URLs** (Dominio y direcciones URL de ServiceNow), lleve a cabo los pasos siguientes:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de ServiceNow](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<instance-name>.service-now.com/navpage.do`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<instance-name>.service-now.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizar estos valores con la dirección URL de inicio de sesión y el identificador reales, como se explica más adelante en el tutorial.

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. Para generar la dirección URL de **Metadatos**, lleve a cabo los pasos siguientes:

    a. Haga clic en **Registros de aplicaciones**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/appregistrations.png)

    b. Haga clic en **Puntos de conexión** para abrir el cuadro de diálogo **Puntos de conexión**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/endpointicon.png)
    
    c. Haga clic en el botón Copiar para copiar la dirección URL del **DOCUMENTO DE METADATOS DE FEDERACIÓN** y péguela en el Bloc de notas.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/endpoint.png)

    d. Ahora, vaya a las propiedades de **ServiceNow**, copie el valor de **Application ID** con el botón **Copiar** y péguelo en el bloc de notas.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/appid.png)

    e. Genere la **Dirección URL de metadatos** con el patrón siguiente: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`.  Copie el valor generado en el bloc de notas; estas dirección URL de metadatos se usará más adelante en el tutorial.

7. ServiceNow tiene el servicio de configuración con un clic, es decir, Azure AD configura automáticamente ServiceNow para la autenticación de SAML. Para habilitarlo, vaya a la sección **ServiceNow Configuration** (Configuración de ServiceNow), haga clic en **Configure ServiceNow** (Configurar ServiceNow) para abrir la ventana Configurar inicio de sesión.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

8. Escriba el nombre de la instancia de ServiceNow, el nombre de usuario y la contraseña de administrador en el formulario **Configurar inicio de sesión** y haga clic en **Configurar ahora**. Tenga en cuenta que el nombre de usuario de administrador proporcionado debe tener asignado el rol **security_admin** en ServiceNow para que esto funcione. Si no es así, para configurar manualmente ServiceNow para usar Azure AD como proveedor de identidades SAML, haga clic en **Configurar manualmente el inicio de sesión único** y copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** desde la sección Referencia rápida.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/configure.png "Configurar dirección URL de la aplicación")

9. Inicie sesión en la aplicación ServiceNow como administrador.

10. Active el complemento **Integration - Multiple Provider Single Sign-On Installer**, para lo que debe seguir estos pasos:

    a. En el panel de navegación del lado izquierdo, busque la sección **System Definition** (Definición del sistema) con la barra de búsqueda y haga clic en **Plugins** (Complementos).

    ![Activar complemento](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "activar complemento")

     b. Busque **Integration - Multiple Provider Single Sign-On Installer**.

     ![Activar complemento](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "activar complemento")

    c. Seleccione el complemento. Haga clic con el botón derecho y seleccione **Activate/Upgrade** (Activar o actualizar).

    d. Haga clic en el botón **Activate**.

11. En el panel de navegación del lado izquierdo, busque la sección de **SSO de varios proveedores** de la barra de búsqueda y haga clic en **Properties** (Propiedades).

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configurar dirección URL de la aplicación")

12. En el cuadro de diálogo **Multiple Provider SSO Properties** (Propiedades de SSO de varias proveedores), realice los pasos siguientes:

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/ic7694981.png "Configurar dirección URL de la aplicación")

    a. En **Enable multiple provider SSO** (Habilitar SSO de varios proveedores), seleccione **Yes** (Sí).

    b. En **Enable Auto Importing of users from all identity providers into the user table** (Habilitar la importación automática de usuarios de todos los proveedores de identidades en la tabla de usuarios), seleccione **Yes** (Sí).

    c. En **Enable debug logging for the multiple provider SSO integration** (Habilitar registro de depuración para la integración de SSO de varios proveedores), seleccione **Yes** (Sí).

    d. En el cuadro de texto **The field on the user table that...** (El campo en la tabla de usuario que...), escriba **user_name** (nombre_usuario).

    e. Haga clic en **Guardar**.

13. En el panel de navegación del lado izquierdo, busque la sección de **SSO de varios proveedores** de la barra de búsqueda y haga clic en **x509 Certificates** (Certificados x509).

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Configurar inicio de sesión único")

14. En el cuadro de diálogo **Certificados X.509**, haga clic en **Nuevo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694974.png "Configurar inicio de sesión único")

15. En el cuadro de diálogo **Certificados X.509** , realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "Configurar inicio de sesión único")

    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración (por ejemplo, **TestSAML2.0**).

    b. Seleccione **Active**(Activo).

    c. En **Format** (Formato), seleccione **PEM**.

    d. En **Type** (Tipo), seleccione **Trust Store Cert** (Confiar en certificados de almacén).

    e. Abra el certificado codificado en base 64 descargado de Azure en el bloc de notas, copie su contenido en el portapapeles y péguelo en el cuadro de texto **PEM Certificate** (Certificado PEM).

     f. Haga clic en **Enviar**.

16. En el panel de navegación de la izquierda, haga clic en **Identity Providers**(Proveedores de identidades).

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configurar inicio de sesión único")

17. En el cuadro de diálogo **Proveedores de identidades**, haga clic en **Nuevo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694977.png "Configurar inicio de sesión único")

18. En el cuadro de diálogo **Proveedores de identidades**, haga clic en **SAML2 Update1?**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694978.png "Configurar inicio de sesión único")

19. En el cuadro de diálogo SAML2 Update1 Properties (Propiedades de SAML2 Update1), realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/idp.png "Configurar inicio de sesión único")

    a. Seleccione la opción **URL** del cuadro de diálogo **Import Identity Provider Metadata** (Importar metadatos del proveedor de identidades).

    b. Escriba la **dirección URL de metadatos** generada en Azure Portal.

    c. Haga clic en **Import**.

20. Se lee la dirección URL de metadatos de IdP y se rellena toda la información de campos.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694982.png "Configurar inicio de sesión único")

    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración (por ejemplo, **SAML 2.0**).

    b. En el cuadro de texto **Campo de usuario**, escriba **correo electrónico** o **user_name**, según qué campo se use para identificar de forma única a los usuarios en la implementación de ServiceNow.

    > [!NOTE]
    > Puede configurar Azure AD para que emita el identificador de usuario de Azure AD (nombre de usuario principal) o la dirección de correo electrónico como identificador único en el token SAML. Para ello, vaya a la sección **ServiceNow > Atributos > Inicio de sesión único** en Azure Portal y asigne el campo que desee al atributo **nameidentifier**. El valor almacenado para el atributo seleccionado en Azure AD (por ejemplo, nombre de usuario principal) debe coincidir con el valor almacenado en ServiceNow para el campo especificado (por ejemplo, user_name).

    c. Copie el valor de **ServiceNow Homepage**, péguelo en el cuadro de texto **Dirección URL de inicio de sesión** de la sección **ServiceNow Domain and URLs** (Dominio y direcciones URL de ServiceNow) de Azure Portal.

    > [!NOTE]
    > La página principal de la instancia de ServiceNow es una concatenación de su **URL de inquilino de ServiceNow** y **/navpage.do** (por ejemplo: `https://fabrikam.service-now.com/navpage.do`).

    d. Copie el valor de **Entity ID / Issuer** (Id. de entidad / emisor), péguelo en el cuadro de texto **Identificador** de la sección **ServiceNow Domain and URLs** (Dominio y direcciones URL de ServiceNow) de Azure Portal.

     e. En **Certificado x.509**, aparece el certificado que ha creado en el paso anterior.

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Configuración del inicio de sesión único de Azure AD para ServiceNow Express

1. En Azure Portal, en la página de integración de la aplicación **ServiceNow**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. En la sección **ServiceNow Domain and URLs** (Dominio y direcciones URL de ServiceNow), lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba el valor con el siguiente patrón: `https://<instance-name>.service-now.com/navpage.do`

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de ServiceNow](https://www.servicenow.com/support/contact-support.html) para obtener estos valores.

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificate.png)

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. ServiceNow tiene el servicio de configuración con un clic, es decir, Azure AD configura automáticamente ServiceNow para la autenticación de SAML. Para habilitarlo, vaya a la sección **ServiceNow Configuration** (Configuración de ServiceNow), haga clic en **Configure ServiceNow** (Configurar ServiceNow) para abrir la ventana Configurar inicio de sesión.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

7. Escriba el nombre de la instancia de ServiceNow, el nombre de usuario y la contraseña de administrador en el formulario **Configurar inicio de sesión** y haga clic en **Configurar ahora**. Tenga en cuenta que el nombre de usuario de administrador proporcionado debe tener asignado el rol **security_admin** en ServiceNow para que esto funcione. Si no es así, para configurar manualmente ServiceNow para usar Azure AD como proveedor de identidades SAML, haga clic en **Configurar manualmente el inicio de sesión único** y copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** desde la sección Referencia rápida.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/configure.png "Configurar dirección URL de la aplicación")

8. Inicie sesión en la aplicación ServiceNow Express como administrador.

9. En el panel de navegación del lado izquierdo, haga clic en **Inicio de sesión único**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configurar dirección URL de la aplicación")

10. En el cuadro de diálogo **Inicio de sesión único**, haga clic en el icono de configuración de la parte superior derecha y establezca las siguientes propiedades:

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configurar dirección URL de la aplicación")

    a. Cambie **Enable multiple provider SSO** (Habilitar SSO de varios proveedores), hacia la derecha.
    
    b. Cambie **Enable debug logging got the multiple provider SSO integration** (Habilitar registro de depuración para integración de SSO de varios proveedores) hacia la derecha.
    
    c. En el cuadro de texto **The field on the user table that...** (El campo en la tabla de usuario que...), escriba **user_name** (nombre_usuario).

11. En el cuadro de diálogo **Single Sign-On** (Inicio de sesión único), haga clic en **Add New Certificate** (Agregar nuevo certificado).

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Configurar inicio de sesión único")

12. En el cuadro de diálogo **Certificados X.509** , realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "Configurar inicio de sesión único")

    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración (por ejemplo, **TestSAML2.0**).

    b. Seleccione **Active**(Activo).

    c. En **Format** (Formato), seleccione **PEM**.

    d. En **Type** (Tipo), seleccione **Trust Store Cert** (Confiar en certificados de almacén).

    e. Abra el certificado codificado en base 64 descargado de Azure Portal en el bloc de notas, copie su contenido en el portapapeles y péguelo en el cuadro de texto **PEM Certificate** (Certificado PEM).

    f. Haga clic en **Update** (Actualizar).

13. En el cuadro de diálogo **Single Sign-On** (Inicio de sesión único), haga clic en **Add New IdP** (Agregar nuevo IdP).

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Configurar inicio de sesión único")

14. En el cuadro de diálogo **Add New Identity Provider** (Agregar nuevo proveedor de identidades), en **Configure Identity Provider** (Configurar proveedor de identidades), siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Configurar inicio de sesión único")

    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración (por ejemplo, **SAML 2.0**).

    b. En el campo **Identity provider URL** (Dirección URL del proveedor de identidad), pegue el valor del **identificador del proveedor de identidad** que ha copiado de Azure Portal.
    
    c. En el campo **Identity Provider's AuthnRequest** (Solicitud de autenticación del proveedor de identidades), pegue el valor de la **dirección URL de solicitud de autenticación** que ha copiado de Azure Portal.

    d. En el campo **Identity Provider's SingleLogoutRequest** (Solicitud de cierre de sesión único del proveedor de identidades), pegue el valor de la **dirección URL del servicio de cierre de sesión único** que ha copiado de Azure Portal.

    e. Como **Certificado de proveedor de identidades**, seleccione el certificado que ha creado en el paso anterior.

15. Haga clic en **Advanced Settings** (Configuración avanzada) y en **Additional Identity Provider Properties** (Propiedades adicionales del proveedor de identidades), siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Configurar inicio de sesión único")

    a. En el cuadro de texto **Protocol Binding for the IDP's SingleLogoutRequest** (Vinculación de protocolo para la solicitud de cierre de sesión único del proveedor de identidades), escriba **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. En el cuadro de texto **NameID Policy** (Directiva NameID), escriba **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    c. En **AuthnContextClassRef Method** (Método AuthnContextClassRef), escriba `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Anule la selección de **Create an AuthnContextClass**(Crear AuthnContextClass).

16. En **Additional Identity Provider Properties** (Propiedades adicionales del proveedor de identidades), siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Configurar inicio de sesión único")

    a. En el cuadro de texto **ServiceNow Homepage** (Página de inicio de ServiceNow), escriba la dirección URL de la página de inicio de instancia de ServiceNow.

    > [!NOTE]
    > La página principal de la instancia de ServiceNow es una concatenación de su **URL de inquilino de ServiceNow** y **/navpage.do** (por ejemplo: `https://fabrikam.service-now.com/navpage.do`).

    b. En el cuadro de texto **Entity ID / Issuer** (Id. de entidad / emisor), escriba la dirección URL de su inquilino de ServiceNow.

    c. En el cuadro de texto **Audience URI** (Identificador URI de audiencia), escriba la dirección URL de su inquilino ServiceNow.

    d. En el cuadro de diálogo **Clock Skew** (Desplazamiento del reloj), escriba **60**.

    e. En el cuadro de texto **Campo de usuario**, escriba **correo electrónico** o **user_name**, según qué campo se use para identificar de forma única a los usuarios en la implementación de ServiceNow.

    > [!NOTE]
    > Puede configurar Azure AD para que emita el identificador de usuario de Azure AD (nombre de usuario principal) o la dirección de correo electrónico como identificador único en el token SAML. Para ello, vaya a la sección **ServiceNow > Atributos > Inicio de sesión único** en Azure Portal y asigne el campo que desee al atributo **nameidentifier**. El valor almacenado para el atributo seleccionado en Azure AD (por ejemplo, nombre de usuario principal) debe coincidir con el valor almacenado en ServiceNow para el campo especificado (por ejemplo, user_name).

    f. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-servicenow-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-servicenow-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-servicenow-test-user"></a>Creación de un usuario de prueba de ServiceNow

En esta sección, creará un usuario llamado Britta Simon en ServiceNow. Si no sabe cómo agregar un usuario en una cuenta de ServiceNow o ServiceNow Express, póngase en contacto con el [equipo de atención al cliente de ServiceNow](https://www.servicenow.com/support/contact-support.html).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a ServiceNow.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a ServiceNow, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **ServiceNow**.

    ![Vínculo a ServiceNow en la lista de aplicaciones](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de ServiceNow en el panel de acceso, debería iniciar sesión automáticamente en su aplicación ServiceNow.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png

