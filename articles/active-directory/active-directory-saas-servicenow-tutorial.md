---
title: "Tutorial: Integración de Azure Active Directory con ServiceNow | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y ServiceNow y ServiceNow Express."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d8eb99e-8ce5-4ba4-8b54-5c3d9ae573f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: a91fab90a94b655b93c8ae9064ea4836b80d7678
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Tutorial: Integración de Azure Active Directory con ServiceNow
En este tutorial, aprenderá a integrar ServiceNow y ServiceNow Express con Azure Active Directory (Azure AD).

La integración de ServiceNow y ServiceNow Express con Azure AD proporciona las siguientes ventajas:

* En Azure AD se puede controlar quién tiene acceso a ServiceNow y ServiceNow Express
* Puede permitir que los usuarios inicien sesión automáticamente en ServiceNow y ServiceNow Express (inicio de sesión único) con sus cuentas de Azure AD
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con ServiceNow y ServiceNow Express se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Para ServiceNow, una instancia o inquilino de ServiceNow, versión Calgary o superior
* Para ServiceNow Express, una instancia de ServiceNow Express, versión Helsinki o superior
* El inquilino de ServiceNow debe tener habilitado el [complemento de inicio de sesión único en varios proveedores](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0). Esto puede hacerse [enviando una solicitud de servicio](https://hi.service-now.com). 

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de ServiceNow desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD en ServiceNow o ServiceNow Express

## <a name="adding-servicenow-from-the-gallery"></a>Adición de ServiceNow desde la galería
Para configurar la integración de ServiceNow o ServiceNow Express en Azure AD, es preciso agregar ServiceNow desde la galería a la lista de aplicaciones SaaS administradas. 

**Para agregar ServiceNow desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **ServiceNow**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)
7. En el panel de resultados, seleccione **ServiceNow** y haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con ServiceNow o ServiceNow con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de ServiceNow para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ServiceNow.
Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en ServiceNow. Para configurar y probar el inicio de sesión único de Azure AD con ServiceNow, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD para ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)**: para permitir a los usuarios usar esta característica.
2. **[Configuración del inicio de sesión único de Azure AD para ServiceNow Express](#configuring-azure-ad-single-sign-on-for-servicenow-express)**: para permitir a los usuarios usar esta característica.
3. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de usuarios de prueba de ServiceNow](#creating-a-servicenow-test-user)**: para tener un homólogo de Britta Simon en ServiceNow que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
6. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

> [!NOTE]
> Si desea configurar ServiceNow, omita el paso 2. De igual modo, si desea configurar ServiceNow Express, omita el paso 1.
> 
> 

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>Configuración del inicio de sesión único de Azure AD para ServiceNow
1. En el Portal de Azure AD clásico, en la página de integración de aplicaciones de **ServiceNow**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurar inicio de sesión único")

2. En la página **¿Cómo desea que los usuarios inicien sesión en ServiceNow?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurar inicio de sesión único")

3. En la página **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurar dirección URL de la aplicación")
   
    a. En el cuadro de texto **URL de inicio de sesión de ServiceNow**, escriba la dirección URL usada por los usuarios para iniciar sesión en la aplicación ServiceNow con el siguiente patrón: `https://<instance-name>.service-now.com`.
   
    b. En el cuadro de texto **Identifier**, escriba la dirección URL usada por los usuarios para iniciar sesión en la aplicación ServiceNow con el siguiente patrón: `https://<instance-name>.service-now.com`.
   
    c. Haga clic en **Siguiente**

4. Para que Azure AD configure automáticamente ServiceNow para la autenticación basada en SAML, escriba el nombre de la instancia de ServiceNow, el nombre de usuario de administrador y la contraseña de administrador en el formulario **Configurar el inicio de sesión único automáticamente** y haga clic en *Configurar*. Tenga en cuenta que el nombre de usuario de administrador proporcionado debe tener asignado el rol **security_admin** en ServiceNow para que esto funcione. De lo contrario, para configurar manualmente ServiceNow para usar Azure AD como proveedor de identidades SAML, haga clic en **Configurar manualmente esta aplicación para el inicio de sesión único** y en **Siguiente**, y complete los pasos indicados a continuación.
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurar dirección URL de la aplicación")

5. En la página **Configurar inicio de sesión único en ServiceNow**, haga clic en **Descargar certificado**, guarde el archivo de certificado localmente en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurar inicio de sesión único")

6. Inicie sesión en su aplicación ServiceNow como administrador.

7. Active el complemento *Integration - Multiple Provider Single Sign-On Installer*, para lo que debe seguir estos pasos:
   
    a. En el panel de navegación del lado izquierdo, Vaya a la sección **System Definition** (Definición del sistema) y haga clic en **Plugins** (Complementos).
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "Activate plugin")(Activar complemento)
   
    b. Busque *Integration - Multiple Provider Single Sign-On Installer*.
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "Activate plugin")(Activar complemento)
   
    c. Seleccione el complemento. Haga clic con el botón derecho y seleccione **Activate/Upgrade** (Activar o actualizar).
   
    d. Haga clic en el botón **Activate**.

