---
title: "Tutorial: Integración de Azure Active Directory con Google Apps | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Google Apps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: f3b0d48534113dea152aba632e59d03ed78db301
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>Tutorial: Integración de Azure Active Directory con Google Apps

En este tutorial, obtendrá información sobre cómo integrar Google Apps con Azure Active Directory (Azure AD).

La integración de Google Apps con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a Google Apps.
- Puede permitir que los usuarios inicien sesión automáticamente en Google Apps (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Google Apps, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Google Apps

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Preguntas frecuentes
1. **P: ¿Son los Chromebooks y otros dispositivos Chrome compatibles con el inicio de sesión único de Azure AD?**
   
    R: Sí, los usuarios pueden iniciar sesión en sus dispositivos Chromebook con sus credenciales de Azure AD. Consulte este [artículo de soporte técnico de Google Apps](https://support.google.com/chrome/a/answer/6060880) para información sobre por qué puede que se pidan las credenciales a los usuarios dos veces.

2. **P: Si se habilita el inicio de sesión único, ¿podrán usar los usuarios sus credenciales de Azure AD para iniciar sesión en cualquier producto de Google, como Google Classroom, GMail, Google Drive, YouTube, etc.?**
   
    R: Sí, en función de [qué aplicaciones de Google](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) decida habilitar o deshabilitar para su organización.

3. **P: ¿Puedo habilitar el inicio de sesión único solo para un subconjunto de mis usuarios de Google Apps?**
   
    R: No; si activa el inicio de sesión único, es necesario de inmediato que todos los usuarios de Google Apps se autentiquen con sus credenciales de Azure AD. Dado que Google Apps no admite tener varios proveedores de identidades, el proveedor de identidades para su entorno de Google Apps puede ser Azure AD o Google, pero no ambos al mismo tiempo.

4. **P: Si un usuario inicia sesión a través de Windows, ¿se autentica automáticamente en Google Apps sin que se le pida una contraseña?**
   
    R: Hay dos opciones para habilitar este escenario. En primer lugar, los usuarios podrían iniciar sesión en dispositivos Windows 10 a través de [Azure Active Directory Join](active-directory-azureadjoin-overview.md). Como alternativa, los usuarios podrían iniciar sesión en dispositivos Windows que están unidos a un dominio en un entorno Active Directory local que se ha habilitado para el inicio de sesión único en Azure AD a través de una implementación de los [Servicios de federación de Active Directory (AD FS)](active-directory-aadconnect-user-signin.md) . Ambas opciones requieren que realice los pasos del tutorial siguiente para permitir el inicio de sesión único entre Azure AD y Google Apps.

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Google Apps desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-google-apps-from-the-gallery"></a>Incorporación de Google Apps desde la galería
Para configurar la integración de Google Apps en Azure AD, será preciso que agregue Google Apps desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Google Apps desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Google Apps**, seleccione **Google Apps** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Google Apps en la lista de resultados](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Google Apps con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Google Apps para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Google Apps.

Para establecer la relación de vínculo, en Google Apps, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Google Apps, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Google Apps](#create-a-google-apps-test-user)**: para tener un homólogo de Britta Simon en Google Apps que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Google Apps.

**Para configurar el inicio de sesión único de Azure AD con Google Apps, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Google Apps**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_samlbase.png)

3. En la sección de **dominio y direcciones URL de Google Apps**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Google Apps](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://mail.google.com/a/<yourdomain.com>`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón:

    | |
    |--|
    | `google.com`|
    | `http://google.com`|
    | `google.com/<yourdomain.com>`|
    | `http://google.com/a/<yourdomain.com>`|
       
    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de Google Apps](https://www.google.com/contact/) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-googleapps-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Google Apps**, haga clic en **Configurar Google Apps** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de cierre de sesión, la dirección URL del servicio de inicio de sesión único de SAML y la dirección URL de cambio de contraseña** de la **sección de referencia rápida**.

    ![Configuración de Google Apps](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_configure.png) 

7. Abra una nueva pestaña en el explorador e inicie sesión en la [consola de administración de Google Apps](http://admin.google.com/) con su cuenta de administrador.

8. Haga clic en **Seguridad**. Si no ve el vínculo, puede estar oculto debajo del menú **Más controles** en la parte inferior de la pantalla.
   
    ![Haga clic en Seguridad.][10]

9. En la página **Seguridad**, haga clic en **Configurar inicio de sesión único (SSO)**.
   
    ![Haga clic en SSO.][11]

10. Realice los cambios de configuración siguientes:
   
    ![Configuración de SSO][12]
   
    a. Seleccione **Configurar SSO con un proveedor de identidades de terceros**.

    b. En el campo **Dirección URL de la página de inicio de sesión** en Google Apps, pegue el valor de la **dirección URL del servicio de inicio de sesión único** que copió desde Azure Portal.

    c. En el campo **Dirección URL de cierre de sesión** en Google Apps, pegue el valor de la **dirección URL de cierre de sesión** que copió desde Azure Portal. 

    d. En el campo **Dirección URL de cambio de contraseña** en Google Apps, pegue el valor de la **dirección URL de cambio de contraseña** que copió desde Azure Portal. 

    e. En Google Apps, para el **certificado de verificación**, cargue el certificado que descargó de Azure Portal.

    f. Haga clic en **Guardar cambios**.

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

    d. Haga clic en **Crear**.
 
### <a name="create-a-google-apps-test-user"></a>Creación de un usuario de prueba de Google Apps

El objetivo de esta sección es crear un usuario llamado Britta Simon en Google Apps Software. Google Apps admite el aprovisionamiento automático, que está habilitado de manera predeterminada. El usuario no tiene que hacer nada en esta sección. Si el usuario aún no existe en Google Apps Software, se crea uno nuevo cuando se intenta acceder a esta aplicación.

>[!NOTE] 
>Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de Google](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Google Apps.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a Google Apps, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Google Apps**.

    ![Vínculo de Google Apps en la lista de aplicaciones](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Google Apps en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de Google Apps.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

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

