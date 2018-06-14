---
title: 'Tutorial: Integración de Azure Active Directory con FiscalNote | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y FiscalNote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 55274f26-be7e-4514-964c-7186ecb55c4a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: a1c0175d0bb19e372730b850aa1f49a2c247b032
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342671"
---
# <a name="tutorial-azure-active-directory-integration-with-fiscalnote"></a>Tutorial: Integración de Azure Active Directory con FiscalNote

En este tutorial, obtendrá información sobre cómo integrar FiscalNote con Azure Active Directory (Azure AD).

La integración de FiscalNote con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a FiscalNote.
- Puede permitir que los usuarios inicien sesión automáticamente en FiscalNote (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con FiscalNote se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de FiscalNote habilitada para el inicio de sesión único

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de FiscalNote desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-fiscalnote-from-the-gallery"></a>Adición de FiscalNote desde la galería
Para configurar la integración de FiscalNote en Azure AD, será preciso que agregue FiscalNote desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar FiscalNote desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **FiscalNote**, seleccione **FiscalNote** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![FiscalNote en la lista de resultados](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con FiscalNote con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de FiscalNote para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de FiscalNote.

Para configurar y probar el inicio de sesión único de Azure AD con FiscalNote, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de FiscalNote](#create-a-fiscalnote-test-user)**: para tener un homólogo de Britta Simon en FiscalNote que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación FiscalNote.

**Para configurar el inicio de sesión único de Azure AD con FiscalNote, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **FiscalNote**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_samlbase.png)

3. En la sección **Dominio y direcciones URL de FiscalNote**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de FiscalNote](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_url.png)
    
    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<InstanceName>.fiscalnote.com/login?client=<ClientID>&redirect_uri=https://app.fiscalnote.com/saml-login.html&audience=https://api.fiscalnote.com/&connection=<CONNECTION_NAME>&response_type=id_token%20token`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `urn:auth0:fiscalnote:<CONNECTIONNAME>`

    > [!NOTE]
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de FiscalNote](mailto:support@fiscalnote.com) para obtener estos valores.

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (sin procesar)** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_certificate.png)

5. La aplicación FiscalNote espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones.

    ![Configurar inicio de sesión único](./media/active-directory-saas-fiscalnote-tutorial/tutorial_attribute.png)

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo del token de SAML como muestra la imagen anterior y realice los siguientes pasos:
           
    | Nombre del atributo | Valor de atributo |
    | ---------------| ----------------|
    | Nombre | user.userprincipalname|
    | givenName| user.givenname|
    | familyName| user.surname|
    | email| user.mail|
    
    a. Quite los atributos existentes y agregue otros nuevos. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo del mismo nombre.

    ![Configurar inicio de sesión único](./media/active-directory-saas-fiscalnote-tutorial/tutorial_attribute_04.png)
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-fiscalnote-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. Deje el espacio de nombres en blanco.
    
    e. Haga clic en **Aceptar**.

7. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_400.png)

8. En la sección **Configuración de FiscalNote**, haga clic en **Configurar FiscalNote** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de FiscalNote](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_configure.png) 

9. Para configurar el inicio de sesión único en **FiscalNote**, es preciso enviar los valores descargados de **Certificado (sin procesar)**, [Sign-Out URL (Dirección URL de cierre de sesión), SAML Entity ID (Identificador de entidad de SAML) y SAML Single Sign-On Service URL (Dirección URL del servicio de inicio de sesión único de SAML)](mailto:support@fiscalnote.com) al equipo de soporte técnico de FiscalNote. Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-fiscalnote-test-user"></a>Creación de un usuario de prueba de FiscalNote

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en FiscalNote. FiscalNote admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a FiscalNote se creará un nuevo usuario, en caso de que no exista.
>[!Note]
>Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de FiscalNote](mailto:support@fiscalnote.com).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a FiscalNote.

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a FiscalNote, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **FiscalNote**.

    ![Vínculo de FiscalNote en la lista de aplicaciones](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de FiscalNote en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación FiscalNote.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_203.png

