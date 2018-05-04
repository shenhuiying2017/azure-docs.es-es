---
title: 'Tutorial: Integración de Azure Active Directory con OrgChart Now | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y OrgChart Now.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: 8af344f6aa3cd666d655405f927542850b770ae5
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/30/2018
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Tutorial: Integración de Azure Active Directory con OrgChart Now

En este tutorial, aprenderá a integrar OrgChart Now con Azure Active Directory (Azure AD).

La integración de OrgChart Now con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a OrgChart Now.
- Puede permitir que los usuarios inicien sesión automáticamente en OrgChart Now (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con OrgChart Now, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en OrgChart Now

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de OrgChart Now desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-orgchart-now-from-the-gallery"></a>Adición de OrgChart Now desde la galería
Para configurar la integración de OrgChart Now en Azure AD, deberá agregar OrgChart Now de la galería a la lista de aplicaciones SaaS administradas.

**Para agregar OrgChart Now desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **OrgChart Now**, seleccione **OrgChart Now** en el panel de resultados y haga clic en el botón **Agregar** para agregar la aplicación.

    ![OrgChart Now en la lista de resultados](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con OrgChart Now con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de OrgChart Now para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de OrgChart Now.

Para configurar y probar el inicio de sesión único de Azure AD con OrgChart Now, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de OrgChart Now](#create-an-orgchart-now-test-user)**: para tener un homólogo de Britta Simon en OrgChart Now que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación OrgChart Now.

**Para configurar el inicio de sesión único de Azure AD con OrgChart Now, realice los pasos siguientes:**

1. En la página de integración de la aplicación **OrgChart Now** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

3. Vaya a la sección **OrgChart Now Domain and URLs** (Dominio y direcciones URL de OrgChart Now), si quiere configurar la aplicación en modo iniciado por **IDP**:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    En el cuadro de texto **Identificador**, escriba una dirección URL como: `https://sso2.orgchartnow.com`

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`.
     
    > [!NOTE]
    > `<YourEntityID>` es el Identificador de entidad de SAML que copió en la sección de referencia rápida, que se describe más adelante en el tutorial.

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_400.png)
    
7. En la sección **Configuración de OrgChart Now**, haga clic en **Configurar OrgChart Now** para abrir la ventana **Configurar inicio de sesión**. Copie el **identificador de entidad SAML** de la **sección de referencia rápida** y úselo para completar la **dirección URL de inicio de sesión** en la sección **OrgChart Now Domain and URLs section** (Dominio y direcciones URL de OrgChart Now).

    ![Configuración de OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

8. Para configurar el inicio de sesión único en **OrgChart Now**, es necesario enviar el archivo **XML de metadatos** descargado al [equipo de soporte técnico de OrgChart Now](mailto:ocnsupport@officeworksoftware.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-an-orgchart-now-test-user"></a>Creación de un usuario de prueba de OrgChart Now

Para permitir que los usuarios de Azure AD inicien sesión en OrgChart Now, deben aprovisionarse en OrgChart Now. 

1. OrgChart Now admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. Al intentar acceder a OrgChart Now, se crea un nuevo usuario, en caso de que no exista. La característica de aprovisionamiento de usuarios Just-in-Time solo crea un usuario de **solo lectura** cuando la solicitud de inicio de sesión único procede de un IDP reconocido y el correo electrónico de la aserción de SAML no se encuentra en la lista de usuarios. Para esta característica de aprovisionamiento automático, es necesario crear un grupo de acceso llamado **General** en OrgChart Now. Siga los pasos siguientes para crear un grupo de acceso:

    a. Después de hacer clic en el **engranaje** en la esquina superior derecha de la interfaz de usuario, vaya a la opción **Manage Groups** (Administrar grupos).

    ![Grupos de OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_manage.png)  

    b. Seleccione el icono **Add** (Agregar) y asigne al grupo el nombre **General**; a continuación, haga clic en **OK** (Aceptar). 

    ![OrgChart Now: agregar](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Seleccione las carpetas a las que desea que tengan acceso los usuarios generales o de solo lectura:

    ![Carpetas de OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Bloquee** las carpetas para que solo los usuarios administrativos pueden modificarlas. Después, presione **OK** (Aceptar).

    ![OrgChart Now: bloquear](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Para crear usuarios **administradores** y usuarios de **lectura/escritura**, debe crear manualmente un usuario con el fin de obtener acceso a su nivel de privilegios mediante inicio de sesión único. Para aprovisionar una cuenta de usuario, realice estos pasos:

    a. Inicie sesión ahora en OrgChart Now como administrador de seguridad.

    b.  Haga clic en **Settings** (Configuración) en la esquina superior derecha y, luego, vaya a **Manage Users** (Administrar usuarios).

    ![Configuración de OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Haga clic en **Add** (Agregar) y realice los siguientes pasos:

    ![OrgChart Now: administrar](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * En el cuadro de texto **User ID** (Id. de usuario), escriba el identificador de usuario, por ejemplo, **brittasimon@contoso.com**.

    * En el cuadro de texto **Email Address** (Dirección de correo electrónico), escriba el correo electrónico del usuario, por ejemplo, **brittasimon@contoso.com**.

    * Haga clic en **Agregar**.
    
### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a OrgChart Now.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a OrgChart Now, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **OrgChart Now**.

    ![El vínculo de OrgChart Now en la lista de aplicaciones](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de OrgChart Now en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación OrgChart Now.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_203.png

