---
title: "Tutorial: Integración de Azure Active Directory con Zoho | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Zoho."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: 51a10a4d687a0d218b5439ebf5aeb1d0895c6161
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Tutorial: Integración de Azure Active Directory con Zoho

En este tutorial, obtendrá información sobre cómo integrar Zoho con Azure Active Directory (Azure AD).

La integración de Zoho con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Zoho.
- Puede permitir que los usuarios inicien sesión automáticamente en Zoho (Inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Zoho, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Zoho

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Zoho desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-zoho-from-the-gallery"></a>Incorporación de Zoho desde la galería
Para configurar la integración de Zoho en Azure AD, deberá agregar Zoho desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Zoho desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Zoho**, seleccione **Zoho** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Zoho en la lista de resultados](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Zoho con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Zoho para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Zoho.

Para establecer la relación de vínculo, en Zoho, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Zoho, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Zoho](#create-a-zoho-test-user)** : para tener un homólogo de Britta Simon en Zoho que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en su aplicación Zoho.

**Para configurar el inicio de sesión único de Azure AD con Zoho, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Zoho**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_samlbase.png)

3. En la sección **Dominio y direcciones URL de Zoho**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Zoho](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<company name>.zohomail.com`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de atención al cliente de Zoho](https://www.zoho.com/mail/contact.html) para obtener este valor. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Zoho**, haga clic en **Configurar Zoho** para abrir la ventana **Configurar inicio de sesión**. Copie las **direcciones URL del servicio de inicio de sesión único de SAML, de cambio de contraseña y de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de Zoho](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_configure.png) 

7. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Zoho Mail como administrador.

8. Vaya al **Panel de control**.
   
    ![Panel de control](./media/active-directory-saas-zoho-mail-tutorial/ic789607.png "Panel de control")

9. Haga clic en la pestaña **Autenticación SAML** .
   
    ![Autenticación SAML](./media/active-directory-saas-zoho-mail-tutorial/ic789608.png "Autenticación SAML")

10. En la sección **Detalles de la autenticación SAML** , realice los pasos siguientes:
   
    ![Detalles de la autenticación SAML](./media/active-directory-saas-zoho-mail-tutorial/ic789609.png "Detalles de la autenticación SAML")
   
    a. En el cuadro de texto **Login URL** (Dirección URL de inicio de sesión), pegue la **dirección URL del servicio de inicio de sesión único de SAML** que copió desde Azure Portal.
   
    b. En el cuadro de texto **Logout URL** (Dirección URL de cierre de sesión), pegue la **dirección URL de cierre de sesión** que copió de Azure Portal.
   
    c. En el cuadro de texto **Change Password Link** (Cambiar vínculo de contraseña), pegue el valor de **Cambiar dirección URL de contraseña** que copió de Azure Portal.
       
    d. Abra en el Bloc de notas el certificado codificado en Base 64 que descargó de Azure Portal, copie el contenido en el Portapapeles y, luego, péguelo en el cuadro de texto **PublicKey** (Clave pública).
   
    e. En **Algoritmo**, seleccione **RSA**.
   
    f. Haga clic en **Aceptar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-zoho-test-user"></a>Creación de un usuario de prueba de Zoho

Para permitir que los usuarios de Azure AD inicien sesión en Zoho Mail, deben aprovisionarse a Zoho Mail. En el caso de Zoho Mail, el aprovisionamiento es una tarea manual.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Zoho Mail ofrecida por Zoho Mail para aprovisionar cuentas de usuario de AAD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:

1. Inicie sesión en su sitio de la compañía de **Zoho Mail** como administrador.

2. Vaya a **Control Panel \> Mail & Docs** (Panel de control > Correo y documentos) .

3. Vaya a **User Details \> Add User** (Detalles del usuario > Agregar usuario).
   
    ![Agregar usuario](./media/active-directory-saas-zoho-mail-tutorial/ic789611.png "Agregar usuario")

4. En el cuadro de diálogo **Agregar usuarios** , realice los pasos siguientes:
   
    ![Agregar usuario](./media/active-directory-saas-zoho-mail-tutorial/ic789612.png "Agregar usuario")
   
    a. En el cuadro de texto **Nombre**, escriba el nombre del usuario, en este caso, **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba los apellidos del usuario, en este caso, **Simon**.

    c. En el cuadro de texto **Identificador de correo electrónico**, escriba la dirección de correo electrónico de un usuario, por ejemplo, **brittasimon@contoso.com**.

    d. En el cuadro de texto **Contraseña**, escriba la contraseña del usuario.
   
    e. Haga clic en **Aceptar**.  
      
    > [!NOTE]
    > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Zoho.

![Asignación del rol de usuario][200] 

**Para asignar el usuario Britta Simon a Zoho, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Zoho**.

    ![Vínculo a Zoho en la lista de aplicaciones](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Zoho en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Zoho.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_203.png