8. En el panel de navegación de la izquierda, haga clic en **Properties**(Propiedades).  
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configurar dirección URL de la aplicación")

9. En el cuadro de diálogo **Multiple Provider SSO Properties** (Propiedades de SSO de varias proveedores), realice los pasos siguientes:
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configurar dirección URL de la aplicación")
   
    a. En **Enable multiple provider SSO** (Habilitar SSO de varios proveedores), seleccione **Yes** (Sí).
   
    b. En **Enable debug logging got the multiple provider SSO integration** (Habilitar registro de depuración para integración de SSO de varios proveedores), seleccione **Yes** (Sí).
   
    c. En el cuadro de texto **The field on the user table that...** (El campo en la tabla de usuario que...), escriba **user_name** (nombre_usuario).
   
    d. Haga clic en **Save**.

10. En el panel de navegación de la izquierda, haga clic en **Certificados x509**.
    
     ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Configurar inicio de sesión único")

11. En el cuadro de diálogo **Certificados X.509**, haga clic en **Nuevo**.
    
     ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configurar inicio de sesión único")

12. En el cuadro de diálogo **Certificados X.509** , realice los pasos siguientes:
    
     ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurar inicio de sesión único")
    
     a. Haga clic en **Nuevo**.
    
     b. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración (por ejemplo, **TestSAML2.0**).
    
     c. Seleccione **Active**(Activo).
    
     d. En **Format** (Formato), seleccione **PEM**.
    
     e. En **Type** (Tipo), seleccione **Trust Store Cert** (Confiar en certificados de almacén).
    
     f. Abra el certificado con codificación Base64 en el Bloc de notas, copie su contenido en el Portapapeles y péguelo en el cuadro de texto **PEM Certificate** (Certificado PEM).
    
     g. Haga clic en **Update**(Actualizar).

13. En el panel de navegación de la izquierda, haga clic en **Identity Providers**(Proveedores de identidades).
    
     ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configurar inicio de sesión único")

14. En el cuadro de diálogo **Proveedores de identidades**, haga clic en **Nuevo**:
    
     ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configurar inicio de sesión único")

15. En el cuadro de diálogo **Proveedores de identidades**, haga clic en **SAML2 Update1?**:
    
     ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configurar inicio de sesión único")

