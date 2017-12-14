---
title: "Tutorial: Integración de Azure Active Directory con Autotask Workplace | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Autotask Workplace."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: e8bd7a770f8544ed4845dd751e43ae5325c68e07
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Tutorial: Integración de Azure Active Directory con Autotask Workplace

En este tutorial, obtendrá información sobre cómo integrar Autotask Workplace con Azure Active Directory (Azure AD).

La integración de Autotask Workplace con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a Autotask Workplace
- Puede permitir que los usuarios inicien sesión automáticamente en Autotask Workplace (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Autotask Workplace, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en Autotask Workplace
- Es necesario que sea administrador o superadministrador en Workplace.
- Tiene que tener una cuenta de administrador en Azure AD.
- Los usuarios que vayan a usar esta característica tienen que tener cuentas en Workplace y Azure AD y las direcciones de correo electrónico para ambas cuentas tienen que coincidir.

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Autotask Workplace desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-autotask-workplace-from-the-gallery"></a>Incorporación de Autotask Workplace desde la galería
Para configurar la integración de Autotask Workplace en Azure AD, deberá agregar Autotask Workplace desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Autotask Workplace desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Autotask Workplace**, seleccione **Autotask Workplace** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Autotask Workplace en la lista de resultados](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Autotask Workplace con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Autotask Workplace para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Autotask Workplace.

Para establecer la relación de vínculo, en Autotask Workplace, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Autotask Workplace, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Autotask Workplace](#create-an-autotask-workplace-test-user)**: para tener un homólogo de Britta Simon en Autotask Workplace que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Autotask Workplace.

**Para configurar el inicio de sesión único de Azure AD con Autotask Workplace, siga estos pasos:**

1. En la página de integración de la aplicación **Autotask Workplace** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_samlbase.png)

3. Si desea configurar la aplicación en el modo iniciado por **IDP** , realice los pasos siguientes en la sección **Dominio y direcciones URL de Autotask Workplace**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Autotask Workplace para IDP](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`.

4. Si desea configurar la aplicación en modo iniciado por **SP**, active **Mostrar configuración avanzada de URL** y realice los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Autotask Workplace para SP](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.awp.autotask.net/loginsso`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para obtener estos valores. 

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_400.png)

7. En una ventana del explorador web diferente, inicie sesión en Workplace en línea con las credenciales de administrador.

    >[!Note]
    >Al configurar la extensión IdP tendrá que especificar un subdominio. Para confirmar el subdominio correcto, inicie sesión en Workplace en línea. Una vez iniciada la sesión, anote el subdominio en la dirección URL.
    >El subdominio es la parte entre "https://" y ".awp.autotask.net/" y debe ser us, eu, ca, o au.

8. Vaya a **Configuration**(Configuración) > **Single Sign-On** (Inicio de sesión único) y realice los pasos siguientes:

    ![Configuración de inicio de sesión único de Autotask](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)
 
    a. Seleccione la opción **XML Metadata File** (Archivo de metadatos XML) y, a continuación, cargue los **metadatos XML** descargados de Azure Portal.

    b. Haga clic en **Enable SSO** (Habilitar SSO).
    
    ![Aprobación de configuración de inicio de sesión único de Autotask](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Seleccione la casilla **I confirm this information is correct and I trust this IdP** (Confirmo que esta información es correcta y que confío en este IdP).

    d. Haga clic en **Approve** (Aprobar).
     
>[!Note]
>Si necesita ayuda con la configuración de Autotask Workplace, consulte [esta página](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para obtener asistencia con la cuenta de Workplace.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.

### <a name="create-an-autotask-workplace-test-user"></a>Creación de un usuario de prueba de Autotask Workplace

En esta sección, creará un usuario llamado Britta Simon en Autotask. Colabore con el [equipo de soporte técnico de Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para agregar los usuarios en esta plataforma.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Autotask Workplace.

![Asignación del rol de usuario][200] 

**Para asignar la usuaria Britta Simon a Autotask Workplace, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Autotask Workplace**.

    ![Vínculo de Autotask Workplace en la lista de aplicaciones](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Autotask Workplace en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de Autotask Workplace.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_203.png

