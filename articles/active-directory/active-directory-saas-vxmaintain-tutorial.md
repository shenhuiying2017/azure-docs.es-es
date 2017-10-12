---
title: "Tutorial: Integración de Azure Active Directory con vxMaintain | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y vxMaintain."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: ad87534af448356b8cc80d8ddd278bfb8a9165e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-integrate-azure-active-directory-with-vxmaintain"></a>Tutorial: Integración de Azure Active Directory con vxMaintain

En este tutorial, obtendrá información sobre cómo integrar vxMaintain con Azure Active Directory (Azure AD).

Esta integración proporciona varias ventajas importantes. Puede:

- Controlar en Azure AD quién tiene acceso a vxMaintain.
- Permitir que los usuarios inicien sesión automáticamente en vxMaintain mediante inicio de sesión único (SSO) con sus cuentas de Azure AD.
- Administrar sus cuentas en una ubicación central, Azure Portal.

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con vxMaintain, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de inicio de sesión único de vxMaintain habilitada

> [!NOTE]
> No se recomienda usar un entorno de producción para probar los pasos de este tutorial.

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. 

La situación descrita en este tutorial consta de dos bloques de creación principales:

* Agregar vxMaintain desde la galería
* Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-vxmaintain-from-the-gallery"></a>Adición de vxMaintain desde la galería
Para configurar la integración de vxMaintain con Azure AD, deberá agregar vxMaintain desde la galería a la lista de aplicaciones SaaS administradas.

Para agregar vxMaintain desde la galería, haga lo siguiente:

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), seleccione el botón **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Panel "Aplicaciones empresariales"][2]
    
3. Para agregar una aplicación, en el cuadro de diálogo **Todas las aplicaciones**, seleccione **Nueva aplicación**.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **vxMaintain**.

    ![Lista desplegable de "Modo de inicio de sesión único"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_search.png)

5. En la lista de resultados, seleccione **vxMaintain**y, a continuación, seleccione **Agregar**.

    ![El vínculo a vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con vxMaintain con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo en vxMaintain para el usuario de Azure AD. Es decir, debe establecer una relación de vínculo entre el usuario de Azure AD y el usuario correspondiente de vxMaintain.

Para establecer la relación de vínculo, asigne el valor **nombre de usuario** de vxMaintain como valor de **nombre de usuario** de Azure AD.

Para configurar y probar el inicio de sesión único de Azure AD con vxMaintain, complete los siguientes bloques de creación.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, puede habilitar el inicio de sesión único de Azure AD en Azure Portal y configurar el inicio de sesión único en la aplicación vxMaintain de la siguiente manera:

1. En Azure Portal, en la página de integración de la aplicación **vxMaintain**, seleccione **Inicio de sesión único**.

    ![Comando "Inicio de sesión único"][4]

2. Para habilitar el inicio de sesión único, en la lista desplegable **Modo de inicio de sesión único**, seleccione **Inicio de sesión basado en SAML**.
 
    ![Comando "Inicio de sesión basado en SAML"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

3. En **Dominio y direcciones URL de vxMaintain**, haga lo siguiente:

    ![Sección Dominio y direcciones URL de vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL que tenga la siguiente sintaxis: `https://<company name>.verisae.com`.

    b. En el cuadro **URL de respuesta**, escriba una dirección URL que tenga la siguiente sintaxis: `https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true`

    > [!NOTE] 
    > Los valores anteriores no son reales. Actualícelos con el identificador y la URL de respuesta reales. Para obtener los valores, póngase en contacto con el [equipo de soporte técnico de vxMaintain](http://www.verisae.com/contact-us).
 
4. En la sección **Certificado de firma de SAML**, seleccione **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Sección "Certificado de firma SAML"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

5. Seleccione **Guardar**.

    ![Botón Guardar](./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_400.png)

6. Para configurar el inicio de sesión único de **vxMaintain**, envíe el archivo **XML de metadatos** al [equipo de soporte técnico de vxMaintain](http://www.verisae.com/contact-us).

> [!TIP]
> Cuando configure la aplicación, puede leer una versión concisa de las instrucciones anteriores en [Azure Portal](https://portal.azure.com). Después de agregar la aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, seleccione la pestaña **Inicio de sesión único** y, a continuación, acceda a la documentación incrustada en la sección **Configuración**. 
>
>Para más información sobre la característica de documentación incrustada, consulte [Administración de inicio de sesión único para aplicaciones empresariales](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará el usuario de prueba Britta Simon en Azure Portal siguiendo estos pasos:

![Usuario de prueba de Azure AD][100]

1. En el panel izquierdo de **Azure Portal**, seleccione el botón **Azure Active Directory**.

    ![Botón "Azure Active Directory"](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_01.png) 

2. Para mostrar una lista de usuarios, vaya a **Usuarios y grupos** > **Todos los usuarios**.
    
    ![Vínculo "Todos los usuarios"](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_02.png)  
    Se abre el cuadro de diálogo **Todos los usuarios**. 

3. Para abrir el cuadro de diálogo **Usuario**, seleccione **Agregar**.
 
    ![Botón Agregar](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_03.png) 

4. En el cuadro de diálogo **Usuario**, haga lo siguiente:
 
    ![Cuadro de diálogo Usuario](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_04.png) 

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario de prueba Britta Simon.

    c. Seleccione la casilla **Mostrar contraseña** y, después , anote el valor que se generó en el cuadro **Contraseña**.

    d. Seleccione **Crear**.
 
### <a name="create-a-vxmaintain-test-user"></a>Creación de un usuario de prueba de vxMaintain

En esta sección, creará una usuaria de prueba llamada Britta Simon en vxMaintain. Trabaje con el [equipo de soporte técnico de vxMaintain](http://www.verisae.com/contact-us) para agregar usuarios a la plataforma de vxMaintain. Antes de usar el inicio de sesión único, cree y active los usuarios.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que la usuaria de prueba Britta Simon use el inicio de sesión único (SSO) de Azure, para lo cual le concederá acceso a vxMaintain. Para ello, haga lo siguiente:

![Usuario de prueba en la lista Nombre para mostrar][200] 

1. En la vista **Aplicaciones** de Azure Portal, vaya a la vista **Directorio** > **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Vínculo "Todas las aplicaciones"][201] 

2. En la lista **Aplicaciones**, seleccione **vxMaintain**.

    ![El vínculo a vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

3. En el panel izquierdo, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202] 

4. Seleccione **Agregar** y, después, en el panel **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][203]

5. En el cuadro de diálogo **Usuarios y grupos**, en la lista **Usuarios** seleccione **Britta Simon** y, después, el botón **Seleccionar**.

7. En el cuadro de diálogo **Agregar asignación**, seleccione **Asignar**.
    
### <a name="test-your-azure-ad-single-sign-on"></a>Prueba del inicio de sesión único de Azure AD

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el panel de acceso.

Al seleccionar el icono de **vxMaintain** en el panel de acceso, debería iniciar sesión automáticamente en su aplicación vxMaintain.

Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="next-steps"></a>Pasos siguientes

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_203.png