16. En el cuadro de diálogo SAML2 Update1 Properties (Propiedades de SAML2 Update1), realice los pasos siguientes:
    
     ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configurar inicio de sesión único")

    a. En el cuadro de texto **Nombre**, escriba el nombre de la configuración (por ejemplo, **SAML 2.0**).

    b. En el cuadro de texto **Campo de usuario**, escriba **correo electrónico** o **user_name**, según qué campo se use para identificar de forma única a los usuarios en la implementación de ServiceNow. 

    > [!NOTE] 
    > Puede configurar Azure AD para que emita el identificador de usuario de Azure AD (nombre principal de usuario) o la dirección de correo electrónico como identificador único en el token SAML. Para ello, vaya a la sección **ServiceNow > Atributos > Inicio de sesión único** en el Portal de Azure clásico y asigne el campo que desee al atributo **nameidentifier**. El valor almacenado para el atributo seleccionado en Azure AD (por ejemplo, nombre principal de usuario) debe coincidir con el valor almacenado en ServiceNow para el campo especificado (por ejemplo, user_name).

    c. En el Portal de Azure AD clásico, copie el valor de **Id. de proveedor de identidad**y péguelo en el cuadro de texto **Identity Provider URL** (URL del proveedor de identidades).

    d. En el Portal de Azure AD clásico, copie el valor de **Dirección URL de solicitud de autenticación** y péguelo en el cuadro de texto **Solicitud de autenticación de proveedor de identidades**.

    e. En el Portal de Azure AD clásico, copie el valor de **Dirección URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **Solicitud de cierre de sesión única del proveedor de identidades**.

    f. En el cuadro de texto **ServiceNow Homepage** (Página de inicio de ServiceNow), escriba la dirección URL de la página de inicio de instancia de ServiceNow.

    > [!NOTE] 
    > La página de inicio de la instancia de ServiceNow es una concatenación de su **URL de inquilino de ServiceNow** y **/navpage.do** (por ejemplo: `https://fabrikam.service-now.com/navpage.do`).

    g. En el cuadro de texto **Entity ID / Issuer** (Id. de entidad / emisor), escriba la dirección URL de su inquilino de ServiceNow.

    h. En el cuadro de texto **Audience URL** (Dirección URL de audiencia), escriba la dirección URL de su inquilino ServiceNow. 

    i. En el cuadro de texto **Protocol Binding for the IDP's SingleLogoutRequest** (Vinculación de protocolo para la solicitud de cierre de sesión único del proveedor de identidades), escriba **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    j. En el cuadro de texto NameID Policy (Directiva de Id. de nombres), escriba **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    k. Anule la selección de **Create an AuthnContextClass**(Crear AuthnContextClass).

    l. En **AuthnContextClassRef Method** (Método AuthnContextClassRef), escriba `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. Esto solo es preciso para las organizaciones que solo trabajan en la nube. Si se usa MFA o ADFS local para la autenticación, este valor no se debe configurar. 

    m. En el cuadro de diálogo **Clock Skew** (Desplazamiento del reloj), escriba **60**.

    n. En **Single Sign On Script** (Script de inicio de sesión único), seleccione **MultiSSO_SAML2_Update1**.

    o. Como **Certificado X.509**, seleccione el certificado que ha creado en el paso anterior.

    p. Haga clic en **Submit**(Enviar). 

1. En el Portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurar inicio de sesión único")

2. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurar inicio de sesión único")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>Configuración del inicio de sesión único de Azure AD para ServiceNow Express
1. En el Portal de Azure AD clásico, en la página de integración de aplicaciones de **ServiceNow**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurar inicio de sesión único")

2. En la página **¿Cómo desea que los usuarios inicien sesión en ServiceNow?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurar inicio de sesión único")

3. En la página **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurar dirección URL de la aplicación")
   
    a. En el cuadro de texto **URL de inicio de sesión de ServiceNow**, escriba la dirección URL usada por los usuarios para iniciar sesión en la aplicación ServiceNow con el siguiente patrón: `https://<instance-name>.service-now.com`.
   
    b. En el cuadro de texto **URL del emisor**, escriba la dirección URL usada por los usuarios para iniciar sesión en la aplicación ServiceNow con el siguiente patrón: `https://<instance-name>.service-now.com`.
   
    c. Haga clic en **Siguiente**

4. Haga clic en **Configurar manualmente esta aplicación para el inicio de sesión único** y, luego, en **Siguiente**, y complete los pasos siguientes.
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurar dirección URL de la aplicación")

