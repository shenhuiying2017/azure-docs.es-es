---
title: "Tutorial: integración de Azure Active Directory con el software Cezanne HR | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y el software Cezanne HR."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 623c438edfce5f98c2d32d8bb25a97d86aa77909
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="tutorial-integrate-azure-active-directory-with-cezanne-hr-software"></a>Tutorial: Integración de Azure Active Directory con el software Cezanne HR

En este tutorial, obtendrá información sobre cómo integrar Cezanne HR Software con Azure Active Directory (Azure AD).

La integración del software Cezanne HR con Azure AD proporciona las siguientes ventajas. Puede:

- Controlar en Azure AD quién tiene acceso al software Cezanne HR.
- Permitir que los usuarios inicien sesión automáticamente en el software Cezanne HR con el inicio de sesión único (SSO) con sus cuentas de Azure AD.
- Administrar sus cuentas en una ubicación central: Azure Portal.

Para obtener más información sobre la integración de aplicaciones de software como servicio (SaaS) con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el SSO con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con el software Cezanne HR, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en el software Cezanne HR

> [!NOTE]
> No se recomienda usar un entorno de producción para probar los pasos de este tutorial.

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único (SSO) de Azure AD en un entorno de prueba. 

La situación descrita en este tutorial consta de dos bloques de creación principales:

* Adición del software Cezanne HR desde la galería
* Configuración y prueba del inicio de sesión único de Azure AD

## <a name="add-cezanne-hr-software-from-the-gallery"></a>Adición del software Cezanne HR desde la galería
Para configurar la integración del software Cezanne HR en Azure AD, agréguelo desde la galería a la lista de aplicaciones SaaS administradas.

Para agregar el software Cezanne HR desde la galería, realice lo siguiente:

1. En el panel izquierdo de **[Azure Portal](https://portal.azure.com)**, seleccione el botón **Azure Active Directory**. 

    ![Botón "Azure Active Directory"][1]

2. Seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Vínculo "Todas las aplicaciones"][2]
    
3. Para agregar una aplicación nueva, en la parte superior del cuadro de diálogo **Todas las aplicaciones**, seleccione **Nueva aplicación**.

    ![Botón “Nueva aplicación”][3]

4. En el cuadro de búsqueda, escriba **Software Cezanne HR**.

    ![El cuadro de búsqueda](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_search.png)

