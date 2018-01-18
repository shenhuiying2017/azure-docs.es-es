---
title: "Tutorial: Integración de Azure Active Directory con Rollbar | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Rollbar."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.openlocfilehash: bb8a81327163513ab721d2ad72da19173b59bc1f
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Tutorial: integración de Azure Active Directory con Rollbar

En este tutorial, aprenderá a integrar Rollbar con Azure Active Directory (Azure AD).

La integración de Rollbar con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Rollbar.
- Puede permitir que los usuarios inicien sesión automáticamente en Rollbar (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Rollbar, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Rollbar

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Rollbar desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-rollbar-from-the-gallery"></a>Incorporación de Rollbar desde la galería
Para configurar la integración de Rollbar en Azure AD, deberá agregar Rollbar desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Rollbar desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Rollbar**, seleccione **Rollbar** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Rollbar en la lista de resultados](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Rollbar con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Rollbar para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Rollbar.

Para establecer la relación de vínculo, en Rollbar, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Rollbar, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en Rollbar](#create-a-rollbar-test-user)**: para tener un homólogo de Britta Simon en Rollbar que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Rollbar.

**Para configurar el inicio de sesión único de Azure AD con Rollbar, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Rollbar**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_samlbase.png)

3. En la sección **Dominio y direcciones URL de Rollbar**, realice los siguientes pasos si desea configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Rollbar](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url.png)

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://saml.rollbar.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://rollbar.com/<accountname>/saml/sso/azure/`.

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Rollbar](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://rollbar.com/<accountname>/saml/login/azure/`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Rollbar](mailto:support@rollbar.com) para obtener estos valores. 

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-rollbar-tutorial/tutorial_general_400.png)
    
7. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Rollbar.

8. Haga clic en **Configuración de perfil** en la esquina superior derecha y, luego, en **Configuración de nombre de cuenta**.
    
    ![Configuración](./media/active-directory-saas-rollbar-tutorial/general.png)

9. Haga clic en **Proveedor de identidades** en SEGURIDAD.

    ![Configuración](./media/active-directory-saas-rollbar-tutorial/configure1.png)

10. En la sección **Proveedores de identidades SAML**, realice los pasos siguientes:
    
    ![Configuración](./media/active-directory-saas-rollbar-tutorial/configure2.png)

    a. Seleccione **AZURE** en el menú desplegable **Proveedor de identidades de SAML**.

    b. Abra el archivo de metadatos en el Bloc de notas, copie el contenido del mismo en el Portapapeles y, luego, péguelo en el cuadro de texto **Metadatos de SAML**.

    c. Haga clic en **Save**(Guardar).

11. Después de hacer clic en el botón Guardar, la pantalla se verá de la siguiente manera. Siga los pasos que aparecen en esta sección:
    
    ![Configuración](./media/active-directory-saas-rollbar-tutorial/configure3.png)

    a. Active la casilla de verificación **Require login via SAML Identity Provider** (Requerir inicio de sesión vía el proveedor de identidades de SAML).

    b. Haga clic en **Save**(Guardar).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-rollbar-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-rollbar-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-rollbar-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-rollbar-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-rollbar-test-user"></a>Creación de un usuario de prueba de Rollbar

Para permitir que los usuarios de Azure AD inicien sesión en Rollbar, deben aprovisionarse en dicha solución. En el caso de Rollbar, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su sitio de la compañía de Rollbar como administrador.

2. Haga clic en **Configuración de perfil** en la esquina superior derecha y, luego, en **Configuración de nombre de cuenta**.

    ![Usuario](./media/active-directory-saas-rollbar-tutorial/general.png)

3. Haga clic en **Usuarios**.
    
    ![Agregar empleado](./media/active-directory-saas-rollbar-tutorial/user1.png)

4. Haga clic en **Invite Team Members** (Invitar a miembros del equipo).

    ![Invitar a contactos](./media/active-directory-saas-rollbar-tutorial/user2.png)

5. En el cuadro de texto, escriba el nombre de usuario como **brittasimon@contoso.com** y haga clic en **Add/Invite** (Agregar o invitar).

    ![Invitar a contactos](./media/active-directory-saas-rollbar-tutorial/user3.png)

6. El usuario recibe una invitación y, después de aceptarla, se crea en el sistema.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Rollbar.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Rollbar, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Rollbar**.

    ![Vínculo a Rollbar en la lista de aplicaciones](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Rollbar en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de Rollbar.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_203.png

