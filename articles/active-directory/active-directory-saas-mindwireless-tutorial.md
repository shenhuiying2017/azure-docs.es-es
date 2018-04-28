---
title: 'Tutorial: Integración de Azure Active Directory con mindWireless | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y mindWireless.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bd00a339-27c9-4904-b66f-a95bf597ac3c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeedes
ms.openlocfilehash: 1c08b0fe24e2545a07e3335cc0e0ae87a2c0540c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-mindwireless"></a>Tutorial: Integración de Azure Active Directory con mindWireless

En este tutorial, aprenderá a integrar mindWireless con Azure Active Directory (Azure AD).

La integración de mindWireless con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a mindWireless.
- Puede permitir que los usuarios inicien sesión automáticamente en mindWireless (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con mindWireless se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en mindWireless

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de mindWireless desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-mindwireless-from-the-gallery"></a>Adición de mindWireless desde la galería
Para configurar la integración de mindWireless en Azure AD, deberá agregar mindWireless desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar mindWireless desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **mindWireless**, seleccione **mindWireless** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![mindWireless en la lista de resultados](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con mindWireless con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de mindWireless para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de mindWireless.

Para configurar y probar el inicio de sesión único de Azure AD con mindWireless, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de mindWireless](#create-a-mindwireless-test-user)**: para tener un homólogo de Britta Simon en mindWireless que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación mindWireless.

**Para configurar el inicio de sesión único de Azure AD con mindWireless, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **mindWireless**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_samlbase.png)

3. En la sección **mindWireless Domain and URLs** (Dominio y direcciones URL de mindWireless), lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de mindWireless](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.mwsmart.com/`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.mwsmart.com/SAML/AssertionConsumerService.aspx`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de mindWireless](mailto:sdulloor@mindwireless.com) para obtener estos valores.

4. La aplicación mindWireless espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos de token de SAML.

5. La siguiente captura de pantalla le muestra un ejemplo de esto. El nombre de la notificación siempre será **Employee ID**, cuyo valor hemos asignado a user.employeeid, que contiene el valor de EmployeeID del usuario. Aquí se realiza la asignación de usuario de Azure AD a mindWireless en el valor EmployeeID, pero puede asignarle un valor diferente que también se base en la configuración de la aplicación. Puede trabajar primero con el [ de soporte técnico de mindWireless](mailto:sdulloor@mindwireless.com) para usar el identificador correcto de un usuario y asignar ese valor con la notificación **Employee ID**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mindwireless-tutorial/tutorial_attribute.png)

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo del token de SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo | Valor de atributo | Valor de espacio de nombres |
    | -------------- | --------------- | ----------------|
    | Id. de empleado | user.employeeid | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mindwireless-tutorial/tutorial_attribute_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-mindwireless-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. En el cuadro de texto **Espacio de nombres**, escriba el valor del espacio de nombres que se muestra para esa fila.
    
    e. Haga clic en **Aceptar**.
    
7. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_certificate.png) 

8. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-mindwireless-tutorial/tutorial_general_400.png)

9. En la sección **Configuración de mindWireless**, haga clic en **Configurar mindWireless** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de mindWireless](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_configure.png) 

10. Para configurar el inicio de sesión único en **mindWireless**, es necesario enviar el **certificado (Base64), la dirección URL del servicio de inicio de sesión único de SAML** y el **identificador de entidad de SAML** descargados al [equipo de soporte técnico de mindWireless](mailto:sdulloor@mindwireless.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).

### <a name="create-a-mindwireless-test-user"></a>Creación de un usuario de prueba de mindWireless

En esta sección, creará un usuario llamado Britta Simon en mindWireless. Trabaje con el [equipo de soporte técnico de mindWireless](mailto:sdulloor@mindwireless.com) para agregar los usuarios en la plataforma de mindWireless. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a mindWireless.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a mindWireless, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **mindWireless**.

    ![El vínculo de mindWireless en la lista de aplicaciones](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de mindWireless en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación mindWireless.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_203.png

