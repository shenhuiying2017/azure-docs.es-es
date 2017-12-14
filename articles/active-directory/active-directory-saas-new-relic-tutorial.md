---
title: "Tutorial: Integración de Azure Active Directory con New Relic | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y New Relic."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: a97038b942998b611bdde50ccf67c5b2e3fa4cd5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Tutorial: Integración de Azure Active Directory con New Relic

En este tutorial, aprenderá a integrar New Relic con Azure Active Directory (Azure AD).

La integración de New Relic con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a New Relic.
- Puede permitir que los usuarios inicien sesión automáticamente en New Relic (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con New Relic, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en New Relic

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de New Relic desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-new-relic-from-the-gallery"></a>Adición de New Relic desde la galería
Para configurar la integración de New Relic en Azure AD, es precios agregar dicha solución desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar New Relic desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **New Relic**, seleccione **New Relic** en el panel de resultados y haga clic en el botón **Agregar** para agregar la aplicación.

    ![New Relic en la lista de resultados](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con New Relic con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de New Relic para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de New Relic.

Para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario** de New Relic.

Para configurar y probar el inicio de sesión único de Azure AD con New Relic, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de New Relic](#create-a-new-relic-test-user)**: el objetivo es tener un homólogo de Britta Simon en New Relic que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación New Relic.

**Para configurar el inicio de sesión único de Azure AD con New Relic, realice los pasos siguientes:**

1. En la página de integración de la aplicación **New Relic** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_samlbase.png)

3. En la sección **Dominio y direcciones URL de New Relic**, lleve a cabo los pasos siguientes:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de New Relic](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.newrelic.com`.

    b. En el cuadro de texto **Identificador**, escriba el valor `rpm.newrelic.com`.

    > [!NOTE] 
    > El valor de la dirección URL de inicio de sesión no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de New Relic](https://support.newrelic.com/) para obtener este valor. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-new-relic-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de New Relic**, haga clic en **Configurar New Relic** para abrir la ventana **Configurar inicio de sesión**. Copie los valores **Sign-Out URL y SAML Single Sign-On Service URL** (Dirección URL de cierre de sesión y Dirección URL del servicio de inicio de sesión único de SAML) de la **sección de referencia rápida**.

    ![Configuración de New Relic](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_configure.png) 

7. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de **New Relic** como administrador.

8. En el menú de la parte superior, haga clic en **Configuración de cuenta**.
   
    ![Configuración de la cuenta](./media/active-directory-saas-new-relic-tutorial/ic797036.png "configuración de la cuenta")

9. Haga clic en la pestaña **Seguridad y autenticación** y, luego, haga clic en la pestaña **Inicio de sesión único**.
   
    ![Inicio de sesión único](./media/active-directory-saas-new-relic-tutorial/ic797037.png "Inicio de sesión único")

10. En la página de diálogo SAML, realice los pasos siguientes:
   
    ![SAML](./media/active-directory-saas-new-relic-tutorial/ic797038.png "SAML")
   
   a. Haga clic en **Elegir archivo** para cargar el certificado de Azure Active Directory descargado.

   b. En el cuadro de texto **Dirección URL de inicio de sesión remoto**, pegue el valor de la **dirección URL de servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.
   
   c. En el cuadro de texto **Logout landing URL** (Dirección URL de cierre de sesión), pegue el valor de **dirección URL de cierre de sesión** que copió de Azure Portal.

   d. Haga clic en **Guardar los cambios**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-new-relic-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-new-relic-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-new-relic-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-new-relic-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-new-relic-test-user"></a>Creación de un usuario de prueba de New Relic

Para permitir que los usuarios de Azure Active Directory inicien sesión en New Relic, deben aprovisionarse en New Relic. En el caso de New Relic, el aprovisionamiento es una tarea manual.

**Para aprovisionar cuentas de usuario a New Relic, realice los siguientes pasos:**

1. Inicie sesión como administrador en el sitio de la compañía **New Relic** .

2. En el menú de la parte superior, haga clic en **Configuración de cuenta**.
   
    ![Configuración de la cuenta](./media/active-directory-saas-new-relic-tutorial/ic797040.png "configuración de la cuenta")

3. En el panel **Cuenta** que se encuentra en el lado izquierdo, haga clic en **Resumen** y, luego, haga clic en **Agregar usuario**.
   
    ![Configuración de la cuenta](./media/active-directory-saas-new-relic-tutorial/ic797041.png "configuración de la cuenta")

4. En el cuadro de diálogo **Usuarios activos** , realice los pasos siguientes:
   
    ![Usuarios activos](./media/active-directory-saas-new-relic-tutorial/ic797042.png "Usuarios activos")
   
    a. En el cuadro de texto **Correo electrónico** , escriba la dirección de correo electrónico de un usuario válido de Azure Active Directory que quiera aprovisionar.

    b. Como **Rol**, seleccione **Usuario**.

    c. Haga clic en **Agregar este usuario**.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de New Relic ofrecida por New Relic para aprovisionar cuentas de usuario de AAD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a New Relic.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a New Relic, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **New Relic**.

    ![Vínculo a New Relic en la lista de aplicaciones](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de New Relic en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación New Relic.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_203.png

