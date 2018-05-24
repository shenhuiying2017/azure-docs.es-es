---
title: 'Tutorial: Integración de Azure Active Directory con AppNeta Performance Monitor | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y AppNeta Performance Monitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: 0bfbb7022ad8e70d89d79297b5f5476160bd4e33
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-appneta-performance-monitor"></a>Tutorial: Integración de Azure Active Directory con AppNeta Performance Monitor

En este tutorial, aprenderá a integrar AppNeta Performance Monitor con Azure Active Directory (Azure AD).

La integración de AppNeta Performance Monitor con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a AppNeta Performance Monitor.
- Puede permitir que los usuarios inicien sesión automáticamente en AppNeta Performance Monitor (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con AppNeta Performance Monitor, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada con inicio de sesión único de AppNeta Performance Monitor

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de AppNeta Performance Monitor desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Adición de AppNeta Performance Monitor desde la galería
Para configurar la integración de AppNeta Performance Monitor en Azure AD, deberá agregar AppNeta Performance Monitor desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar AppNeta Performance Monitor desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **AppNeta Performance Monitor**, seleccione **AppNeta Performance Monitor**  en el panel de resultados y, después, haga clic en el botón **Agregar** para agregar la aplicación.

    ![AppNeta Performance Monitor en la lista de resultados](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con AppNeta Performance Monitor con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo de AppNeta Performance Monitor para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de AppNeta Performance Monitor.

Para configurar y probar el inicio de sesión único de Azure AD con AppNeta Performance Monitor, debe completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de AppNeta Performance Monitor](#create-an-appneta-performance-monitor-test-user)**: para tener un homólogo de Britta Simon en AppNeta Performance que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación AppNeta Performance Monitor.

**Para configurar el inicio de sesión único de Azure AD con AppNeta Performance Monitor, realice los pasos siguientes:**

1. En la página de integración de la aplicación **AppNeta Performance Monitor** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_samlbase.png)

3. En la sección **AppNeta Performance Monitor Domain and URLs** (Dominio y direcciones URL de AppNeta Performance Monitor), lleve a cabo los pasos siguientes:

    ![Información de inicio de sesión único del dominio y direcciones URL de AppNeta Performance Monitor](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.pm.appneta.com`.

    b. En el cuadro de texto **Identificador**, escriba el valor `PingConnect`.

    > [!NOTE] 
    > El valor de la dirección URL de inicio de sesión no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de cliente de AppNeta Performance Monitor](mailto:support@appneta.com) para obtener este valor. 

5. La aplicación AppNeta Performance Monitor espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones.

    ![Configurar inicio de sesión único](./media/active-directory-saas-appneta-tutorial/attribute.png)

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo del token de SAML como muestra la imagen anterior y realice los siguientes pasos:
           
    | Nombre del atributo | Valor de atributo |
    | ---------------| ----------------|
    | firstName| user.givenname|
    | lastName| user.surname|
    | email| user.userprincipalname|
    | Nombre| user.userprincipalname|
    | groups   | user.assignedroles |
    | phone| user.telephonenumber |
    | título| user.jobtitle|

    > [!NOTE]
    > 'groups' hace referencia al grupo de seguridad en AppNeta que se asigna a un 'rol' de Azure AD. Consulte [este](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) documento que explica cómo crear roles personalizados en Azure AD.
        
    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-appneta-tutorial/tutorial_attribute_04.png)
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-appneta-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. Deje el espacio de nombres en blanco.
    
    e. Haga clic en **Aceptar**.  

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-appneta-tutorial/tutorial_general_400.png)

6. Para configurar el inicio de sesión único en **AppNeta Performance Monitor**, tiene que enviar el **XML de metadatos** descargado al [equipo de soporte técnico de AppNeta Performance Monitor](mailto:support@appneta.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-appneta-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-appneta-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-appneta-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-appneta-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-an-appneta-performance-monitor-test-user"></a>Creación de un usuario de prueba de AppNeta Performance Monitor

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en AppNeta Performance Monitor. AppNeta Performance Monitor admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a AppNeta Performance Monitor se crea un nuevo usuario, en caso de que no exista.
>[!Note]
>Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de AppNeta Performance Monitor](mailto:support@appneta.com).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para usar el inicio de sesión único de Azure al concederle acceso a AppNeta Performance Monitor.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a AppNeta Performance Monitor, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **AppNeta Performance Monitor**.

    ![El vínculo de AppNeta Performance Monitor en la lista de aplicaciones](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de AppNeta Performance Monitor en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación AppNeta Performance Monitor.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_203.png

