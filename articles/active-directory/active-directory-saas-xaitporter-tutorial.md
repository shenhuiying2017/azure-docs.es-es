---
title: "Tutorial: Integración de Azure Active Directory con XaitPorter | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y XaitPorter."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: jeedes
ms.openlocfilehash: 2012d990f7cdcb8c12da5f16db518b261b06a5b7
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Tutorial: Integración de Azure Active Directory con XaitPorter

En este tutorial, obtendrá información sobre cómo integrar XaitPorter con Azure Active Directory (Azure AD).

La integración de XaitPorter con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a XaitPorter.
- Puede habilitar que los usuarios inicien sesión automáticamente en XaitPorter (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con XaitPorter, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en XaitPorter

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de XaitPorter desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-xaitporter-from-the-gallery"></a>Adición de XaitPorter desde la galería
Para configurar la integración de XaitPorter en Azure AD, debe agregar XaitPorter desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar XaitPorter desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **XaitPorter**, seleccione **XaitPorter** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![XaitPorter en la lista de resultados](./media/active-directory-saas-xaitporter-tutorial/tutorial_xaitporter_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con XaitPorter con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de XaitPorter para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de XaitPorter.

Para establecer la relación de vínculo, en XaitPorter, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con XaitPorter, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en XaitPorter](#create-a-xaitporter-test-user)**: para tener un homólogo de Britta Simon en XaitPorter que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación XaitPorter.

**Para configurar el inicio de sesión único de Azure AD con XaitPorter, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **XaitPorter**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-xaitporter-tutorial/tutorial_xaitporter_samlbase.png)

3. En la sección **Dominio y direcciones URL de XaitPorter**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de XaitPorter](./media/active-directory-saas-xaitporter-tutorial/tutorial_xaitporter_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.xaitporter.com/saml/login`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.xaitporter.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de XaitPorter](https://www.xait.com/support/) para obtener estos valores. 

4. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-xaitporter-tutorial/tutorial_general_400.png)

5. Para generar la dirección URL de **Metadatos**, lleve a cabo los pasos siguientes:

    a. Haga clic en **Registros de aplicaciones**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-xaitporter-tutorial/tutorial_xaitporter_appregistrations.png)
   
    b. Haga clic en **Puntos de conexión** para abrir el cuadro de diálogo **Puntos de conexión**.  
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-xaitporter-tutorial/tutorial_xaitporter_endpointicon.png)

    c. Haga clic en el botón Copiar para copiar la dirección URL del **DOCUMENTO DE METADATOS DE FEDERACIÓN** y péguela en el Bloc de notas.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-xaitporter-tutorial/tutorial_xaitporter_endpoint.png)
     
    d. Ahora, vaya a la página de propiedades de **XaitPorter** y copie el **Identificador de la aplicación** con el botón **Copiar** y péguelo en el Bloc de notas.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-xaitporter-tutorial/tutorial_xaitporter_appid.png)

    e. Genere la **Dirección URL de metadatos** con el patrón siguiente: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`

6. Proporcione la **dirección IP** o **dirección URL de metadatos** al [equipo de soporte técnico de SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/), de modo que XaitPorter puede asegurarse de que la dirección IP sea accesible desde su instancia de XaitPorter mediante su inclusión en la lista de permitidos. 

7. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía en XaitPorter.

8. Haga clic en **Admin** (Administrador).

    ![Configurar inicio de sesión único](./media/active-directory-saas-xaitporter-tutorial/user1.png)

9. Seleccione **Manage Single Sign-On** (Administrar inicio de sesión único) desde la lista desplegable **System Setup** (Configuración del sistema).

    ![Configurar inicio de sesión único](./media/active-directory-saas-xaitporter-tutorial/user2.png)

10. En la sección **MANAGE SINGLE SIGN-ON** (Administrar inicio de sesión único), lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-xaitporter-tutorial/user3.png)

    a. Seleccione **Enable Single Sign-On Authentication** (Habilitar autenticación de inicio de sesión único).

    b. En el cuadro de texto **Identity Provider Settings** (Configuración del proveedor de identidades), pegue la **Dirección URL de metadatos** que ha copiado desde Azure y haga clic en **Fetch** (Recuperar).

    c. Seleccione **Enable Autocreation of Users** (Habilitar la creación automática de usuarios).

    d. Haga clic en **Aceptar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-xaitporter-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-xaitporter-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-xaitporter-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-xaitporter-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-xaitporter-test-user"></a>Creación de un usuario de prueba de XaitPorter

En esta sección, creará un usuario llamado Britta Simon en XaitPorter. Trabaje con el [equipo de soporte técnico al cliente de XaitPorter](https://www.xait.com/support/) para agregar los usuarios a la plataforma de XaitPorter. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a XaitPorter.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a XaitPorter, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **XaitPorter**.

    ![Vínculo a XaitPorter en la lista de aplicaciones](./media/active-directory-saas-xaitporter-tutorial/tutorial_xaitporter_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de XaitPorter en el panel de acceso, debería iniciar sesión automáticamente en la aplicación XaitPorter.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_203.png

