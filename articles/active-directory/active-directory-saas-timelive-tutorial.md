---
title: "Tutorial: integración de Azure Active Directory con TimeLive | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y TimeLive."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 34123629-4ad5-465c-a4c1-8299f857e720
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: jeedes
ms.openlocfilehash: 945067f0f413790883d55ab6cc9b2721144e966a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-timelive"></a>Tutorial: integración de Azure Active Directory con TimeLive

En este tutorial, obtendrá información acerca de cómo integrar TimeLive con Azure Active Directory (Azure AD).

La integración de TimeLive con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a TimeLive.
- Puede habilitar que los usuarios inicien sesión automáticamente en TimeLive (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con TimeLive, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en TimeLive

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de TimeLive desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-timelive-from-the-gallery"></a>Adición de TimeLive desde la galería
Para configurar la integración de TimeLive en Azure AD, será preciso que agregue TimeLive desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar TimeLive desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **TimeLive**, seleccione **TimeLive** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![TimeLive en la lista de resultados](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con TimeLive según un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de TimeLive para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de TimeLive.

Para establecer la relación de vínculo, en TimeLive, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con TimeLive, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de TimeLive](#create-a-timelive-test-user)**: para tener un homólogo de Britta Simon en TimeLive que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación TimeLive.

**Para configurar el inicio de sesión único de Azure AD con TimeLive, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **TimeLive**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_samlbase.png)

3. En la sección **Dominio y direcciones URL de TimeLive**, lleve a cabo los pasos siguientes:

    ![Información sobre el inicio de sesión único del dominio y las direcciones URL de TimeLive](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://domainname.livetecs.com/`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://domainname.livetecs.com/`

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de soporte de cliente de TimeLive](mailto:support@livetecs.com) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-timelive-tutorial/tutorial_general_400.png)
    
6. En la sección **TimeLive Configuration** (Configuración de TimeLive), haga clic en **Configure TimeLive** (Configurar TimeLive) para abrir la ventana **Configurar inicio de sesión**. Copie las **direcciones URL del servicio de inicio de sesión único de SAML y de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de TimeLive](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_configure.png)

7. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de TimeLive como administrador.

8. Seleccione **Preferencias** en **Opciones de administración**.

    ![Configuración de TimeLive](./media/active-directory-saas-timelive-tutorial/configure1.png)

9. En la sección **Application Preference** (Aplicación de preferencia), realice los pasos siguientes:
    
    ![Configuración de TimeLive](./media/active-directory-saas-timelive-tutorial/configure2.png)

    a. Seleccione la pestaña **Seguridad**.

    b. Marque la casilla **Enable Single Sign On (SSO)** (Habilitar inicio de sesión único [SSO]).

    c. Seleccione **SAML** en el menú desplegable con el encabezado **Sign in using Single Sign-On (SSO) with** (Iniciar sesión con inicio de sesión único [SSO] con).

    d. Pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que copió de Azure Portal en el cuadro de texto **SAML SSO URL** (Dirección URL de inicio de sesión de SAML).

    e. En **Dirección URL de cierre de sesión remoto**, pegue el valor de **URL de cierre de sesión** que copió de Azure Portal.

    f. Abra el **certificado codificado en Base 64** que descargó de Azure Portal en el Bloc de notas, copie el contenido y, luego, péguelo en el cuadro de texto **X.509 Certificate** (Certificado X.509).

    g. Haga clic en **Update**(Actualizar).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-timelive-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-timelive-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-timelive-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-timelive-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-timelive-test-user"></a>Crear un usuario de prueba de TimeLive

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en TimeLive. TimeLive admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a TimeLive, se crea un nuevo usuario, en caso de que no exista.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a TimeLive.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a TimeLive, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **TimeLive**.

    ![Vínculo a TimeLive en la lista de aplicaciones](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de TimeLive en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación TimeLive.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_203.png

