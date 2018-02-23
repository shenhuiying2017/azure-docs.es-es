---
title: "Tutorial: Integración de Azure Active Directory con Zscaler Private Access Administrator | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Zscaler Private Access Administrator."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: bf0b7cbd8047dfdbc1a4503775e6d36f8e8a67c1
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Tutorial: Integración de Azure Active Directory con Zscaler Private Access Administrator

En este tutorial, aprenderá a integrar Zscaler Private Access Administrator con Azure Active Directory (Azure AD).

La integración de Zscaler Private Access Administrator con Azure AD ofrece las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Zscaler Private Access Administrator.
- Puede permitir que los usuarios inicien sesión automáticamente en Zscaler Private Access Administrator (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Zscaler Private Access Administrator, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único de Zscaler Private Access Administrator

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Zscaler Private Access Administrator desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Adición de Zscaler Private Access Administrator desde la galería
Para configurar la integración de Zscaler Private Access Administrator con Azure AD, debe agregar Zscaler Private Access Administrator desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Zscaler Private Access Administrator desde la galería, realice los siguientes pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Zscaler Private Access Administrator**, seleccione **Zscaler Private Access Administrator** en el panel de resultados y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Zscaler Private Access Administrator en la lista de resultados](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Zscaler Private Access Administrator con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD necesita saber cuál es el usuario homólogo en Zscaler Private Access Administrator para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en Zscaler Private Access Administrator.

Para configurar y probar el inicio de sesión único de Azure AD con Zscaler Private Access Administrator, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Zscaler Private Access Administrator](#create-a-zscaler-private-access-administrator-test-user)**: para tener un homólogo de Britta Simon en Zscaler Private Access Administrator que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Zscaler Private Access Administrator.

**Para configurar el inicio de sesión único en Azure AD con Zscaler Private Access Administrator, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de aplicaciones de **Zscaler Private Access Administrator**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

3. Vaya a la sección **Dominio y direcciones URL de Zscaler Private Access Administrator**, si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.private.zscaler.com/auth/sso`.

    c. Seleccione **Mostrar configuración avanzada de URL**.

    d. En el cuadro de texto **RelayState**, escriba un valor: `idpadminsso`.

4.  Si quiere configurar la aplicación en modo iniciado por **SP**, realice los siguientes pasos:

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.private.zscaler.com/auth/sso`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Zscaler Private Access Administrator](https://help.zscaler.com/zpa-submit-ticket) para obtener estos valores.
 
5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

7. En otra ventana del explorador web, inicie sesión en Zscaler Private Access Administrator como administrador.

8. En la parte superior, haga clic en **Administration** (Administración) y vaya a la sección **AUTHENTICATION** (Autenticación) y haga clic en **IdP Configuration** (Configuración de IdP).

    ![Administrador de Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

9. En la esquina superior derecha, haga clic en **Add IdP Configuration** (Agregar configuración de IdP). 

    ![Agregar IdP de Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

10. En la página **Add IdP Configuration** (Agregar configuración de IdP), realice estos pasos:
 
    ![Seleccionar IdP de Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Haga clic en **Select File** (Seleccionar archivo) para cargar el archivo de metadatos descargado de Azure AD en el campo **IdP Metadata File Upload** (Carga de archivo de metadatos de IdP).

    b. Lee los **metadatos de IdP** de Azure AD y rellena la información de todos los campos como se indica a continuación.

    ![Configurar IdP de Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Seleccione **Single Sign On** (Inicio de sesión único) como **Administrator** (Administrador).

    d. Seleccione el dominio en el campo **Domains** (Dominios).
    
    e. Haga clic en **Save**(Guardar).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Creación de un usuario de prueba en Zscaler Private Access Administrator

Para permitir que los usuarios de Azure AD inicien sesión en Zscaler Private Access Administrator, se les debe aprovisionar en Zscaler Private Access Administrator. El aprovisionamiento en Zscaler Private Access Administrator consiste en una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión como administrador en el sitio de la empresa de Zscaler Private Access Administrator.

2. En la parte superior, haga clic en **Administration** (Administración) y vaya a la sección **AUTHENTICATION** (Autenticación) y haga clic en **IdP Configuration** (Configuración de IdP).

    ![Administrador de Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Haga clic en **Administrators** (Administradores) en el lado izquierdo del menú.

    ![Administrador de Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. En la esquina superior derecha, haga clic en **Add Administrator** (Agregar administrador):

    ![Agregar administrador en Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. En la página **Add Administrator** (Agregar administrador), siga estos pasos:

    ![Administrador de usuarios de Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. En el cuadro de texto **Username** (Nombre de usuario), escriba el correo electrónico del usuario, en este caso **BrittaSimon@contoso.com**.

    b. En el cuadro de texto **Password** (Contraseña), escriba la contraseña.

    c. En el cuadro de texto **Confirm Password** (Confirmar contraseña), escriba la contraseña.

    d. Seleccione **Role** (Rol) como **Zscaler Private Access Administrator**.

    e. En el cuadro de texto **E-mail** (Correo electrónico), escriba el correo electrónico del usuario con el siguiente formato **BrittaSimon@contoso.com**.

    f. En el cuadro de texto **Phone** (Teléfono), escriba el número de teléfono.

    g. En el cuadro de texto **Timezone** (Zona horaria), seleccione la zona horaria.

    h. Haga clic en **Save**(Guardar).  

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Zscaler Private Access Administrator.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Zscaler Private Access Administrator, realice los siguientes pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Zscaler Private Access Administrator**.

    ![Vínculo a Zscaler Private Access Administrator en la lista de aplicaciones](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Zscaler Private Access Administrator en el Panel de acceso, debe iniciar sesión automáticamente en la aplicación Zscaler Private Access Administrator.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png

