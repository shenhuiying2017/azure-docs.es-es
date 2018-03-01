---
title: "Tutorial: Integración de Azure Active Directory con E Sales Manager Remix | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y E Sales Manager Remix."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: 56c2860b6aeac9fdc66f2b1fdd1ed9126bf77d3f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="tutorial-azure-active-directory-integration-with-e-sales-manager-remix"></a>Tutorial: Integración de Azure Active Directory con E Sales Manager Remix

En este tutorial, aprenderá a integrar E Sales Manager Remix con Azure Active Directory (Azure AD).

La integración de E Sales Manager Remix con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a E Sales Manager Remix.
- Puede habilitar que los usuarios inicien sesión automáticamente en E Sales Manager Remix (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con E Sales Manager Remix, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en E Sales Manager Remix

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de E Sales Manager Remix desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-e-sales-manager-remix-from-the-gallery"></a>Adición de E Sales Manager Remix desde la galería
Para configurar la integración de E Sales Manager Remix en Azure AD, será preciso que agregue E Sales Manager Remix desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar E Sales Manager Remix desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **E Sales Manager Remix** , seleccione **E Sales Manager Remix** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![E Sales Manager Remix en la lista de resultados](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con E Sales Manager Remix utilizando un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de E Sales Manager Remix para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de E Sales Manager Remix.

Para configurar y probar el inicio de sesión único de Azure AD con E Sales Manager Remix, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en E Sales Manager Remix](#create-an-e-sales-manager-remix-test-user)**: para tener un homólogo de Britta Simon en E Sales Manager Remix que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación E Sales Manager Remix.

**Para configurar el inicio de sesión único de Azure AD con E Sales Manager Remix, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **E Sales Manager Remix**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. En la sección **Dominio y direcciones URL de E Sales Manager Remix**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<Server-Based-URL>/<sub-domain>/esales-pc`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<Server-Based-URL>/<sub-domain>/`

    c. Cope el valor **Identificador** en el Bloc de notas. Lo usará más adelante en este tutorial.
    
    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de E Sales Manager Remix](mailto:esupport@softbrain.co.jp) para obtener estos valores.

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. Seleccione **Ver y editar todos los demás atributos de usuario** y haga clic en el atributo **emailaddress**.
    
    ![Configuración de E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure1.png)

6. Copie el valor **Espacio de nombres** y **Nombre** de la notificación del cuadro de texto. Genere el valor con el siguiente patrón - `<Namespace>/<Name>`. Lo usará más adelante en este tutorial.

    ![Configuración de E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure2.png)

7. En la sección **Configuración de E Sales Manager Remix**, haga clic en **ConfigurarE Sales Manager Remix** para abrir la ventana **Configurar inicio de sesión**. Copie las **direcciones URL del servicio de inicio de sesión único de SAML y de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

8. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_400.png)

9. Inicie sesión en la aplicación E Sales Manager Remix como administrador.

10. Seleccione **To Administrator Menu** (Menú para administrador) en el menú de la esquina superior derecha.

    ![Configuración de E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

11. Seleccione **System settings** (Configuración del sistema)>**Cooperation with external system** (Cooperación con sistema externo).

    ![Configuración de E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure5.png)
    
12. Seleccione **SAML**.

    ![Configuración de E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure6.png)

13. En la sección **SAML authentication setting** (Configuración de la autenticación SAML), realice los pasos siguientes:

    ![Configuración de E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure3.png)
    
    a. Seleccione la **versión de su equipo**.
    
    b. Seleccione **email** (Correo electrónico) en la lista desplegable de la sección Collaboration item (Elemento de colaboración).

    c. En el cuadro de texto Collaboration item (Elemento de colaboración) pegue el **valor de notificación** que ha copiado en Azure Portal; esto es, `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. En el cuadro de texto **Issuer (entity ID)** (Emisor [Id. de entidad]), pegue el valor **Identificador** que ha copiado de Azure Portal, en la sección **Dominio y direcciones URL de E Sales Manager Remix**.

    e. Para cargar el **certificado** descargado de Azure Portal, seleccione **File selection** (Selección de archivo).

    f. En el cuadro de texto **ID provider login URL** (Dirección URL de inicio de sesión de proveedor de identidades), pegue la **dirección URL del servicio de inicio de sesión único de SAML** que copió de Azure Portal.

    g. En el cuadro de texto **Identity Provider Logout URL** (Dirección URL de cierre de sesión del proveedor de identidades), pegue el valor de **dirección URL de cierre de sesión** que ha copiado de Azure Portal.

    h. Haga clic en **Setting complete** (Configuración completada).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Creación de un usuario de prueba de E Sales Manager Remix

1. Inicie sesión en la aplicación E Sales Manager Remix como administrador.

2. Seleccione **To Administrator Menu** (Menú para administrador) en el menú de la esquina superior derecha.

    ![Configuración de E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

3. Select **Your company settings** (Configuración de su empresa)>**Maintenance of departments and employees** (Mantenimiento de departamentos y empleados) y seleccione **Employees registered** (Empleados registrados).

    ![El usuario](./media/active-directory-saas-esalesmanagerremix-tutorial/user1.png)

4. En la sección **New employee registration** (Nuevo registro de empleado), realice los siguientes pasos:
    
    ![El usuario](./media/active-directory-saas-esalesmanagerremix-tutorial/user2.png)

    a. En el cuadro de texto **Employee Name** (Nombre de empleado), escriba el nombre de un usuario, por ejemplo, Brita.

    b. Rellene los campos obligatorios respectivos con la información de usuario.
    
    c. Si habilita SAML, el administrador no podrá iniciar sesión desde la pantalla de inicio de sesión, así que conceda privilegios de inicio de sesión de administrador para el usuario seleccionando **Admin Login** (Inicio de sesión de administrador).

    d. Haga clic en **Registration** (Registro).

5. En el futuro, si desea iniciar sesión como administrador, inicie sesión con el usuario al que se concedió permiso de administrador y haga clic en **To Administrator Menu** (Menú para administrador) en el menú de la esquina superior derecha.

    ![El usuario](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a E Sales Manager Remix.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a E Sales Manager Remix, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **E Sales Manager Remix**.

    ![Vínculo a E Sales Manager Remix en la lista de aplicaciones](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de E Sales Manager Remix en el panel de acceso, debería iniciar sesión automáticamente en la aplicación E Sales Manager Remix.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_203.png