5. En la página **Configurar inicio de sesión único en ServiceNow**, haga clic en **Descargar certificado**, guarde el archivo de certificado localmente en el equipo y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurar inicio de sesión único")

6. Inicie sesión en la aplicación ServiceNow Express como administrador.

7. En el panel de navegación del lado izquierdo, haga clic en **Inicio de sesión único**.  
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configurar dirección URL de la aplicación")

8. En el cuadro de diálogo **Inicio de sesión único**, haga clic en el icono de configuración de la parte superior derecha y establezca las siguientes propiedades:
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configurar dirección URL de la aplicación")
   
    a. Cambie **Enable multiple provider SSO** (Habilitar SSO de varios proveedores), hacia la derecha.
   
    b. Cambie **Enable debug logging got the multiple provider SSO integration** (Habilitar registro de depuración para integración de SSO de varios proveedores) hacia la derecha.
   
    c. En el cuadro de texto **The field on the user table that...** (El campo en la tabla de usuario que...), escriba **user_name** (nombre_usuario).
9. En el cuadro de diálogo **Single Sign-On** (Inicio de sesión único), haga clic en **Add New Certificate** (Agregar nuevo certificado).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Configurar inicio de sesión único")
10. En el cuadro de diálogo **Certificados X.509** , realice los pasos siguientes:
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurar inicio de sesión único")
    
    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración (por ejemplo, **TestSAML2.0**).
    
    b. Seleccione **Active**(Activo).
    
    c. En **Format** (Formato), seleccione **PEM**.
    
    d. En **Type** (Tipo), seleccione **Trust Store Cert** (Confiar en certificados de almacén).
    
    e. Cree un archivo con codificación Base64 a partir del certificado descargado.
    
    > [!NOTE]
    > Para más información, vea [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).
    > 
    > 
    
    f. Abra el certificado con codificación Base64 en el Bloc de notas, copie su contenido en el Portapapeles y péguelo en el cuadro de texto **PEM Certificate** (Certificado PEM).
    
    g. Haga clic en **Update**(Actualizar).
11. En el cuadro de diálogo **Single Sign-On** (Inicio de sesión único), haga clic en **Add New IdP** (Agregar nuevo IdP).
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Configurar inicio de sesión único")
12. En el cuadro de diálogo **Add New Identity Provider** (Agregar nuevo proveedor de identidades), en **Configure Identity Provider** (Configurar proveedor de identidades), siga estos pasos:
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Configurar inicio de sesión único")

    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración (por ejemplo, **SAML 2.0**).

    b. En el Portal de Azure AD clásico, copie el valor de **Id. de proveedor de identidad**y péguelo en el cuadro de texto **Identity Provider URL** (URL del proveedor de identidades).

    c. En el Portal de Azure AD clásico, copie el valor de **Dirección URL de solicitud de autenticación** y péguelo en el cuadro de texto **Solicitud de autenticación de proveedor de identidades**.

    d. En el Portal de Azure AD clásico, copie el valor de **Dirección URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **Solicitud de cierre de sesión única del proveedor de identidades**.

    e. Como **Certificado de proveedor de identidades**, seleccione el certificado que ha creado en el paso anterior.


1. Haga clic en **Advanced Settings** (Configuración avanzada) y en **Additional Identity Provider Properties** (Propiedades adicionales del proveedor de identidades), siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Configurar inicio de sesión único")
   
    a. En el cuadro de texto **Protocol Binding for the IDP's SingleLogoutRequest** (Vinculación de protocolo para la solicitud de cierre de sesión único del proveedor de identidades), escriba **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.
   
    b. En el cuadro de texto **NameID Policy** (Directiva NameID), escriba **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.    
   
    c. En **AuthnContextClassRef Method**, escriba **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.
   
    d. Anule la selección de **Create an AuthnContextClass**(Crear AuthnContextClass).

