---
title: 'Tutorial: Integración de Azure Active Directory con Secret Server (On-Premises) | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Secret Server (On-Premises).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: 8aaf31022d54ebbd8c7c3f0bc72763f8c5139cbc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348519"
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Tutorial: Integración de Azure Active Directory con Secret Server (On-Premises)

En este tutorial, aprenderá a integrar Secret Server (On-Premises) con Azure Active Directory (Azure AD).

La integración de Secret Server (On-Premises) con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Secret Server (On-Premises).
- Puede permitir que los usuarios inicien sesión automáticamente en Secret Server (On-Premises) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Secret Server (On-Premises), necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Secret Server (On-Premises)

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Secret Server (On-Premises) desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Adición de Secret Server (On-Premises) desde la galería
Para configurar la integración de Secret Server (On-Premises) en Azure AD, es necesario que agregue Secret Server (On-Premises) desde la galería hasta la lista de aplicaciones SaaS administradas.

**Para agregar Secret Server (On-Premises) desde la galería, siga los pasos a continuación:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Secret Server (On-Premises)**, seleccione **Secret Server (On-Premises)** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Secret Server (On-Premises) en la lista de resultados](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Secret Server (On-Premises) con una usuaria de prueba llamada "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Secret Server (On-Premises) para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Secret Server (On-Premises).

Para configurar y probar el inicio de sesión único de Azure AD con Secret Server (On-Premises), es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Secret Server (On-Premises)](#create-a-secret-server-on-premises-test-user)**: para tener un homólogo de Britta Simon en Secret Server (On-Premises) que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Secret Server (On-Premises).

**Para configurar el inicio de sesión único de Azure AD con Secret Server (On-Premises), siga los pasos a continuación:**

1. En Azure Portal, en la página de integración de la aplicación **Secret Server (On-Premises)**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

3. En la sección **Secret Server (On-Premises) Domain and URLs** (Dominio y direcciones URL de Secret Server [On-Premises]), realice los pasos siguientes si quiere configurar la aplicación en modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Secret Server (On-Premises)](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. En el cuadro de texto **Identificador**, escriba el valor elegido por el usuario como ejemplo: `https://secretserveronpremises.azure`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx `.

    > [!NOTE]
    > El identificador de entidad mostrado anteriormente es solo un ejemplo; es libre de elegir cualquier valor único que identifique la instancia de Secret Server en Azure AD. Este identificador de entidad se deberá enviar al [equipo de atención al cliente de Secret Server (On-Premises)](https://thycotic.force.com/support/s/) para que se lo configuren. Para obtener más información, lea [este artículo](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Secret Server (On-Premises)](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<SecretServerURL>/login.aspx`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de Secret Server (On-Premises)](https://thycotic.force.com/support/s/) para obtener estos valores.

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

6. Marque **Show advanced certificate signing settings** (Mostrar configuración avanzada de firma de certificados) y, en **Opción de firma**, seleccione **Sign SAML response and assertion** (Firmar respuesta y aserción SAML).

    ![Opciones de firma](./media/active-directory-saas-secretserver-on-premises-tutorial/signing.png)

7. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_400.png)
    
8. En la sección **Configuración de Secret Server (On-Premises)**, haga clic en **Configurar Secret Server (On-Premises)** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de Secret Server (On-Premises)](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

9. Para configurar el inicio de sesión único en **Secret Server (On-Premises)**, es preciso enviar el **certificado (Base64), la dirección URL de cierre de sesión, la dirección URL del servicio de inicio de sesión único de SAML** y el **identificador de entidad de SAML** descargados al [equipo de soporte técnico de Secret Server (On-Premises)](https://thycotic.force.com/support/s/). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Crear un usuario de prueba de Secret Server (On-Premises)

En esta sección, creará un usuario llamado Britta Simon en Secret Server (On-Premises). Trabaje con el [equipo de soporte técnico de Secret Server (On-Premises)](https://thycotic.force.com/support/s/) para agregar a los usuarios a la plataforma de Secret Server (On-Premises). Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure, para lo cual le concederá acceso a Secret Server (On-Premises).

![Asignación de rol de usuario][200]

**Para asignar a Britta Simon a Secret Server (On-Premises), siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Secret Server (On-Premises)**.

    ![Vínculo de Secret Server (On-Premises) en la lista de aplicaciones](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Secret Server (On-Premises) en el Panel de acceso, iniciará sesión automáticamente en la aplicación Secret Server (On-Premises).
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_203.png

