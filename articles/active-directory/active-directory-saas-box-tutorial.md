---
title: 'Tutorial: Integración de Azure Active Directory con Box | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Box.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2017
ms.author: jeedes
ms.openlocfilehash: daad9104798dc02b479b4e022287c3630e4a67a0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34336877"
---
# <a name="integrate-azure-active-directory-with-box"></a>Integración de Azure Active Directory con Box

En este tutorial, aprenderá a integrar Azure Active Directory (Azure AD) con Box.

Mediante la integración de Azure AD con Box, obtendrá las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Box.
- Puede permitir que los usuarios inicien sesión automáticamente en Box (inicio de sesión único o SSO) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Para información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Box, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de inicio de sesión único habilitada para Box.

> [!NOTE]
> *No* se recomienda usar un entorno de producción para probar los pasos de este tutorial.

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. 

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Box desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-box-from-the-gallery"></a>Adición de Box desde la galería
Para configurar la integración de Azure AD con Box, agregue Box desde la galería a la lista de aplicaciones SaaS administradas de la manera siguiente:

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Ventana Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior de la ventana.

    ![Botón “Nueva aplicación”][3]

4. En el cuadro de búsqueda, escriba **Box**, seleccione **Box** en la lista de resultados y, a continuación, seleccione **Agregar**.

    ![Box en la lista de resultados](./media/active-directory-saas-box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, se configura y prueba el inicio de sesión único de Azure AD con Box con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD necesita identificar al usuario de Box y a su homólogo en Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el mismo usuario de Box.

Para establecer la relación de vínculo, asigne el valor de *nombre de usuario* de Box como valor de *nombre de usuario* de Azure AD.

Para configurar y probar el inicio de sesión único de Azure AD con Box, es preciso completar los bloques de creación que se detallan en las siguientes cinco secciones.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

Haga lo siguiente para habilitar el inicio de sesión único de Azure AD en Azure Portal y configurarlo en la aplicación Box:

1. En Azure Portal, en la ventana de integración de la aplicación **Box**, haga clic en **Inicio de sesión único**.

    ![Vínculo Inicio de sesión único][4]

2. En la ventana **Inicio de sesión único**, en el cuadro **Modo de inicio de sesión único**, seleccione **Inicio de sesión basado en SAML**.
 
    ![Ventana Inicio de sesión único](./media/active-directory-saas-box-tutorial/tutorial_box_samlbase.png)

3. En **Box Domain and URLs** (Dominio y direcciones URL de Box), haga lo siguiente:

    ![Información de dominio y direcciones URL de inicio de sesión único de Box](./media/active-directory-saas-box-tutorial/url3.png)

    a. En el cuadro **Dirección URL de inicio de sesión**, escriba una con el siguiente formato: *https://\<subdomain>.box.com*.

    b. En el cuadro de texto **Identificador**, escriba **box.net**.
     
    > [!NOTE] 
    > Los valores anteriores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Para obtener los valores, póngase en contacto con el [equipo de soporte técnico de Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). 

4. En la sección **Certificado de firma de SAML**, seleccione **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-box-tutorial/tutorial_box_certificate.png) 

5. Seleccione **Guardar**.

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-box-tutorial/tutorial_general_400.png)
    
6. Para configurar el inicio de sesión único para la aplicación, siga el procedimiento que se describe en [Set up SSO on your own](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown) (Configuración del inicio de sesión único por su cuenta).

> [!NOTE] 
> Si no puede habilitar la configuración de inicio de sesión único para su cuenta de Box, puede que necesite ponerse en contacto con el [equipo de atención al cliente de Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) y proporcionar el archivo XML descargado.

> [!TIP]
> Mientras configure la aplicación, puede leer una versión concisa de las instrucciones anteriores en [Azure Portal](https://portal.azure.com). Después de agregar la aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, seleccione la pestaña **Inicio de sesión único** y, después, acceda a la documentación insertada a través de la sección **Configuración** en la parte inferior. Para más información sobre la característica de documentación insertada, consulte la [documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
>

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, creará el usuario de prueba Britta Simon en Azure Portal siguiendo estos pasos:

![Creación de un usuario de prueba de Azure AD][100]

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**.

    ![Vínculo de Azure Active Directory](./media/active-directory-saas-box-tutorial/create_aaduser_01.png)

2. Para mostrar una lista de usuarios actuales, seleccione **Usuarios y grupos** > **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-box-tutorial/create_aaduser_02.png)

3. En la parte superior de la ventana **Todos los usuarios**, seleccione **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-box-tutorial/create_aaduser_03.png)

    Se abre la ventana **Usuario**.

4. En la ventana **Usuario**, haga lo siguiente:

    ![Ventana Usuario](./media/active-directory-saas-box-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Seleccione **Crear**.
 
### <a name="create-a-box-test-user"></a>Creación de usuario de prueba de Box

En esta sección, creará un usuario de prueba llamado Britta Simon en Box. Box admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. Si el usuario no existe aún, se crea uno nuevo cuando se intenta acceder a Box. No es necesaria ninguna acción por su parte para crear el usuario.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Box. Para ello, haga lo siguiente:

![Asignación de rol de usuario][200]

1. En Azure Portal, abra la vista **Aplicaciones**, vaya a la vista **Directorio** y seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Vínculos "Aplicaciones empresariales" y "Todas las aplicaciones"][201] 

2. En la lista **Aplicaciones**, seleccione **Box**.

    ![El vínculo de Box](./media/active-directory-saas-box-tutorial/tutorial_box_app.png)  

3. En el panel izquierdo, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Seleccione **Agregar** y, después, en el panel **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Panel Agregar asignación][203]

5. En la ventana **Usuarios y grupos**, en la lista **Usuarios**, seleccione **Britta Simon**.

6. Seleccione el botón **Seleccionar**.

7. En la ventana **Agregar asignación**, seleccione **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Cuando se selecciona el icono de **Box** en el Panel de acceso, se abre la página de inicio de sesión para que inicie sesión en su aplicación Box.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Configuración del aprovisionamiento de usuarios](active-directory-saas-box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-box-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-box-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-box-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-box-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-box-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-box-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-box-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-box-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-box-tutorial/tutorial_general_203.png

