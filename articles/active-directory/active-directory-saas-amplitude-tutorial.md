---
title: 'Tutorial: Integración de Azure Active Directory con Amplitude | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Amplitude.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 496c9ffa-c833-41fa-8d17-2dc3044954d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeedes
ms.openlocfilehash: 5bcf666774876a6def48766f93066657862536df
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amplitude"></a>Tutorial: Integración de Azure Active Directory con Amplitude

En este tutorial, aprenderá a integrar Amplitude con Azure Active Directory (Azure AD).

La integración de Amplitude con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Amplitude.
- Puede permitir que los usuarios inicien sesión automáticamente en Amplitude (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Amplitude, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Amplitude

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Amplitude desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-amplitude-from-the-gallery"></a>Adición de Amplitude desde la galería
Para configurar la integración de Amplitude en Azure AD, es preciso agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Amplitude desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Amplitude**, seleccione **Amplitude** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Amplitude en la lista de resultados](./media/active-directory-saas-amplitude-tutorial/tutorial_amplitude_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Amplitude con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Amplitude para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Amplitude.

Para configurar y probar el inicio de sesión único de Azure AD con Amplitude, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Amplitude](#create-an-amplitude-test-user)**: para tener un homólogo de Britta Simon en Amplitude que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Amplitude.

**Para configurar el inicio de sesión único de Azure AD con Amplitude, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Amplitude**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-amplitude-tutorial/tutorial_amplitude_samlbase.png)

3. En la sección **Amplitude Domain and URLs** (Dominio y direcciones URL de Amplitude), realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Amplitude](./media/active-directory-saas-amplitude-tutorial/tutorial_amplitude_url.png)

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://amplitude.com/saml/sso/metadata`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://analytics.amplitude.com/saml/sso/<uniqueid>`.

    > [!NOTE]
    > El valor de dirección URL de respuesta no es real. Obtendrá el valor de dirección URL de respuesta más adelante en este tutorial.

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Amplitude](./media/active-directory-saas-amplitude-tutorial/tutorial_amplitude_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://analytics.amplitude.com/sso`

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-amplitude-tutorial/tutorial_amplitude_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-amplitude-tutorial/tutorial_general_400.png)
    
7. Inicie sesión en su sitio de la empresa Amplitude como administrador.

8. Haga clic en **Plan Admin** (Planear administración) en la barra de navegación izquierda.

    ![Configurar inicio de sesión único](./media/active-directory-saas-amplitude-tutorial/configure1.png)

9. Seleccione **Microsoft Azure Active Directory Metadata** (Metadatos de Microsoft Azure Active Directory) desde **SSO Integration** (Integración de SSO).

    ![Configurar inicio de sesión único](./media/active-directory-saas-amplitude-tutorial/configure2.png)

10. En la sección **Set Up Single Sign-On** (Configurar inicio de sesión único), realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-amplitude-tutorial/configure3.png)

    a. Abra el **XML de metadatos** descargado de Azure Portal en Bloc de notas y pegue el contenido en el cuadro de texto **Microsoft Azure Active Directory Metadata** (Metadatos de Microsoft Azure Active Directory).

    b. Copie el valor de **Reply URL (ACS)** (Dirección URL de respuesta [ACS]) y péguelo en el cuadro de texto **Reply URL** (Dirección URL de respuesta) de la sección de dominio y direcciones URL de Amplitude de Azure Portal.

    c. Haga clic en **Guardar**

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-amplitude-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-amplitude-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-amplitude-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-amplitude-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-an-amplitude-test-user"></a>Creación de un usuario de prueba de Amplitude

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Amplitude. Amplitude admite aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a Amplitude se crea un nuevo usuario, en caso de que no exista.
>[!Note]
>Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de Amplitude](https://amplitude.zendesk.com).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Amplitude.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Amplitude, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Amplitude**.

    ![Vínculo a Amplitude en la lista de aplicaciones](./media/active-directory-saas-amplitude-tutorial/tutorial_amplitude_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Amplitude en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Amplitude.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_203.png