5. En la lista de resultados, seleccione **Cezanne HR Software** y haga clic en **Agregar** para agregar la aplicación.

    ![Lista de resultados](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con el software Cezanne HR con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo en el software Cezanne HR para el usuario de Azure AD. Es decir, debe establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado del software Cezanne HR.

Para establecer la relación de vínculo, asigne el valor **nombre de usuario** del software Cezanne HR como valor de **nombre de usuario** de Azure AD.

Para configurar y probar el inicio de sesión único de Azure AD con el software Cezanne HR, complete los siguientes bloques de creación.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, puede habilitar el inicio de sesión único de Azure AD en Azure Portal y configurar el inicio de sesión único en la aplicación del software Cezanne HR de la siguiente manera:

1. En Azure Portal, en la página de integración de la aplicación **Cezanne HR Software**, seleccione **Inicio de sesión único**.

    ![Comando "Inicio de sesión único"][4]

2. Para habilitar el inicio de sesión único, en el cuadro de diálogo **Inicio de sesión único**, seleccione el **Modo** en **Inicio de sesión basado en SAML**.
 
    ![Cuadro "Modo"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. En **Dominio y direcciones URL de Cezanne HR Software**, realice lo siguiente:

    ![Sección “Dominio y direcciones URL de Cezanne HR Software”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. En el cuadro **URL de inicio de sesión**, escriba una dirección URL que tenga la siguiente sintaxis: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`

    b. En el cuadro **URL de respuesta**, escriba una dirección URL que tenga la siguiente sintaxis: `https://w3.cezanneondemand.com:443/<tenantid>`    
     
    > [!NOTE] 
    > Los valores anteriores no son reales. Actualícelos con los valores reales de URL de respuesta y URL de inicio de sesión. Para obtener estos valores, póngase en contacto con el [equipo de soporte al cliente de Cezanne HR Software](mailto:info@cezannehr.com).

4. En **Certificado de firma de SAML**, seleccione **Certificado (Base64)** y, luego, guarde el archivo del certificado en el equipo.

    ![Sección "Certificado de firma de SAML"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Seleccione **Guardar**.

    ![Botón “Guardar”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)
    
6. En **Configuración de Cezanne HR Software**, seleccione **Configurar Cezanne HR Software** para abrir la ventana **Configurar inicio de sesión**. Copie el **Identificador de entidad de SAML** y la dirección URL del **Servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Sección “Configuración de Cezanne HR Software”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. En otra ventana del explorador web, inicie sesión en el inquilino del software Cezanne HR como administrador.

8. En el panel izquierdo, seleccione **Configuración del sistema**. Seleccione **Configuración de seguridad** > **Configuración de inicio de sesión único**.

    ![Vínculo “Configuración de inicio de sesión único”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. En el panel **Permitir a los usuarios iniciar sesión mediante el siguiente servicio de inicio de sesión único**, seleccione la casilla **SAML 2.0** y, luego, la opción **Configuración avanzada**.

    ![Opciones de inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. Seleccione **Agregar nuevo**.

    ![Botón “Agregar nuevo”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. En **Proveedores de identidades SAML 2.0**, realice lo siguiente:

    ![Sección “Proveedores de identidades SAML 2.0”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. En el cuadro **Nombre para mostrar**, escriba el nombre de su proveedor de identidades.

    b. En el cuadro **Identificador de entidad**, pegue el **Identificador de entidad de SAML** que copió de Azure Portal. 

    c. En el cuadro de lista **Enlace SAML**, seleccione **PUBLICAR**.

    d. En el cuadro **Punto de conexión de servicio de token de seguridad**, pegue la dirección URL del **Servicio de inicio de sesión único de SAML** que copió de Azure Portal. 
    
    e. En el cuadro **Nombre de atributo de Id. de usuario**, escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Para cargar el certificado descargado de Azure AD, seleccione el botón **CARGAR**.
    
    g. Seleccione **Aceptar**. 

12. Seleccione **Guardar**.

    ![Botón “Guardar”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Cuando configure la aplicación, puede leer una versión concisa de las instrucciones anteriores en [Azure Portal](https://portal.azure.com). Después de agregar la aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, seleccione la pestaña **Inicio de sesión único**. A continuación, consulte la documentación insertada en la sección **Configuración**. 

Para obtener más información sobre la característica de documentación insertada, vea la [documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará el usuario de prueba Britta Simon en Azure Portal.

![Usuario de prueba Britta Simon][100]

Haga lo siguiente para crear un usuario de prueba en Azure AD:

1. En el panel izquierdo de **Azure Portal**, seleccione el botón **Azure Active Directory**.

    ![Botón "Azure Active Directory"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. Para mostrar una lista de usuarios, seleccione **Usuarios y grupos** > **Todos los usuarios**.
    
    ![Vínculo "Todos los usuarios"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 
    
    Se abrirá el cuadro de diálogo **Todos los usuarios**.

3. Para abrir el cuadro de diálogo **Usuario**, seleccione **Agregar**.
 
    ![Botón "Agregar"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. En el cuadro de diálogo **Usuario**, haga lo siguiente:
 
    ![Cuadro de diálogo "Usuario"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione la casilla **Mostrar contraseña** y, después , anote el valor que se generó en el cuadro **Contraseña**.

    d. Seleccione **Crear**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Creación de un usuario de prueba del software Cezanne HR

Para permitir que los usuarios de Azure AD inicien sesión en el software Cezanne HR, tienen que aprovisionarse en él. En el caso del software Cezanne HR, el aprovisionamiento es una tarea manual.

Para aprovisionar una cuenta de usuario, realice lo siguiente:

1.  Inicie sesión en su sitio de la compañía del software Cezanne HR como administrador.

2.  En el panel izquierdo, seleccione **Configuración del sistema** > **Administrar usuarios** > **Adición de un nuevo usuario**.

    ![Vínculo “Adición de un nuevo usuario”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nuevo usuario")

3.  En **Detalles de la persona**, realice lo siguiente:

    ![Sección "Detalles de la persona"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nuevo usuario")
    
    a. Establezca **Usuario interno** en **DESACTIVADO**.
    
    b. En el cuadro de texto **Nombre**, escriba el nombre del usuario, por ejemplo, **Britta**.  
 
    c. En el cuadro de texto **Apellido**, escriba el apellido del usuario, por ejemplo, **Simon**.
    
    d. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico del usuario, por ejemplo, Brittasimon@contoso.com.

4.  En **Información de la cuenta**, realice lo siguiente:

    ![Sección “Información de la cuenta”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nuevo usuario")
    
    a. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario, por ejemplo, Brittasimon@contoso.com.
    
    b. En el cuadro de texto **Contraseña** , escriba la contraseña del usuario.
    
    c. En el cuadro de texto**Rol de seguridad**, seleccione **Profesional de RR.HH**.
    
    d. Seleccione **Aceptar**.

5. En la pestaña **Inicio de sesión único**, en la sección **Identificadores SAML 2.0**, seleccione **Agregar nuevo**.

    ![Botón “Agregar nuevo”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Usuario")

6. En el cuadro de lista **Proveedor de identidades**, seleccione el proveedor de identidades. En el cuadro **Identificador de usuario**, escriba la dirección de correo electrónico de la cuenta de usuario de prueba Britta Simon.

    ![Cuadros “Proveedor de identidades” e “Identificador de usuario”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Usuario")
    
7. Seleccione **Guardar**.

    ![Botón "Guardar"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Usuario")

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará al usuario de prueba Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso al software Cezanne HR.

![Acceso de usuario de prueba][200] 

1. En Azure Portal, abra la vista de aplicaciones y vaya a la vista de directorio. Seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Vínculo "Todas las aplicaciones"][201] 

2. En la lista de aplicaciones, seleccione **Software Cezanne HR**.

    ![Lista “Aplicaciones”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png) 

3. En el menú de la izquierda, seleccione **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Seleccione **Agregar**. Después, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][203]

5. En el cuadro de diálogo **Usuarios y grupos**, en la lista **Usuarios** seleccione **Britta Simon**.

6. En el cuadro de diálogo **Usuarios y grupos**, elija **Seleccionar**.

7. En el cuadro de diálogo **Agregar asignación**, seleccione **Asignar**.
    
### <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el panel de acceso.

Cuando selecciona el icono del software Cezanne HR en el panel de acceso, inicia sesión automáticamente en la aplicación del software Cezanne HR.

## <a name="next-steps"></a>Pasos siguientes

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el SSO con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png

