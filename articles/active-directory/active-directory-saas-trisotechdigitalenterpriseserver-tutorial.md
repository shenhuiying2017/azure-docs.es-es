---
title: 'Tutorial: Integración de Azure Active Directory con Trisotech Digital Enterprise Server | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Trisotech Digital Enterprise Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 8be41cbdbea237d2075523f22caf52238d921f24
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352929"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Tutorial: Integración de Azure Active Directory con Trisotech Digital Enterprise Server

En este tutorial, aprenderá a integrar Trisotech Digital Enterprise Server con Azure Active Directory (Azure AD).

Integrar Trisotech Digital Enterprise Server con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Trisotech Digital Enterprise Server.
- Puede permitir que los usuarios inicien sesión automáticamente en Trisotech Digital Enterprise Server (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Trisotech Digital Enterprise Server, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción con inicio de sesión único de Trisotech Digital Enterprise Server

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Trisotech Digital Enterprise Server desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Adición de Trisotech Digital Enterprise Server desde la galería
Para configurar la integración de Trisotech Digital Enterprise Server en Azure AD, será preciso que agregue Trisotech Digital Enterprise Server desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Trisotech Digital Enterprise Server desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Trisotech Digital Enterprise Server**, seleccione **Trisotech Digital Enterprise Server** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Trisotech Digital Enterprise Server en la lista de resultados](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Trisotech Digital Enterprise Server con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Trisotech Digital Enterprise Server para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Trisotech Digital Enterprise Server.

Para configurar y probar el inicio de sesión único de Azure AD con Trisotech Digital Enterprise Server, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Trisotech Digital Enterprise Server](#create-a-trisotech-digital-enterprise-server-test-user)**: para tener un homólogo de Britta Simon en Trisotech Digital Enterprise Server que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, va a habilitar el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Trisotech Digital Enterprise Server.

**Para configurar el inicio de sesión único de Azure AD con Trisotech Digital Enterprise Server, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Trisotech Digital Enterprise Server**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_samlbase.png)

3. En la sección **Trisotech Digital Enterprise Server Domain and URLs** (Dominio y direcciones URL de Trisotech Digital Enterprise Server), lleve a cabo los pasos siguientes:

    ![Información de inicio de sesión único de dominio y direcciones URL de Trisotech Digital Enterprise Server](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.trisotech.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.trisotech.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de cliente de Trisotech Digital Enterprise Server](mailto:support@trisotech.com) para obtener estos valores.

4. En la sección **Certificado de firma de SAML**, haga clic en el botón Copiar para copiar la **dirección URL de metadatos de federación de la aplicación** y péguela en el Bloc de notas. 

    ![Vínculo de descarga del certificado](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_certificate.png)

5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_400.png)

6. En otra ventana del explorador web, inicie sesión en el sitio web de la empresa Trisotech Digital Enterprise Server Configuration como administrador.

7. Haga clic en el **icono de menú** y, después, seleccione **Administration** (Administración).

    ![Configurar inicio de sesión único](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/user1.png)

8. Seleccione **User Provider** (Proveedor de usuarios).

    ![Configurar inicio de sesión único](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/user2.png)

9. En la sección **User Provider Configurations** (Configuración de proveedor de usuarios), realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Seleccione **Secured Assertion Markup Language 2 (SAML 2)** en la lista desplegable **Authentication Method** (Método de autenticación).

    b. En el cuadro de texto **URL de metadatos**, pegue el valor de **Dirección URL de metadatos de federación de la aplicación** que copió en Azure Portal.

    c. En el cuadro de texto **Application ID** (Application ID), escriba la dirección URL usando el patrón siguiente: `https://<companyname>.trisotech.com`.

    d. Haga clic en **Guardar**

    e. Escriba el nombre de dominio en el cuadro de texto **Dominios permitidos (vacío significa todos)** para asignar automáticamente licencias para usuarios que coinciden con los dominios permitidos.

    f. Haga clic en **Guardar**

 ### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-trisotech-digital-enterprise-server-test-user"></a>Creación de un usuario de prueba de Trisotech Digital Enterprise Server

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Trisotech Digital Enterprise Server. Trisotech Digital Enterprise Server admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a Trisotech Digital Enterprise Server, se crea un nuevo usuario, en caso de que no exista.
>[!Note]
>Si tiene que crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de Trisotech Digital Enterprise Server](mailto:support@trisotech.com).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Trisotech Digital Enterprise Server.

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a Trisotech Digital Enterprise Server, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Trisotech Digital Enterprise Server**.

    ![Vínculo de Trisotech Digital Enterprise Server en la lista de aplicaciones](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Trisotech Digital Enterprise Server en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Trisotech Digital Enterprise Server.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_203.png

