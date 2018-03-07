---
title: "Tutorial: Integración de Azure Active Directory con Boxcryptor | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Boxcryptor."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c46aa523-b58c-4a95-a800-db2e5e01c542
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jeedes
ms.openlocfilehash: d595d44fd09b37d6714af5935dc8967caa8ac90b
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="tutorial-azure-active-directory-integration-with-boxcryptor"></a>Tutorial: Integración de Azure Active Directory con Boxcryptor

En este tutorial, obtendrá información sobre cómo integrar Boxcryptor con Azure Active Directory (Azure AD).

La integración de Boxcryptor con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Boxcryptor.
- Puede habilitar que los usuarios inicien sesión automáticamente en Boxcryptor (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Boxcryptor, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en Boxcryptor

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Boxcryptor desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-boxcryptor-from-the-gallery"></a>Adición de Boxcryptor desde la galería
Para configurar la integración de Boxcryptor en Azure AD, deberá agregar Boxcryptor desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Boxcryptor desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Boxcryptor**, seleccione **Boxcryptor** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Boxcryptor en la lista de resultados](./media/active-directory-saas-boxcryptor-tutorial/tutorial_boxcryptor_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Boxcryptor con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Boxcryptor para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Boxcryptor.

Para configurar y probar el inicio de sesión único de Azure AD con Boxcryptor, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Boxcryptor](#create-a-boxcryptor-test-user)**: para tener un homólogo de Britta Simon en Boxcryptor que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Boxcryptor.

**Para configurar el inicio de sesión único de Azure AD con Boxcryptor, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Boxcryptor**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-boxcryptor-tutorial/tutorial_boxcryptor_samlbase.png)

3. En la sección **Dominios y direcciones URL de Boxcryptor**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Boxcryptor](./media/active-directory-saas-boxcryptor-tutorial/tutorial_boxcryptor_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL: `https://www.boxcryptor.com/app`

    b. En el cuadro de texto **Identificador**, escriba un valor: `boxcryptor`

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-boxcryptor-tutorial/tutorial_boxcryptor_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Boxcryptor**, haga clic en **Configurar Boxcryptor** para abrir la ventana **Configurar inicio de sesión**. Copie los valores de **URL del servicio de inicio de sesión único de SAML** e **Identificador de entidad de SAML** de la sección **Referencia rápida**.

    ![Configuración de Andromeda SCM](./media/active-directory-saas-boxcryptor-tutorial/tutorial_boxcryptor_configure.png)

7. Para configurar el inicio de sesión único en **Boxcryptor**, debe enviar el **certificado (Base64)** descargado, la **dirección URL del servicio de inicio de sesión único de SAML** y el **identificador de entidad de SAML** al [equipo de soporte técnico de Boxcryptor](mailto:support@boxcryptor.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-boxcryptor-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-boxcryptor-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-boxcryptor-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-boxcryptor-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-boxcryptor-test-user"></a>Creación de un usuario de prueba de Boxcryptor

En esta sección, creará un usuario llamado Britta Simon en Boxcryptor. Colabore con el [equipo de soporte técnico de Boxcryptor](mailto:support@boxcryptor.com) para agregar los usuarios o el dominio que deben incluirse en la lista blanca en la plataforma de Boxcryptor. Si el dominio lo agrega el equipo, los usuarios se aprovisionarán automáticamente en la plataforma de Boxcryptor. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Boxcryptor.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Boxcryptor, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Boxcryptor**.

    ![Vínculo a Boxcryptor en la lista de aplicaciones](./media/active-directory-saas-boxcryptor-tutorial/tutorial_boxcryptor_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Boxcryptor en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Boxcryptor.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_203.png

