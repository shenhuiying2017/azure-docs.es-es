---
title: 'Tutorial: Integración de Azure Active Directory con Adobe Captivate Prime | Microsoft Docs'
description: Obtenga información para configurar el inicio de sesión único entre Azure Active Directory y Adobe Captivate Prime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f95b226-1465-47f4-b8b7-de4b0772abbc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: jeedes
ms.openlocfilehash: f61ab05d2c89167eeef0cb930a734bfafeeded1c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206706"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-captivate-prime"></a>Tutorial: Integración de Azure Active Directory con Adobe Captivate Prime

En este tutorial se aprende a integrar Adobe Captivate Prime con Azure Active Directory (Azure AD).

La integración de Adobe Captivate Prime con Azure AD ofrece las ventajas siguientes:

- En Azure AD puede controlar quién tiene acceso a Adobe Captivate Prime.
- Puede permitir que los usuarios inicien sesión automáticamente en Adobe Captivate Prime (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Adobe Captivate Prime, necesita lo siguiente:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Adobe Captivate Prime

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Adobe Captivate Prime desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-adobe-captivate-prime-from-the-gallery"></a>Incorporación de Adobe Captivate Prime desde la galería
Para configurar la integración de Adobe Captivate Prime en Azure AD, necesita agregar Adobe Captivate Prime desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Adobe Captivate Prime desde la galería, siga este procedimiento:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Adobe Captivate Prime**, seleccione **Adobe Captivate Prime** en el panel de resultados y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Adobe Captivate Prime en la lista de resultados](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección se configura y se prueba el inicio de sesión único de Azure AD con Adobe Captivate Prime con el usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Adobe Captivate Prime para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Adobe Captivate Prime.

Para configurar y probar el inicio de sesión único de Azure AD con Adobe Captivate Prime, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Adobe Captivate Prime](#create-an-adobe-captivate-prime-test-user)**: para tener un homólogo de Britta Simon en Adobe Captivate Prime que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección se habilita el inicio de sesión único de Azure AD en Azure Portal y se configura el inicio de sesión único en la aplicación Adobe Captivate Prime.

**Para configurar el inicio de sesión único de Azure AD con Adobe Captivate Prime, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Adobe Captivate Prime**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_samlbase.png)

3. En la sección **Dominio y direcciones URL de Adobe Captivate Prime**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Adobe Captivate Prime](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL como: `https://captivateprime.adobe.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL:`https://captivateprime.adobe.com/saml/SSO`

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_400.png)

6. Vaya a la pestaña **Propiedades**, copie la **URL de acceso de usuario** y péguela en el Bloc de notas.

    ![Vínculo de acceso de usuario](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_appprop.png)

7. Para configurar el inicio de sesión único en el lado de **Adobe Captivate Prime**, necesita enviar el archivo **XML de metadatos** descargado y la **URL de acceso de usuario** copiada al [equipo de soporte técnico de Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-adobecaptivateprime-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-adobecaptivateprime-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-adobecaptivateprime-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-adobecaptivateprime-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
  
### <a name="create-an-adobe-captivate-prime-test-user"></a>Crear un usuario de prueba de Adobe Captivate Prime

En esta sección se crea un usuario denominado Britta Simon en Adobe Captivate Prime. Trabaje con el [equipo de soporte técnico de Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com) para agregar los usuarios en la plataforma de Adobe Captivate Prime. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección se habilita a Britta Simon para que use el inicio de sesión único de Azure al concederle acceso a Adobe Captivate Prime.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Adobe Captivate Prime, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Adobe Captivate Prime**.

    ![Vínculo a Adobe Captivate Prime en la lista de aplicaciones](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Adobe Captivate Prime del Panel de acceso, debería iniciar sesión automáticamente en la aplicación Adobe Captivate Prime.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_203.png

