---
title: "Tutorial: integración de Azure Active Directory con Tangoe Command Premium Mobile | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Tangoe Command Premium Mobile."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 930150c07d84f886796208d7e745bdaec78966f5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Tutorial: Integración de Azure Active Directory con Tangoe Command Premium Mobile

En este tutorial, obtendrá información sobre cómo integrar Tangoe Command Premium Mobile con Azure Active Directory (Azure AD).

La integración de Tangoe Command Premium Mobile con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Tangoe Command Premium Mobile.
- Puede permitir que los usuarios inicien sesión automáticamente en Tangoe Command Premium Mobile (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Tangoe Command Premium Mobile, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Tangoe Command Premium Mobile

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Tangoe Command Premium Mobile desde la galería
2. Configuración y prueba del inicio de sesión único en Azure AD

## <a name="add-tangoe-command-premium-mobile-from-the-gallery"></a>Incorporación de Tangoe Command Premium Mobile desde la galería
Para configurar la integración de Tangoe Command Premium Mobile en Azure AD, deberá agregar Tangoe Command Premium Mobile desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Tangoe Command Premium Mobile desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Tangoe Command Premium Mobile**, seleccione **Tangoe Command Premium Mobile** desde el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Incorporación de Tangoe Command Premium Mobile desde la galería ](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Tangoe Command Premium Mobile con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Tangoe Command Premium Mobile para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Tangoe Command Premium Mobile.

Para establecer la relación de vínculo, en Tangoe Command Premium Mobile, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Tangoe Command Premium Mobile, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Tangoe Command Premium Mobile](#create-a-tangoe-command-premium-mobile-test-user)** : para tener un homólogo de Britta Simon en Tangoe Command Premium Mobile que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Tangoe Command Premium Mobile.

**Para configurar el inicio de sesión único de Azure AD con Tangoe Command Premium Mobile, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Tangoe Command Premium Mobile**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Inicio de sesión basado en SAML](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_samlbase.png)

3. En la sección **Dominio y direcciones URL de Tangoe Command Premium Mobile**, lleve a cabo los pasos siguientes:

    ![Dominio y direcciones URL de Tangoe Command Premium Mobile](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=<tenant issuer>&TARGET=<target page url>`.

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://sso.tangoe.com/sp/ACS.saml2`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Tangoe Command Premium Mobile Client](https://www.tangoe.com/contact-2/) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Sección Certificado de firma SAML](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar](./media/active-directory-saas-tangoe-tutorial/tutorial_general_400.png)
    
6. En la sección **Tangoe Command Premium Mobile Configuration**, haga clic en **Configurar Tangoe Command Premium Mobile** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Sección de configuración de Tangoe Command Premium Mobile](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_configure.png) 

7. Para configurar el inicio de sesión único para su aplicación, póngase en contacto con el [equipo de soporte técnico de Tangoe Command Premium Mobile Client](https://www.tangoe.com/contact-2/) y proporcione lo siguiente:

   - El archivo de metadatos descargado
   - El **identificador de entidad de SAML**
   - La **dirección URL del servicio de inicio de sesión único de SAML**
   - La **dirección URL de cierre de sesión**

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Usuarios y grupos -> Todos los usuarios](./media/active-directory-saas-tangoe-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Agregar usuario](./media/active-directory-saas-tangoe-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Página del cuadro de diálogo Usuario](./media/active-directory-saas-tangoe-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-tangoe-command-premium-mobile-test-user"></a>Crear un usuario de prueba de Tangoe Command Premium Mobile

En esta sección, creará un usuario llamado Britta Simon en Tangoe Command Premium Mobile. 

La aplicación Tangoe Command Premium Mobile necesita que todos los usuarios estén aprovisionados en la aplicación antes de realizar el inicio de sesión único. Así pues, colabore con el [equipo de soporte técnico de Tangoe Command Premium Mobile Client](https://www.tangoe.com/contact-2/) para aprovisionar todos estos usuarios en la aplicación. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Tangoe Command Premium Mobile.

![Asignar usuario][200] 

**Para asignar Britta Simon a Tangoe Command Premium Mobile, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Tangoe comando Premium Mobile**.

    ![Tangoe Command Premium Mobile en la lista de aplicaciones](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de SSO de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Tangoe Command Premium Mobile en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Tangoe Command Premium Mobile. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_203.png

