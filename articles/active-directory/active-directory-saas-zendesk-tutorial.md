---
title: "Tutorial: Integración de Azure Active Directory con Zendesk | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Zendesk."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 095fdd68cafbb1bf7d753dd82821bdc2fd089ef0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Tutorial: Integración de Azure Active Directory con Zendesk

En este tutorial, aprenderá a integrar Zendesk con Azure Active Directory (Azure AD).

La integración de Zendesk con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Zendesk.
- Puede permitir que los usuarios inicien sesión automáticamente en Zendesk (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Zendesk, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Zendesk

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Zendesk desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-zendesk-from-the-gallery"></a>Adición de Zendesk desde la galería
Para configurar la integración de Zendesk en Azure AD, es preciso agregar dicha solución desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Zendesk desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Zendesk**, seleccione **Zendesk** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Zendesk en la lista de resultados](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Zendesk con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Zendesk para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Zendesk.

Para establecer la relación de vínculo, en Zendesk, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Zendesk, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Crear un usuario de prueba de Zendesk](#create-a-zendesk-test-user)**: el objetivo es tener un homólogo de Britta Simon en Zendesk que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Zendesk.

**Para configurar el inicio de sesión único de Azure AD con Zendesk, realice los pasos siguientes:**

1. En la página de integración de la aplicación  **Zendesk** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_samlbase.png)

3. En la sección **Dominio y direcciones URL de Zendesk**, lleve a cabo los pasos siguientes:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de Zendesk](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.zendesk.com`.

    b. En el cuadro de texto **Identificador**, escriba el valor con el siguiente patrón: `<subdomain>.zendesk.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico para clientes de Zendesk](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) para obtener estos valores. 
 
4. En la sección **Certificado de firma de SAML**, copie el valor de **HUELLA DIGITAL** del certificado.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_certificate.png)

5. La aplicación Zendesk espera las aserciones de SAML en un formato específico. No hay ningún atributo SAML obligatorio, pero opcionalmente puede agregar un atributo desde la sección **Atributos de usuario** mediante los pasos siguientes: 

     ![Configurar inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes1.png)

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar la opción de agregar el inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/tutorial_attribute_04.png)

    ![Configurar la opción de agregar el atributo del inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.
 
    > [!NOTE] 
    > Los atributos de extensión se usan para agregar atributos que, de forma predeterminada, no están en Azure AD. Haga clic en [User attributes that can be set in SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) (Atributos de usuario que se pueden usar en SAML) para obtener la lista de atributos que acepta **Zendesk**.  

6. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/tutorial_general_400.png)

7. En la sección **Configuración de Zendesk**, haga clic en **Configurar Zendesk** para abrir la ventana **Configurar inicio de sesión**. Copie las **direcciones URL del servicio de inicio de sesión único de SAML y de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de Zendesk](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_configure.png) 

8. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Zendesk como administrador.

9. Haga clic en **Administrador**.

10. En el panel de navegación izquierdo, haga clic en **Settings** (Configuración) y luego en **Security** (Seguridad).

11. En la pestaña **Security** (Seguridad), lleve a cabo los pasos siguientes: 
   
     ![Seguridad](./media/active-directory-saas-zendesk-tutorial/ic773089.png "Seguridad")

    ![Inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/ic773090.png "Inicio de sesión único")

     a. Haga clic en la pestaña **Admin & Agents** (Administración y agentes).

     b. Seleccione **Single sign-on (SSO) and SAML** (Inicio de sesión único (SSO) y SAML) y, luego, seleccione **SAML**.

     c. En el cuadro de texto **SAML SSO URL** (Dirección URL de inicio de sesión único de SAML), pegue el valor de **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal. 

     d. En el cuadro de texto **Logout URL** (Dirección URL de cierre de sesión remoto), pegue el valor de **dirección URL de cierre de sesión** que copió de Azure Portal.
        
     e. En el cuadro de texto **Certificate Fingerprint** (Huella digital de certificado), pegue el valor de **Huella digital** del certificado que haya copiado de Azure Portal.
     
     f. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-zendesk-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-zendesk-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-zendesk-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-zendesk-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-zendesk-test-user"></a>Crear un usuario de prueba de Zendesk

Para que los usuarios de Azure AD puedan iniciar sesión en **Zendesk**, deben aprovisionarse en **Zendesk**.  
El comportamiento esperado depende del rol asignado en la aplicación:

 1. Las cuentas de **usuario final** se aprovisionan automáticamente al iniciar sesión.
 2. Las cuentas de **agente** y **administrador** se deben aprovisionar manualmente en **Zendesk** antes de iniciar la sesión.
 
**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **Zendesk** .

2. Seleccione la pestaña **Customer List** (Lista de clientes).

3. Seleccione la pestaña **User** (Usuario) y haga clic en **Add** (Agregar).
   
    ![Agregar usuario](./media/active-directory-saas-zendesk-tutorial/ic773632.png "Agregar usuario")
4. Escriba el **nombre** y la **dirección de correo electrónico** de una cuenta de Azure AD existente que quiera aprovisionar y, luego, haga clic en **Guardar**.
   
    ![Nuevo usuario](./media/active-directory-saas-zendesk-tutorial/ic773633.png "Nuevo usuario")

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Zendesk ofrecida por Zendesk para aprovisionar cuentas de usuario de AAD.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Zendesk.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a Zendesk, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Zendesk**.

    ![Vínculo de Zendesk en la lista de aplicaciones](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Zendesk en el Panel de acceso, debería iniciar sesión automáticamente en dicha aplicación.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_203.png