2. En **Additional Identity Provider Properties** (Propiedades adicionales del proveedor de identidades), siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Configurar inicio de sesión único")
   
    a. En el cuadro de texto **ServiceNow Homepage** (Página de inicio de ServiceNow), escriba la dirección URL de la página de inicio de instancia de ServiceNow.
   
    > [!NOTE]
    > La página de inicio de la instancia de ServiceNow es una concatenación de su **URL de inquilino de ServiceNow** y **/navpage.do** (por ejemplo: `https://fabrikam.service-now.com/navpage.do`).
    > 
    > 
   
    b. En el cuadro de texto **Entity ID / Issuer** (Id. de entidad / emisor), escriba la dirección URL de su inquilino de ServiceNow.
   
    c. En el cuadro de texto **Audience URI** (Identificador URI de audiencia), escriba la dirección URL de su inquilino ServiceNow. 
   
    d. En el cuadro de diálogo **Clock Skew** (Desplazamiento del reloj), escriba **60**.
   
    e. En el cuadro de texto **Campo de usuario**, escriba **correo electrónico** o **user_name**, según qué campo se use para identificar de forma única a los usuarios en la implementación de ServiceNow.
   
    > [!NOTE]
    > Puede configurar Azure AD para que emita el identificador de usuario de Azure AD (nombre principal de usuario) o la dirección de correo electrónico como identificador único en el token SAML. Para ello, vaya a la sección **ServiceNow > Atributos > Inicio de sesión único** en el Portal de Azure clásico y asigne el campo que desee al atributo **nameidentifier**. El valor almacenado para el atributo seleccionado en Azure AD (por ejemplo, nombre principal de usuario) debe coincidir con el valor almacenado en ServiceNow para el campo especificado (por ejemplo, user_name).
    > 
    > 
   
    f. Haga clic en **Guardar**. 

3. En el Portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurar inicio de sesión único")

4. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurar inicio de sesión único")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory para ServiceNow.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. En el Portal de administración de Azure clásico, en la página de integración de la aplicación de **ServiceNow**, haga clic en **Configurar aprovisionamiento de usuarios**. 
   
    ![Aprovisionamiento de usuarios](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Aprovisionamiento de usuarios")

2. En la página **Especifique sus credenciales de ServiceNow para habilitar el aprovisionamiento automático de usuarios**, proporcione los valores de configuración siguientes:
   
     a. En el cuadro de texto **Nombre de la instancia de ServiceNow** , escriba el nombre de la instancia de ServiceNow.
   
     b. En el cuadro de texto **Nombre del usuario administrador de ServiceNow** , escriba el nombre de la cuenta de administrador de ServiceNow.
   
     c. En el cuadro de texto **Contraseña de administración de ServiceNow** , escriba la contraseña para esta cuenta.
   
     d. Haga clic en **validar** para comprobar la configuración.
   
     e. Haga clic en el botón **Siguiente** para abrir la página **Pasos siguientes**.
   
     f. Si quiere aprovisionar todos los usuarios para esta aplicación, seleccione**Aprovisionar automáticamente todas las cuentas del directorio en esta aplicación**. 
   
    ![Pasos siguientes](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Pasos siguientes")
   
     g. En la página **Pasos siguientes**, haga clic en **Completar** para guardar la configuración.

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.

6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-servicenow-test-user"></a>Creación de un usuario de prueba de ServiceNow
En esta sección, creará un usuario llamado Britta Simon en ServiceNow. En esta sección, creará un usuario llamado Britta Simon en ServiceNow. Si no sabe cómo agregar un usuario en una cuenta de ServiceNow o ServiceNow Express, póngase en contacto con el equipo de soporte técnico de ServiceNow.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a ServiceNow.

![Asignar usuario][200] 

**Para asignar Britta Simon a ServiceNow, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **ServiceNow**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 

4. En la lista Todos los usuarios, seleccione **Britta Simon**.

5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de ServiceNow en el panel de acceso, debería iniciar sesión automáticamente en su aplicación ServiceNow.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
