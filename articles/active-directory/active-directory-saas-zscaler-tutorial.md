---
title: "Tutorial: integración de Azure Active Directory con Zscaler | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Zscaler."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: ffb413289e9bc284c8043bc867ab00a47c8e1516
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Tutorial: Integración de Azure Active Directory con Zscaler

En este tutorial, aprenderá a integrar Zscaler con Azure Active Directory (Azure AD).

La integración de Zscaler con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Zscaler
- Puede permitir que los usuarios inicien sesión automáticamente en Zscaler (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Zscaler, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Zscaler

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Zscaler desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-zscaler-from-the-gallery"></a>Incorporación de Zscaler desde la galería
Para configurar la integración de Zscaler en Azure AD, deberá agregar Zscaler desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Zscaler desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Zscaler**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_search.png)

5. En el panel de resultados, seleccione **Zscaler** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Zscaler con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Zscaler para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario asociado de Zscaler.

Para establecer la relación de vínculo, en Zscaler, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Zscaler, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Configuración de las opciones del proxy](#configuring-proxy-settings)**: para definir la configuración del proxy en Internet Explorer
3. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Zscaler](#creating-a-zscaler-test-user)**: para tener un homólogo de Britta Simon en Zscaler vinculado a la representación del usuario en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
6. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Zscaler.

**Para configurar el inicio de sesión único de Azure AD con Zscaler, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Zscaler**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_samlbase.png)

3. En la sección **Zscaler Domain and URLs** (Dominio y direcciones URL de Zscaler), lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.zsclaer.net`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de Zscaler](https://www.zscaler.com/company/contact) para obtenerlo. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/tutorial_general_400.png)

6. En la sección **Zscaler Configuration** (Configuración de Zscaler), haga clic en **Configure Zscaler** (Configurar Zscaler) para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_configure.png) 

7. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Zscaler como administrador.

8. En el menú de la parte superior, haga clic en **Administration**(Administración).
   
    ![Administración](./media/active-directory-saas-zscaler-tutorial/ic800206.png "Administración")

9. En **Manage Administrators & Roles (Administrar administradores y roles)** haga clic en **Manage Users & Authentication (Administrar usuarios y autenticación)**.   
            
    ![Administración de usuarios y autenticación](./media/active-directory-saas-zscaler-tutorial/ic800207.png "Administración de usuarios y autenticación")

10. En la sección **Choose Authentication Options for your Organization** (Elegir opciones de autenticación para su organización), lleve a cabo los pasos siguientes:   
                
    ![Autenticación](./media/active-directory-saas-zscaler-tutorial/ic800208.png "Autenticación")
   
    a. Seleccione **Authenticate using SAML Single Sign-On**(Autenticarse mediante el inicio de sesión único SAML).

    b. Haga clic en **Configure SAML Single Sign-On Parameters**(Configurar parámetros de inicio de sesión único SAML).

11. En la página de diálogo **Configure SAML Single Sign-On Parameters** (Configurar parámetros de inicio de sesión único SAML), realice estos pasos y luego haga clic en **Done** (Listo)

    ![Inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/ic800209.png "Inicio de sesión único")
    
    a. Pegue el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML), que ha copiado de Azure Portal, en el cuadro de texto **de la dirección URL del portal de SAML al que se dirige a los usuarios para la autenticación**.
    
    b. En el cuadro de texto **Attribute containing Login Name** (Atributo que contiene el nombre de inicio de sesión), escriba **NameID**.
    
    c. Para cargar el certificado descargado, haga clic en **pem de Zscaler**.
    
    d. Seleccione **Habilitar aprovisionamiento automático de SAML**.

12. En la página del cuadro de diálogo **Configurar autenticación de usuario** , realice los pasos siguientes:

    ![Administración](./media/active-directory-saas-zscaler-tutorial/ic800210.png "Administración")
    
    a. Haga clic en **Guardar**.

    b. Haga clic en **Activar ahora**.

## <a name="configuring-proxy-settings"></a>Configuración de los valores de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir la configuración de proxy en Internet Explorer

1. Inicie **Internet Explorer**.

2. Seleccione **Opciones de Internet** en el menú **Herramientas** para abrir el diálogo **Opciones de Internet**.   
    
     ![Opciones de Internet](./media/active-directory-saas-zscaler-tutorial/ic769492.png "Opciones de Internet")

3. Haga clic en la pestaña **Conexiones** .   
  
     ![Conexiones](./media/active-directory-saas-zscaler-tutorial/ic769493.png "Conexiones")

4. Haga clic en **Configuración de LAN** para abrir el diálogo **Configuración de LAN**.

5. En la sección del servidor proxy, lleve a cabo estos pasos:   
   
    ![Servidor proxy](./media/active-directory-saas-zscaler-tutorial/ic769494.png "Servidor proxy")

    a. Seleccione **Usar un servidor proxy para la LAN**.

    b. En el cuadro de texto Dirección, escriba **gateway.zscaler.net**.

    c. En el cuadro de texto Puerto, escriba **80**.

    d. Seleccione **No usar servidor proxy para direcciones locales**.

    e. Haga clic en **Aceptar** para cerrar el diálogo **Configuración de red de área local (LAN)**.

6. Haga clic en **Aceptar** para cerrar el diálogo **Opciones de Internet**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-zscaler-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-zscaler-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-zscaler-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-zscaler-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-zscaler-test-user"></a>Creación de un usuario de prueba de Zscaler

Para permitir que los usuarios de Azure AD inicien sesión en Zscaler, deben aprovisionarse en Zscaler.  
En el caso de Zscaler, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:

1. Inicie sesión en su inquilino de **Zscaler** .

2. Haga clic en **Administración**.   
   
    ![Administración](./media/active-directory-saas-zscaler-tutorial/ic781035.png "Administración")

3. Haga clic en **User Management**(Administración de usuarios).   
        
     ![Agregar](./media/active-directory-saas-zscaler-tutorial/ic781036.png "Agregar")

4. En la pestaña **Usuarios**, haga clic en **Agregar**.
      
    ![Agregar](./media/active-directory-saas-zscaler-tutorial/ic781037.png "Agregar")

5. En la sección Agregar usuario, lleve a cabo estos pasos:
        
    ![Agregar usuario](./media/active-directory-saas-zscaler-tutorial/ic781038.png "Agregar usuario")
   
    a. Escriba el **Id. de usuario**, el **Nombre para mostrar del usuario**, la **Contraseña**, **Confirmar contraseña** y después seleccione **Grupos** y el **Departamento** de una cuenta de AAD válida que quiera aprovisionar.

    b. Haga clic en **Guardar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Zscaler ofrecida por Zscaler para aprovisionar cuentas de usuario de AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Zscaler.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Zscaler, lleve a cabo los siguientes pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Zscaler**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Zscaler del Panel de acceso, iniciará sesión en la aplicación Zscaler automáticamente.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_203.png

