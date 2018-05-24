---
title: 'Tutorial: Integración de Azure Active Directory con Yodeck | Microsoft Docs'
description: Obtenga información para configurar el inicio de sesión único entre Azure Active Directory y Yodeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: ba48c5d14775004e22dd67a2932c8969e6dfdd6d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354187"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Tutorial: Integración de Azure Active Directory con Yodeck

En este tutorial se aprende a integrar Yodeck con Azure Active Directory (Azure AD).

La integración de Yodeck con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Yodeck.
- Puede permitir que los usuarios inicien sesión automáticamente en Yodeck (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Yodeck, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Yodeck

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Yodeck desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-yodeck-from-the-gallery"></a>Incorporación de Yodeck desde la galería
Para configurar la integración de Yodeck en Azure AD, debe agregar Yodeck desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Yodeck desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Yodeck**, seleccione **Yodeck** en el panel de resultados y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Yodeck en la lista de resultados](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección puede configurar y probar el inicio de sesión único de Azure AD con Yodeck con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Yodeck para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Yodeck.

Para configurar y probar el inicio de sesión único de Azure AD con Yodeck, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Yodeck](#create-a-yodeck-test-user)**: para tener un homólogo de Britta Simon en Yodeck que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección se habilita el inicio de sesión único de Azure AD en Azure Portal y se configura en la aplicación Yodeck.

**Para configurar el inicio de sesión único de Azure AD con Yodeck, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Yodeck**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_samlbase.png)

3. En la sección **Dominio y direcciones URL de Yodeck**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Yodeck](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_url.png)

    En el cuadro de texto **Identificador (Id. de entidad)**, escriba la dirección URL: `https://app.yodeck.com/api/v1/account/metadata/`

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Yodeck](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://app.yodeck.com/login`

5. En la sección **Certificado de firma de SAML**, haga clic en el botón Copiar para copiar la **dirección URL de metadatos de federación de la aplicación** y péguela en el Bloc de notas.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_certificate.png)

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-yodeck-tutorial/tutorial_general_400.png)
    
7. En otra ventana del explorador web, inicie sesión en el sitio de la empresa de Yodeck como administrador.

8. Haga clic en la opción **Configuración de usuario** de la esquina superior derecha de la página y seleccione **Configuración de cuenta**.

    ![Configuración de Yodeck](./media/active-directory-saas-yodeck-tutorial/configure1.png)

9. Seleccione **SAML** y realice los siguientes pasos:

    ![Configuración de Yodeck](./media/active-directory-saas-yodeck-tutorial/configure2.png)

    a. Seleccione **Importar desde URL**.

    b. En el cuadro de texto **URL**, pegue el valor **Dirección URL de metadatos de federación de aplicación** que ha copiado de Azure Portal y haga clic en **Importar**.
    
    c. Después de importar **Dirección URL de metadatos de federación de aplicación**, los campos restantes se rellenan automáticamente.

    d. Haga clic en **Save**(Guardar).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-yodeck-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-yodeck-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-yodeck-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-yodeck-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-yodeck-test-user"></a>Crear un usuario de prueba de Yodeck

Para permitir que los usuarios de Azure AD inicien sesión en Yodeck, tienen que aprovisionarse en Yodeck.
En el caso de Yodeck, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en el sitio de la empresa de Yodeck como administrador.

2. Haga clic en la opción **Configuración de usuario** de la esquina superior derecha de la página y seleccione **Usuarios**.

    ![Agregar empleado](./media/active-directory-saas-yodeck-tutorial/user1.png)

3. Haga clic en **+Usuario** para abrir la pestaña **Detalles del usuario**.

    ![Agregar empleado](./media/active-directory-saas-yodeck-tutorial/user2.png)

4. En la página de diálogo **User Details** (Detalles del usuario), siga estos pasos:

    ![Agregar empleado](./media/active-directory-saas-yodeck-tutorial/user3.png)

    a. En el cuadro de texto **Nombre**, escriba el nombre del usuario, en este caso, **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba los apellidos del usuario, en este caso, **Simon**.

    c. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de un usuario; por ejemplo, **brittasimon@contoso.com**.

    d. Seleccione la opción **Permisos de cuenta** adecuada según los requisitos de la organización.
    
    e. Haga clic en **Save**(Guardar).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección se habilita a Britta Simon para que use el inicio de sesión único de Azure al concederle acceso a Yodeck.

![Asignación de rol de usuario][200]

**Para asignar a Britta Simon a Yodeck, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Yodeck**.

    ![Vínculo a Yodeck en la lista de aplicaciones](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Yodeck en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación Yodeck.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_203.png

