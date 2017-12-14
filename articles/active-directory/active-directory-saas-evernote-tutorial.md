---
title: "Tutorial: Integración de Azure Active Directory con Evernote | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Evernote."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: c0dda5288d7985669afef5464bf2f9a34f9e8e8c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Tutorial: Integración de Azure Active Directory con Evernote

En este tutorial, obtendrá información sobre cómo integrar Evernote con Azure Active Directory (Azure AD).

La integración de Evernote con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a Evernote.
- Puede permitir que los usuarios inicien sesión automáticamente en Evernote (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Evernote, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Evernote

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Evernote desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-evernote-from-the-gallery"></a>Incorporación de Evernote desde la galería
Para configurar la integración de Evernote en Azure AD, es preciso agregar Evernote desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Evernote desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Evernote**, seleccione **Evernote** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Evernote en la lista de resultados](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Evernote con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Evernote para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Evernote.

Para establecer la relación de vínculo, en Evernote, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Evernote, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Evernote](#create-an-evernote-test-user)**: para tener un homólogo de Britta Simon en Evernote que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Evernote.

**Para configurar el inicio de sesión único de Azure AD con Evernote, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Evernote**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_samlbase.png)

3. En la sección **Dominio y direcciones URL de Evernote**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por IDP:

    ![Información de dominio y direcciones URL de inicio de sesión único de Evernote](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_url.png)

    En el cuadro de texto **Identificador**, escriba la dirección URL: `https://www.evernote.com/saml2`

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Evernote](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL similar a la siguiente: `https://www.evernote.com/Login.action`   

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-evernote-tutorial/tutorial_general_400.png)

7. En la sección **Configuración de Evernote**, haga clic en **Configurar Evernote** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configuración de Evernote](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configure.png) 

8. En otra ventana del explorador web, inicie sesión en el sitio de la empresa de Evernote como administrador.

9. Vaya a **"Consola de administración"**

    ![Admin-Console](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

10. En la **"Consola de administración"**, vaya a **"Seguridad"** y seleccione **"Inicio de sesión único"**

    ![SSO-Setting](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sso.png)

11. Configure los valores siguientes:

    ![Certificate-Setting](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Enable SSO:** (Habilitar SSO) el SSO está habilitado de manera predeterminada (haga clic en **Disable Single Sign-on** [Deshabilitar el inicio de sesión único] para quitar el requisito de SSO)

    b. Pegue el valor de la **dirección URL del servicio de inicio de sesión único** que copió de Azure Portal en el cuadro de texto **SAML HTTP Request URL** (Dirección URL de solicitud HTTP de SAML).

    c. Abra el certificado que se descargó de Azure AD en un bloc de notas y copie el contenido, incluido "BEGIN CERTIFICATE" y "END CERTIFICATE", y péguelo en el cuadro de texto **X.509 Certificate** (Certificado X.509). 

    d. Haga clic en **Guardar cambios**

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-evernote-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-evernote-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-evernote-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-evernote-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-an-evernote-test-user"></a>Creación de un usuario de prueba de Evernote

Para permitir que los usuarios de Azure AD inicien sesión en Evernote, deben aprovisionarse en Evernote.  
En el caso de Evernote, el aprovisionamiento es una tarea manual.

**Para aprovisionar cuentas de usuario, realice estos pasos:**

1. Inicie sesión en el sitio de la empresa de Evernote como administrador.

2. Haga clic en la **"Consola de administración"**.

    ![Admin-Console](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

3. En la **"Consola de administración"**, vaya a **"Agregar usuarios"**.

    ![Add-testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0001.png)

4. **Agregue miembros del equipo** en el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de la cuenta de usuario y haga clic en **Invitar**.

    ![Add-testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0002.png)
    
5. Una vez que se envía la invitación, el titular de la cuenta de Azure Active Directory recibirá un correo electrónico para aceptar la invitación.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Evernote.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Evernote, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Evernote**.

    ![Vínculo de Evernote en la lista de aplicaciones](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Cuando hace clic en el icono de Evernote en el Panel de acceso, debería iniciar sesión automáticamente en esa aplicación. Iniciará sesión como una cuenta de organización, pero deberá iniciar sesión con su cuenta personal. 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_203.png

