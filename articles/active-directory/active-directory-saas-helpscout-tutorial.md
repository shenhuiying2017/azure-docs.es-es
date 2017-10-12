---
title: "Tutorial: Integración de Azure Active Directory con Help Scout | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Help Scout."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: fe8775bd39173b2adf1f82d32f5e851ef1c19931
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Tutorial: integración de Azure Active Directory con Help Scout

En este tutorial, obtendrá información sobre cómo integrar Help Scout con Azure Active Directory (Azure AD).

La integración de Help Scout con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a Help Scout.
- Puede permitir que los usuarios inicien sesión automáticamente en Help Scout (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Help Scout, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Help Scout

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Help Scout desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-help-scout-from-the-gallery"></a>Incorporación de Help Scout desde la galería
Para configurar la integración de Help Scout en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Help Scout desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Help Scout**, seleccione **Help Scout** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Help Scout en la lista de resultados](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Help Scout con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Help Scout para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Help Scout.

Help Scout utiliza direcciones de correo electrónico para los inicios de sesión, por lo que para establecer la relación de vínculo use la misma **dirección de correo electrónico** como **nombre de usuario** en Azure AD.

Para configurar y probar el inicio de sesión único de Azure AD con Help Scout, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de usuario de prueba de Help Scout](#create-a-help-scout-test-user)**: para tener un homólogo de Britta Simon en Help Scout que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Help Scout.

**Para configurar el inicio de sesión único de Azure AD con Help Scout, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Help Scout**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. En la sección **Dominio y direcciones URL de Help Scout**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

    a. **Identificador** es la opción **"Audience URI (Service Provider Entity ID)"** [URI de audiencia (Identificador de entidad del proveedor de servicios)] de Help Scout y empieza por `urn:`

    b. **URL de respuesta** es la opción **"Post-back URL (Assertion Consumer Service URL)"** [URL de devolución (URL del Servicio de consumidor de aserciones)] de Help Scout y empieza por `https://` 

    > [!NOTE] 
    > Los valores de estas direcciones URL se muestran solo con fines demostrativos. Tiene que actualizar estos valores con el identificador y la dirección URL de respuesta reales. Estos valores se obtienen en la pestaña **Single Sign-On** (Inicio de sesión único), en la sección Authentication (Autenticación), que se explica más adelante en el tutorial.

4. Si desea configurar la aplicación en modo iniciado por **SP**, active **Mostrar configuración avanzada de URL** y realice los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una URL como: `https://secure.helpscout.net/members/login/`
     
5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)


7. En la sección **Configuración de Help Scout**, haga clic en **Configurar Help Scout** para abrir la ventana **Configurar inicio de sesión**. Copie el valor de **SAML Single Sign-On Service URL** (URL del servicio de inicio de sesión único de SAML) de la sección de **referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-helpscout-tutorial/config.png) 

8. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Help Scout como administrador.

9. Una vez registrado, haga clic en **"Manage"** (Administrar) en el menú superior y seleccione **"Company"** (Compañía) en el menú desplegable.

    ![Configurar inicio de sesión único](./media/active-directory-saas-helpscout-tutorial/settings1.png) 
 
10. Seleccione **"Authentication"** (Autenticación) en el menú izquierdo. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-helpscout-tutorial/settings2.png) 

11. Esto le lleva a la sección de configuración de SAML, donde debe seguir estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-helpscout-tutorial/settings3.png) 
 
    a. Copie el valor de **Post-back URL (Assertion Consumer Service URL)** [URL de devolución (URL del Servicio de consumidor de aserciones)] y péguelo en el cuadro **URL de respuesta** en Azure Portal, en la sección **Dominio y direcciones URL** de Help Scout.
    
    b. Copie el valor de **Audience URI (Service Provider Entity ID)** [URI de audiencia (Identificador de entidad del proveedor de servicios)] y péguelo en el cuadro **Identificador** en Azure Portal, en la sección **Dominio y direcciones URL** de Help Scout.

12. Active **Enable SAML** (Habilitar SAML) y siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-helpscout-tutorial/settings4.png) 
 
    a. En el cuadro de texto **Single Sign-On URL** (Dirección URL de inicio de sesión único), pegue el valor de **Dirección URL del servicio de inicio de sesión único** que ha copiado de Azure Portal.
    
    b. Haga clic en **Upload Certificate** (Cargar certificado) para cargar el **Certificado (Base64)** que descargó de Azure Portal.

    c. Especifique el dominio o dominios de correo electrónico de su organización, por ej. `contoso.com` en el cuadro de texto **Email Domains** (Dominios de correo electrónico). Puede separar varios dominios mediante comas. Siempre que un administrador o un usuario de Help Scout entre en ese dominio específico en la [página de inicio de sesión de Help Scout](https://secure.helpscout.net/members/login/), se le redirigirá al proveedor de identidades para que se autentique con sus credenciales.

    d. Por último, puede cambiar **Force SAML Sign-on** (Forzar inicio de sesión de SAML) si desea que los usuarios solo inicien sesión en Help Scout mediante este método. Si aún así desea dejar la opción para que puedan conectarse con sus credenciales de Help Scout, puede dejarla desactivada. Incluso si esta opción está habilitada, el propietario de la cuenta siempre podrá iniciar sesión en Help Scout con su contraseña de la cuenta.

    e. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-help-scout-test-user"></a>Creación de un usuario de prueba de Help Scout

El objetivo de esta sección es crear una usuaria de prueba llamada Britta Simon en Help Scout. Help Scout admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe en Help Scout, se crea uno nuevo cuando se intenta acceder a Help Scout.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Help Scout.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a Help Scout, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Help Scout**.

    ![Vínculo a Help Scout en la lista de aplicaciones](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Help Scout en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Help Scout.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png

