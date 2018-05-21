---
title: 'Tutorial: integración de Azure Active Directory con G Suite | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: jeedes
ms.openlocfilehash: f785a74d5cab7c2fbfb0e2e1036dfddbb0f4838b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Tutorial: integración de Azure Active Directory con G Suite

En este tutorial, aprenderá a integrar G Suite con Azure Active Directory (Azure AD).

La integración de G Suite con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a G Suite.
- Puede permitir que los usuarios inicien sesión automáticamente en G Suite (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con G Suite, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada de G Suite para el inicio de sesión único
- Una suscripción de Google Apps o Google Cloud Platform

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Preguntas frecuentes
1.  **P: ¿Es esta integración compatible con la integración del inicio de sesión único de Google Cloud Platform con Azure AD?**
    
    R: Sí. Google Cloud Platform y Google Apps comparten la misma plataforma de autenticación. Por ello, para la integración de GCP debe configurar el inicio de sesión único con Google Apps.


2. **P: ¿Son los Chromebooks y otros dispositivos Chrome compatibles con el inicio de sesión único de Azure AD?**
   
    R: Sí, los usuarios pueden iniciar sesión en sus dispositivos Chromebook con sus credenciales de Azure AD. Consulte este [artículo de soporte técnico de G Suite](https://support.google.com/chrome/a/answer/6060880) para información sobre por qué se les pueden pedir a los usuarios las credenciales dos veces.

3. **P: Si se habilita el inicio de sesión único, ¿podrán usar los usuarios sus credenciales de Azure AD para iniciar sesión en cualquier producto de Google, como Google Classroom, GMail, Google Drive, YouTube, etc.?**
   
    R: Sí, en función de la [ versión de G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) que decida habilitar o deshabilitar para su organización.

4. **P: ¿Puedo habilitar el inicio de sesión único solo para un subconjunto de usuarios de G Suite?**
   
    R: No; si activa el inicio de sesión único, es necesario de inmediato que todos los usuarios de G Suite se autentiquen con sus credenciales de Azure AD. Dado que G Suite no admite varios proveedores de identidades, el proveedor de identidades del entorno de G Suite puede ser Azure AD o Google, pero no ambos al mismo tiempo.

5. **P: Si un usuario inicia sesión mediante Windows, ¿se autentica automáticamente en G Suite sin que se le pida la contraseña?**
   
    R: Hay dos opciones para habilitar este escenario. En primer lugar, los usuarios podrían iniciar sesión en dispositivos Windows 10 a través de [Azure Active Directory Join](active-directory-azureadjoin-overview.md). Como alternativa, los usuarios podrían iniciar sesión en dispositivos Windows que están unidos a un dominio en un entorno Active Directory local que se ha habilitado para el inicio de sesión único en Azure AD a través de una implementación de los [Servicios de federación de Active Directory (AD FS)](active-directory-aadconnect-user-signin.md) . Ambas opciones requieren los pasos del tutorial siguiente para permitir el inicio de sesión único entre Azure AD y G Suite.

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de G Suite desde la Galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-g-suite-from-the-gallery"></a>Incorporación de G Suite desde la Galería
Para configurar la integración de G Suite en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar G Suite desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **G Suite**, seleccione **G Suite** en el panel de resultados y haga clic en el botón **Agregar** para agregar la aplicación.

    ![G Suite en la lista de resultados](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con G Suite con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el homólogo de G Suite del usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre el usuario de Azure AD y el usuario asociado de G Suite.

Para establecer la relación de vínculo, en G Suite, asigne el valor de **nombre de usuario** de Azure AD como valor de **Username** (Nombre de usuario).

Para configurar y probar el inicio de sesión único de Azure AD con G Suite, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de G Suite](#create-a-g-suite-test-user)**: para tener un homólogo de Britta Simon en G Suite vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación G Suite.

**Para configurar el inicio de sesión único de Azure AD con G Suite, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **G Suite**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_samlbase.png)

3. En la sección **G Suite Domain and URLs** (Direcciones URL y dominio de G Suite), si desea configurar **Gmail**, realice los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de G Suite](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_urlgmail.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
 
    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de G Suite](https://www.google.com/contact/) para obtener estos valores.

4. En la sección **G Suite Domain and URLs** (Direcciones URL y dominio de G Suite), si desea configurar **Google Cloud Platform**, realice los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de G Suite](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_url1.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com `

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de G Suite](https://www.google.com/contact/) para obtener estos valores. 

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-googleapps-tutorial/tutorial_general_400.png)

7. En la sección **G Suite Configuration** (Configuración de G Suite), haga clic en **Configure G Suite** (Configurar G Suite) para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de cierre de sesión, la dirección URL del servicio de inicio de sesión único de SAML y la dirección URL de cambio de contraseña** de la **sección de referencia rápida**.

    ![Configuración de G Suite](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_configure.png) 

8. Abra una nueva pestaña en el explorador e inicie sesión en la [consola de administración de G Suite](http://admin.google.com/) con la cuenta de administrador.

9. Haga clic en **Seguridad**. Si no ve el vínculo, puede estar oculto debajo del menú **Más controles** en la parte inferior de la pantalla.
   
    ![Haga clic en Seguridad.][10]

10. En la página **Seguridad**, haga clic en **Configurar inicio de sesión único (SSO)**.
   
    ![Haga clic en SSO.][11]

11. Realice los cambios de configuración siguientes:
   
    ![Configuración de SSO][12]
   
    a. Seleccione **Configurar SSO con un proveedor de identidades de terceros**.

    b. En el campo **Sign-in page URL** (Dirección URL de la página de inicio de sesión) de G Suite, pegue el valor de la **URL de servicio de inicio de sesión único** que copió de Azure Portal.

    c. En el campo **Sign-out page URL** (Dirección URL de cierre de sesión) de G Suite, pegue el valor de la **URL de cierre de sesión** que copió de Azure Portal. 

    d. En el campo **Change password URL** (Dirección URL de cambio de contraseña) de G Suite, pegue el valor de la **URL de cambio de contraseña** que copió de Azure Portal. 

    e. En G Suite, para el **certificado de verificación** y cargue el certificado que descargó de Azure Portal.

    f. Seleccione **Use a domain specific issuer** (Usar un emisor de dominio específico).

    g. Haga clic en **Guardar cambios**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-googleapps-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-googleapps-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-googleapps-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-googleapps-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-g-suite-test-user"></a>Creación de un usuario de prueba de G Suite

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en el software G Suite. G Suite admite el aprovisionamiento automático, que está habilitado de manera predeterminada. El usuario no tiene que hacer nada en esta sección. Si el usuario aún no existe en el software G Suite, se crea uno al intentar acceder.

>[!NOTE] 
>Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de Google](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a G Suite.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a G Suite, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **G Suite**.

    ![Vínculo a G Suite en la lista de aplicaciones](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de G Suite en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación G Suite.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-googleapps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-googleapps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-googleapps-tutorial/gapps-sso-config.png

