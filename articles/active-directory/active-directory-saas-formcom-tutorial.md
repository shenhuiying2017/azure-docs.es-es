---
title: "Tutorial: Integración de Azure Active Directory con Form.com | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Form.com."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: f1bc0112-315c-4e6f-8c69-7c6873007bcf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: jeedes
ms.openlocfilehash: e4de55d99fce1cb1feff18a784eb029d3cb6a404
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-formcom"></a>Tutorial: Integración de Azure Active Directory con Form.com

En este tutorial, obtendrá información sobre cómo integrar Form.com con Azure Active Directory (Azure AD).

La integración de Form.com con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Form.com.
- Puede permitir que los usuarios inicien sesión automáticamente en Form.com (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Form.com, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Form.com

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Form.com desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-formcom-from-the-gallery"></a>Adición de Form.com desde la galería
Para configurar la integración de Form.com en Azure AD, deberá agregar Form.com desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Form.com desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Form.com**, seleccione **Form.com** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Form.com en la lista de resultados](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Form.com con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Form.com para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Form.com.

Para establecer la relación de vínculo, en Form.com, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Form.com, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Form.com](#create-a-formcom-test-user)**: para tener un homólogo de Britta Simon en Form.com que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Form.com.

**Para configurar el inicio de sesión único de Azure AD con Form.com, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Form.com**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_samlbase.png)

3. En la sección **Dominio y direcciones URL de Form.com**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Form.com](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.wa-form.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.form.com`

    c. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con el siguiente patrón:
    | |
    |--|
    | `https://<subdomain>.wa-form.com/Member/UserAccount/SAML2.action` |
    | `https://<subdomain>.form.com/Member/UserAccount/SAML2.action` |
    
    > [!NOTE] 
    > Estos valores no son reales. Actualícelos con la dirección URL de inicio de sesión, la dirección URL de respuesta y el identificador reales. Póngase en contacto con el [equipo de atención al cliente de Form.com](https://form.com/about/company/contact-us/) para obtener estos valores. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_certificate.png) 

5. Para generar la **dirección URL de Metadatos**, lleve a cabo los pasos siguientes:

    a. Haga clic en **Registros de aplicaciones**.
    
    ![Configurar registro de aplicación](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_appregistrations.png)
   
    b. Haga clic en **Puntos de conexión** para abrir el cuadro de diálogo **Puntos de conexión**.  
    
    ![Configurar punto de conexión](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_endpointicon.png)

    c. Haga clic en el botón Copiar para copiar la dirección URL del **DOCUMENTO DE METADATOS DE FEDERACIÓN** y péguela en el Bloc de notas.
    
    ![Configurar punto de conexión](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_endpoint.png)
     
    d. Ahora, vaya a la página de propiedades de **Form.com**, copie el **Identificador de la aplicación** con el botón **Copiar** y péguelo en el Bloc de notas.
 
    ![Configurar identificador de la aplicación](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_appid.png)

    e. Genere la **Dirección URL de metadatos** con el patrón siguiente: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`

6. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-formcom-tutorial/tutorial_general_400.png)

7. En la sección **Configuración de Form.com**, haga clic en **Configurar Form.com** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configuración de Form.com](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_configure.png) 

8. Para configurar el inicio de sesión único en **Form.com**, es preciso enviar el **certificado (Base64)** descargado, la **dirección URL de metadatos** y la **dirección URL de servicio de inicio de sesión único de SAML** al [equipo de soporte técnico de Form.com](https://form.com/about/company/contact-us/). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-formcom-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-formcom-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-formcom-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-formcom-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-formcom-test-user"></a>Creación de un usuario de prueba de Form.com

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Form.com. Trabaje con el [equipo de soporte técnico de Form.com](https://form.com/about/company/contact-us/) para agregar los usuarios en la cuenta de Form.com.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Form.com.

![Asignación del rol de usuario][200] 

**Para asignar el usuario Britta Simon a Form.com, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Form.com**.

    ![Vínculo a Form.com en la lista de aplicaciones](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Form.com en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Form.com.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_203.png

