---
title: 'Tutorial: Integración de Azure Active Directory con LaunchDarkly | Microsoft Docs'
description: Obtenga información para configurar el inicio de sesión único entre Azure Active Directory y LaunchDarkly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0e9cb37e-16bf-493b-bfc8-8d9840545a1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: f6611009ae10bf7a0d5c7f20f3ad26e9d41e456e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34340716"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Tutorial: Integración de Azure Active Directory con LaunchDarkly

En este tutorial se aprende a integrar LaunchDarkly con Azure Active Directory (Azure AD).

La integración de LaunchDarkly con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a LaunchDarkly.
- Puede permitir que los usuarios inicien sesión automáticamente en LaunchDarkly (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con LaunchDarkly, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en LaunchDarkly

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de LaunchDarkly desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-launchdarkly-from-the-gallery"></a>Incorporación de LaunchDarkly desde la galería
Para configurar la integración de LaunchDarkly en Azure AD, debe agregar LaunchDarkly desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar LaunchDarkly desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **LaunchDarkly**, seleccione **LaunchDarkly** en el panel de resultados y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![LaunchDarkly en la lista de resultados](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección se configura y se prueba el inicio de sesión único de Azure AD con LaunchDarkly con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de LaunchDarkly para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de LaunchDarkly.

Para configurar y probar el inicio de sesión único de Azure AD con LaunchDarkly, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de LaunchDarkly](#create-a-launchdarkly-test-user)**: para tener un homólogo de Britta Simon en LaunchDarkly que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección se habilita el inicio de sesión único de Azure AD en Azure Portal y se configura en la aplicación LaunchDarkly.

**Para configurar el inicio de sesión único de Azure AD con LaunchDarkly, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **LaunchDarkly**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_samlbase.png)

3. En la sección **Dominio y direcciones URL de LaunchDarkly**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de LaunchDarkly](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_url.png)

    a. En el cuadro de texto **Identificador (Id. de entidad)**, escriba la dirección URL: `app.launchdarkly.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`.
    
    > [!NOTE]
    > El valor de dirección URL de respuesta no es real. El valor se actualizará con la dirección URL de respuesta real, que se explica más adelante en el tutorial.

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de LaunchDarkly](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://app.launchdarkly.com`

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_400.png)
    
7. En la sección **Configuración de LaunchDarkly**, haga clic en **Configurar LaunchDarkly** para abrir la ventana **Configurar inicio de sesión**. Copie la **Dirección URL del servicio de inicio de sesión único** de la sección **Referencia rápida**.

    ![Configuración de LaunchDarkly](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_configure.png)

8. En otra ventana del explorador web, inicie sesión en el sitio de LaunchDarkly de la compañía como administrador.

9. Seleccione **Configuración de cuenta** en el panel de navegación izquierdo.

    ![Configuración de LaunchDarkly](./media/active-directory-saas-launchdarkly-tutorial/configure1.png)

10. Haga clic en la pestaña **Seguridad**.

    ![Configuración de LaunchDarkly](./media/active-directory-saas-launchdarkly-tutorial/configure2.png)

11. Haga clic en **HABILITAR SSO** y luego en **EDITAR CONFIGURACIÓN DE SAML**.

    ![Configuración de LaunchDarkly](./media/active-directory-saas-launchdarkly-tutorial/configure3.png)

12. En la sección **Editar configuración de SAML**, realice los siguientes pasos:

    ![Configuración de LaunchDarkly](./media/active-directory-saas-launchdarkly-tutorial/configure4.png)

    a. Copie la **dirección URL del servicio del consumidor de SAML** de la instancia y péguela en el cuadro de texto URL de respuesta de la sección **Dominio y direcciones URL de LaunchDarkly** de Azure Portal.

    b. En el cuadro de texto **URL de inicio de sesión**, pegue el valor de **Dirección URL del servicio de inicio de sesión único** que ha copiado de Azure Portal.

    c. Abra el certificado descargado desde Azure Portal en el Bloc de notas, copie el contenido y luego péguelo en el cuadro **Certificado X.509** o cárguelo directamente al hacer clic **Cargar**.

    d. Haga clic en **Guardar**

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).

### <a name="create-a-launchdarkly-test-user"></a>Crear un usuario de prueba de LaunchDarkly

El objetivo de esta sección es crear un usuario de prueba denominado Britta Simon en LaunchDarkly. LaunchDarkly admite el aprovisionamiento Just-In-Time, que está habilitado de manera predeterminada. No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a LaunchDarkly, se crea un usuario, en caso de que no exista.
>[!Note]
>Si necesita crear manualmente un usuario, es preciso que se ponga contacto con el [equipo de soporte técnico de LaunchDarkly](mailto:support@launchdarkly.com).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección se habilita a Britta Simon para que use el inicio de sesión único de Azure al concederle acceso a LaunchDarkly.

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a LaunchDarkly, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **LaunchDarkly**.

    ![Vínculo a LaunchDarkly en la lista de aplicaciones](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de LaunchDarkly en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación LaunchDarkly.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_203.png

