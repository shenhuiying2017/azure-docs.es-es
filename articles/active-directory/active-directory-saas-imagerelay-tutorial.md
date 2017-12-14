---
title: "Tutorial: Integración de Azure Active Directory con Image Relay | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory e Image Relay."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: da05da676ea160dcdaf7e9c711d5f308ac260434
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Tutorial: Integración de Azure Active Directory con Image Relay

En este tutorial, obtendrá información sobre cómo integrar Image Relay con Azure Active Directory (Azure AD).

La integración de Image Relay con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Image Relay
- Puede permitir que los usuarios inicien sesión automáticamente en Image Relay (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Image Relay, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en Image Relay

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Image Relay desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-image-relay-from-the-gallery"></a>Incorporación de Image Relay desde la galería
Para configurar la integración de Image Relay en Azure AD, es preciso agregar Image Relay desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Image Relay desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Image Relay**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_search.png)

5. En el panel de resultados, seleccione **Image Relay** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Image Relay con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Image Relay para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Image Relay.

Para establecer la relación de vínculo, en Image Relay, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Image Relay, es necesario completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Image Relay](#creating-an-image-relay-test-user)**: para tener un homólogo de Britta Simon en Image Relay que esté vinculado a la representación de usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Image Relay.

**Para configurar el inicio de sesión único de Azure AD con Image Relay, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Image Relay**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

3. En la sección **Dominio y direcciones URL de Image Relay**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.imagerelay.com/`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de Image Relay](http://support.imagerelay.com/) para obtener estos valores. 
 


4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Image Relay**, haga clic en **Configurar Image Relay** para abrir la ventana **Configurar inicio de sesión**. Copie los valores de la **dirección URL del servicio de cierre de sesión y SAML Single Sign-On Service URL (Dirección URL del servicio de inicio de sesión único de SAML)** de la **sección Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_configure.png) 

7. En otra ventana del explorador, inicie sesión en su sitio de empresa de Image Relay como administrador.

8. En la barra de herramientas de la parte superior, haga clic en la carga de trabajo **Users & Permissions** (Usuarios y permisos).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 

9. Haga clic en **Create New Permission**(Crear permiso nuevo).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png)

10. En la carga de trabajo **Single Sign On Settings** (Configuración de inicio de sesión único), active la casilla **This Group can only sign-in via Single Sign On** (Este grupo solo puede iniciar sesión mediante inicio de sesión único) y haga clic en **Save** (Guardar).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 

11. Vaya a **Account Settings**(Configuración de cuenta).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 

12. Vaya a la carga de trabajo **Single Sign On Settings** (Configuración de inicio de sesión único).
    
     ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

13. En el cuadro de diálogo **SAML Settings** (Configuración de SAML), siga estos pasos:
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. En el cuadro de texto **Dirección URL de inicio de sesión**, pegue el valor de la **dirección URL de inicio de sesión único** que ha copiado de Azure Portal.

    b. En el cuadro de texto **Logout URL** (Dirección URL de cierre de sesión), pegue el valor de **Dirección URL de cierre de sesión** que copió de Azure Portal.

    c. En **Name Id Format** (Formato de id. de nombre), seleccione **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    d. En **Binding Options for Requests from the Service Provider (Image Relay)** [Opciones de enlace para solicitudes del proveedor de servicio (Image Relay)], seleccione **POST Binding** (Enlace POST).

    e. En **x.509 Certificate** (Certificado x.509), haga clic en **Update Certificate** (Actualizar certificado).

    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Abra el certificado descargado en el Bloc de notas, copie el contenido y péguelo en el cuadro de texto x.509 Certificate (Certificado X.509).

    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. En **Just-In-Time User Provisioning** (Aprovisionamiento de usuarios Just-In-Time), active la casilla **Enable Just-In-Time User Provisioning** (Habilitar aprovisionamiento de usuarios Just-In-Time).

    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Seleccione el grupo de permisos, por ejemplo, **SSO Basic**(SSO básico), que puede iniciar sesión solo mediante inicio de sesión único.

    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-an-image-relay-test-user"></a>Creación de un usuario de prueba de Image Relay

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Image Relay.

**Para crear un usuario llamado Britta Simon en Image Relay, realice los pasos siguientes:**

1. Inicie sesión en su sitio de empresa de Image Relay como administrador.

2. Vaya a **Users & Permissions** (Usuarios y permisos) y seleccione **Create SSO User** (Crear usuario de SSO).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Escriba los valores de los campos **Email** (Correo electrónico), **First Name** (Nombre), **Last Name** (Apellidos) y **Company** (Empresa) del usuario que desea aprovisionar y seleccione el grupo de permisos, por ejemplo, SSO Basic (SSO básico), que es el grupo que solo puede iniciar sesión mediante inicio de sesión único.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 

4. Haga clic en **Crear**.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Image Relay.

![Asignar usuario][200] 

**Para asignar Britta Simon a Image Relay, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Image Relay**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.    

Al hacer clic en el icono de Image Relay en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Image Relay.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png

