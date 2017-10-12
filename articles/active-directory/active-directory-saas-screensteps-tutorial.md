---
title: "Tutorial: Integración de Azure Active Directory con ScreenSteps | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y ScreenSteps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: b6ded8ba1adf03fdccbdb7573c09fae1857c8b16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Tutorial: Integración de Azure Active Directory con ScreenSteps

En este tutorial, obtendrá información sobre cómo integrar ScreenSteps con Azure Active Directory (Azure AD).

Integrar ScreenSteps con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a ScreenSteps.
- Puede permitir que los usuarios inicien sesión automáticamente en ScreenSteps (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con ScreenSteps, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en ScreenSteps

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de ScreenSteps desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-screensteps-from-the-gallery"></a>Incorporación de ScreenSteps desde la galería
Para configurar la integración de ScreenSteps en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar ScreenSteps desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **ScreenSteps**, seleccione **ScreenSteps** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![ScreenSteps en la lista de resultados](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con ScreenSteps con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de ScreenSteps para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ScreenSteps.

Para establecer la relación de vínculo, en ScreenSteps, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con ScreenSteps, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de ScreenSteps](#create-a-screensteps-test-user)**: para tener un homólogo de Britta Simon en ScreenSteps que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación ScreenSteps.

**Para configurar el inicio de sesión único de Azure AD con ScreenSteps, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **ScreenSteps**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_samlbase.png)

3. En la sección de **dominio y direcciones URL de ScreenSteps**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de ScreenSteps](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<tenantname>.ScreenSteps.com`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real, que se explica más adelante en este tutorial. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-screensteps-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de ScreenSteps**, haga clic en **Configurar ScreenSteps** para abrir la ventana **Configurar inicio de sesión**. Copie las **direcciones URL del servicio de inicio de sesión único de SAML y de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de ScreenSteps](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_configure.png) 

7. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de ScreenSteps como administrador.

8. Haga clic en **Configuración de la cuenta**.

    ![Administración de cuentas](./media/active-directory-saas-screensteps-tutorial/ic778523.png "Administración de cuentas")

9. Haga clic en **Inicio de sesión único**.

    ![Autenticación remota](./media/active-directory-saas-screensteps-tutorial/ic778524.png "Autenticación remota")

10. Haga clic en **Crear punto de conexión de inicio de sesión único**.

    ![Autenticación remota](./media/active-directory-saas-screensteps-tutorial/ic778525.png "Autenticación remota")

11. En la sección **Crear punto de conexión de inicio de sesión único** , siga estos pasos:

    ![Creación de un punto de conexión de autenticación](./media/active-directory-saas-screensteps-tutorial/ic778526.png "Creación de un punto de conexión de autenticación")
    
    a. En el cuadro de texto **Título** , escriba un título.
    
    b. En la lista **Modo**, seleccione **SAML**.
    
    c. Haga clic en **Crear**.

12. **Edite** el punto de conexión nuevo.

    ![Editar punto de conexión](./media/active-directory-saas-screensteps-tutorial/ic778528.png "Editar punto de conexión")

13. En la sección **Editar punto de conexión de inicio de sesión único** siga los pasos siguientes:

    ![Punto de conexión de autenticación remota](./media/active-directory-saas-screensteps-tutorial/ic778527.png "Punto de conexión de autenticación remota")

    a. Haga clic en **Cargar nuevo archivo de certificado SAML** y cargue el certificado que descargó de Azure Portal.
    
    b. Pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML**, que copió de Azure Portal, en el cuadro de texto de **dirección URL de inicio de sesión remoto**.
    
    c. Pegue el valor de **dirección URL de cierre de sesión**, que copió de Azure Portal, en el cuadro de texto de **dirección URL de cierre de sesión**.
    
    d. Seleccione un **Grupo** al que asignar usuarios cuando sean aprovisionados.
    
    e. Haga clic en **Update**(Actualizar).

    f. Copie la **dirección URL de consumidor de SAML** en el Bloc de notas y péguela en el cuadro de texto de **dirección URL de inicio de sesión** en la sección **Dominio y direcciones URL de ScreenSteps**.
    
    g. Vuelva a **Editar punto de conexión de inicio de sesión único**.
    
    h. Haga clic en el botón **Predeterminar para cuenta** para usar este punto de conexión para todos los usuarios que inician sesión en ScreenSteps. También puede hacer clic en el botón **Agregar al sitio** para utilizar este punto de conexión para sitios específicos de **ScreenSteps**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-screensteps-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-screensteps-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-screensteps-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-screensteps-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-screensteps-test-user"></a>Creación de un usuario de prueba de ScreenSteps

En esta sección, creará un usuario llamado Britta Simon en ScreenSteps. Trabaje con el [equipo de soporte técnico de ScreenSteps](https://www.screensteps.com/contact) para agregar los usuarios a la plataforma de ScreenSteps. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a ScreenSteps.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a ScreenSteps, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **ScreenSteps**.

    ![Vínculo a ScreenSteps en la lista de aplicaciones](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de ScreenSteps en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación ScreenSteps.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_203.png

