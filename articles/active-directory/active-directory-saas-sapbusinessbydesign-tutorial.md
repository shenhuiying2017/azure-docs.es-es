---
title: "Tutorial: Integración de Azure Active Directory con SAP Business ByDesign | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAP Business ByDesign."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: ab76a0ac1ef954efd3c66e6f565514b889ed9444
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Tutorial: Integración de Azure Active Directory con SAP Business ByDesign

En este tutorial, aprenderá a integrar SAP Business ByDesign con Azure Active Directory (Azure AD).

La integración de SAP Business ByDesign con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a SAP Business ByDesign.
- Puede permitir que los usuarios inicien sesión automáticamente en SAP Business ByDesign (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SAP Business ByDesign, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en SAP Business ByDesign

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de SAP Business ByDesign desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Adición de SAP Business ByDesign desde la galería
Para configurar la integración de SAP Business ByDesign en Azure AD, es preciso agregar SAP Business ByDesign desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SAP Business ByDesign desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **SAP Business ByDesign**, seleccione **SAP Business ByDesign** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![SAP Business ByDesign en la lista de resultados](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, se configura y se prueba el inicio de sesión único de Azure AD con SAP Business ByDesign con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo de SAP Business ByDesign para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SAP Business ByDesign.

Para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario** en SAP Business ByDesign.

Para configurar y probar el inicio de sesión único de Azure AD con SAP Business ByDesign, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SAP Business ByDesign](#create-an-sap-business-bydesign-test-user)**: para tener un homólogo de Britta Simon en SAP Business ByDesign que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación SAP Business ByDesign.

**Para configurar el inicio de sesión único de Azure AD con SAP Business ByDesign, siga estos pasos:**

1. En la página de integración de la aplicación **SAP Business ByDesign** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

3. En la sección **Dominio y direcciones URL de SAP Business ByDesign**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<servername>.sapbydesign.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<servername>.sapbydesign.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para obtener estos valores.

4. En la sección **Atributos de usuario**, realice estos pasos:

    ![Sección Atributos de SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. En la lista **Identificador de usuario**, seleccione la función **ExtractMailPrefix**.
    
    b. En la lista **Correo** , seleccione el atributo de usuario que desea usar en su implementación. Por ejemplo, si quiere usar EmployeeID como identificador de usuario único y ha almacenado el valor del atributo en ExtensionAttribute2, seleccione user.extensionattribute2.     

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_400.png)

7. En la sección **Configuración de SAP Business ByDesign**, haga clic en **Configurar SAP Business ByDesign** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configuración de SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

8. Para que SSO se configure para su aplicación, siga estos pasos:
   
    a. Inicie sesión en el portal de SAP Business ByDesign con derechos de administrador.
   
    b. Navegue hasta **Application and User Management Common Task** (Tarea común de administración de usuarios y aplicaciones) y haga clic en la pestaña **Proveedor de identidades**.
   
    c. Haga clic en **New Identity Provider** (Nuevo proveedor de identidades) y seleccione el archivo XML de metadatos que ha descargado de Azure Portal. Al importar los metadatos, el sistema carga automáticamente el certificado de firma y el certificado de cifrado necesarios.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Para incluir la **URL del Servicio de consumidor de aserciones** en la petición SAML, seleccione **Include Assertion Consumer Service URL** (Incluir URL del Servicio de consumidor de aserciones).
   
    e. Haga clic en **Activate Single Sign-On**(Activar inicio de sesión único).
   
    f. Guarde los cambios.
   
    g. Haga clic en la pestaña **My System** (Mi sistema).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Pegue el valor de **Dirección URL del servicio de inicio de sesión único de SAML** que copió de Azure Portal en el cuadro de texto **Azure AD Sign On URL** (Dirección URL de inicio de sesión único de Azure AD).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. Especifique si el empleado puede elegir manualmente entre iniciar sesión con el id. de usuario y una contraseña o con SSO, para lo que necesita seleccionar **Manual Identity Provider Selection** (Selección manual de proveedor de identidades).
   
    j. En la sección **SSO URL** (URL de SSO), especifique la dirección URL que debe utilizar el empleado para iniciar sesión en el sistema. 
    En la lista desplegable URL Sent to Employee (URL que se envía a empleado), puede elegir una de las siguientes opciones:
   
    **Non-SSO URL**
   
    El sistema solo envía la dirección URL del sistema normal al empleado. El empleado no puede iniciar sesión mediante SSO y debe usar una contraseña o un certificado en su lugar.
   
    **SSO URL** 
   
    El sistema solo envía la dirección URL de SSO al empleado. El empleado puede iniciar sesión mediante SSO. La solicitud de autenticación se redirige a través de IdP.
   
    **Automatic Selection**
   
    SI SSO no está activo, el sistema envía la dirección URL del sistema normal al empleado. Si SSO está activo, el sistema comprueba si el empleado tiene contraseña. Si existe una contraseña, se envían al empleado tanto la URL de SSO como la URL no de SSO. Sin embargo, si el empleado no tiene contraseña, solo se envía la primera al empleado.
   
    k. Guarde los cambios.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-an-sap-business-bydesign-test-user"></a>Creación de un usuario de prueba de SAP Business ByDesign

En esta sección, creará un usuario llamado Britta Simon en SAP Business ByDesign. Trabaje con el [equipo de soporte técnico de SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para agregar usuarios en la plataforma SAP Business ByDesign. 

> [!NOTE]
> Asegúrese de que el valor de NameID coincide con el campo de nombre de usuario de la plataforma SAP Business ByDesign.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a SAP Business ByDesign.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a SAP Business ByDesign, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SAP Business ByDesign**.

    ![Vínculo a SAP Business ByDesign en la lista de aplicaciones](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de SAP Business ByDesign en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación SAP Business ByDesign.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png

