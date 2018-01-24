---
title: "Tutorial: Integración de Azure Active Directory con Vodeclic | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Vodeclic."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: 940c7bb5040fb91a03b01dc43ee07d52e3d4e63b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Tutorial: Integración de Azure Active Directory con Vodeclic

En este tutorial, obtendrá información sobre cómo integrar Vodeclic con Azure Active Directory (Azure AD).

Integrar Vodeclic con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Vodeclic.
- Puede permitir que los usuarios inicien sesión automáticamente en Vodeclic (inicio de sesión único o SSO) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Vodeclic, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de Vodeclic habilitada para el inicio de sesión único

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de evaluación gratuita de Azure AD, obtenga una [evaluación gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Vodeclic desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-vodeclic-from-the-gallery"></a>Adición de Vodeclic desde la galería
Para configurar la integración de Vodeclic en Azure AD, debe agregar Vodeclic desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Vodeclic desde la galería, realice los pasos siguientes:**

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), seleccione el icono **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Vodeclic**. En el panel de resultados, seleccione **Vodeclic** y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Vodeclic en la lista de resultados](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, puede configurar y probar el inicio de sesión único de Azure AD con Vodeclic con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Vodeclic para un usuario de Azure AD. Es decir, es necesario establecer un vínculo entre un usuario de Azure AD y el usuario relacionado de Vodeclic.

En Vodeclic, asigne a **Nombre de usuario** el mismo valor definido en **nombre de usuario** en Azure AD. Ahora ya ha establecido el vínculo entre los dos usuarios.

Para configurar y probar el inicio de sesión único de Azure AD con Vodeclic, complete los siguientes bloques de creación:

1. [Configurar el inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on), para permitir que los usuarios usen esta característica.
2. [Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user), para probar el inicio de sesión único de Azure AD con Britta Simon.
3. [Crear un usuario de prueba de Vodeclic](#create-a-vodeclic-test-user), para tener un homólogo de Britta Simon en Vodeclic que esté vinculado a la representación del usuario en Azure AD.
4. [Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user), para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. [Prueba del inicio de sesión único](#test-single-sign-on), para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Vodeclic.

**Para configurar el inicio de sesión único de Azure AD con Vodeclic, siga estos pasos:**

1. En la página de integración de la aplicación **Vodeclic** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo de inicio de sesión único**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

3. Si quiere configurar la aplicación en el modo iniciado mediante **IDP**, realice los siguientes pasos en la sección **Dominio y direcciones URL de Vodeclic**:

    ![Información de inicio de sesión único de dominio y direcciones URL de Vodeclic](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. En el cuadro **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.lms.vodeclic.net/auth/saml`

    b. En el cuadro **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

4. Si quiere configurar la aplicación en el modo iniciado mediante **SP**, haga clic en la casilla **Mostrar configuración avanzada de URL** y realice los siguientes pasos:

    ![Información de inicio de sesión único de dominio y direcciones URL de Vodeclic](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url1.png)

    En el cuadro **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador, la URL de respuesta y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de soporte técnico para clientes de Vodeclic](mailto:hotline@vodeclic.com) para obtener estos valores.

5. En la sección **Certificado de firma de SAML**, seleccione **XML de metadatos**. Luego, guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

6. Seleccione **Guardar**.

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-vodeclic-tutorial/tutorial_general_400.png)
    
7. Para configurar el inicio de sesión único en **Vodeclic**, envíe el archivo **XML de metadatos** descargado al [equipo de soporte técnico de Vodeclic](mailto:hotline@vodeclic.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación. Después de agregar esta aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, seleccione la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada en la [documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, seleccione el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos**. Después, seleccione **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, seleccione **Agregar** para abrir el cuadro de diálogo **Usuario**.

    ![Botón Agregar](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario**, realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Seleccione **Crear**.
 
### <a name="create-a-vodeclic-test-user"></a>Creación de un usuario de prueba de Vodeclic

En esta sección, creará un usuario llamado Britta Simon en Vodeclic. Colabore con el [equipo de soporte técnico de Vodeclic](mailto:hotline@vodeclic.com) para agregar usuarios en la plataforma Vodeclic. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

> [!NOTE]
> Según los requisitos de la aplicación, es posible que deba obtener la lista de elementos permitidos de la máquina. Para ello, debe compartir la dirección IP pública con el [equipo de soporte técnico de Vodeclic](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección dará permiso a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Vodeclic.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Vodeclic, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones y vaya a la vista de directorio. Después, vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Vodeclic**.

    ![Vínculo a Vodeclic en la lista de aplicaciones](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_app.png)  

3. En el menú de la izquierda, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Seleccione el botón **Agregar**. Después, seleccione **Usuarios y grupos**, en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista **Usuarios**.

6. En el cuadro de diálogo **Usuarios y grupos**, haga clic en el botón **Seleccionar**.

7. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el panel de acceso.

Al seleccionar el icono de Vodeclic en el panel de acceso, se inicia sesión automáticamente en la aplicación Vodeclic.

Para más información sobre el panel de acceso, consulte [Introducción al panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_203.png

