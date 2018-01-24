---
title: "Tutorial: Integración de Azure Active Directory con IriusRisk | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre IriusRisk y Azure Active Directory."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d2c854d5-101d-4d67-80e0-87749e1a0352
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: jeedes
ms.openlocfilehash: e33d584b3e7eb939d669253e8c6ef40fd4f963c9
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="tutorial-azure-active-directory-integration-with-iriusrisk"></a>Tutorial: Integración de Azure Active Directory con IriusRisk

En este tutorial, aprenderá a integrar IriusRisk con Azure Active Directory (Azure AD).

La integración de IriusRisk con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a IriusRisk.
- Puede permitir que los usuarios inicien sesión automáticamente en IriusRisk (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con IriusRisk, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en IriusRisk

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de IriusRisk desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-iriusrisk-from-the-gallery"></a>Adición de IriusRisk desde la galería
Para configurar la integración de IriusRisk en Azure AD, será preciso que agregue IriusRisk desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar IriusRisk desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **IriusRisk**, seleccione **IriusRisk** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![IriusRisk en la lista de resultados](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con IriusRisk con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de IriusRisk para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de IriusRisk.

Para establecer la relación de vínculo, en IriusRisk, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con IriusRisk, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de IriusRisk](#create-a-iriusrisk-test-user)**: para tener un homólogo de Britta Simon en IriusRisk que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación IriusRisk.

**Para configurar el inicio de sesión único de Azure AD con IriusRisk, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **IriusRisk**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_samlbase.png)

3. En la sección **Dominio y direcciones URL de IriusRisk**, lleve a cabo los pasos siguientes:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de IriusRisk](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.iriusrisk.com/ui#!login`.

    b. En el cuadro de texto **Identificador**, escriba el valor `iriusrisk-sp`.

    > [!NOTE] 
    > El valor de la dirección URL de inicio de sesión no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de atención al cliente de IriusRisk](mailto:info@continuumsecurity.net) para obtener este valor. 

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_400.png)

6. Para configurar el inicio de sesión único en **IriusRisk**, necesita enviar el archivo **XML de metadatos** descargado al [equipo de soporte técnico de IriusRisk](mailto:info@continuumsecurity.net). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-iriusrisk-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-iriusrisk-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-iriusrisk-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-iriusrisk-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-iriusrisk-test-user"></a>Creación de un usuario de prueba de IriusRisk

El objetivo de esta sección es crear un usuario llamado Britta Simon en IriusRisk. IriusRisk admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a IriusRisk, se creará un usuario, en caso de que no exista.

> [!Note]
> Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de IriusRisk](mailto:info@continuumsecurity.net).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a IriusRisk.

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a IriusRisk, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **IriusRisk**.

    ![Vínculo a IriusRisk en la lista de aplicaciones](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de IriusRisk en el panel de acceso, debería iniciar sesión automáticamente en su aplicación IriusRisk.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_203.png

