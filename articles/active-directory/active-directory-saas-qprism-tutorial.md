---
title: "Tutorial: Integración de Azure Active Directory con QPrism | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y QPrism."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: jeedes
ms.openlocfilehash: 1f697b95074e0fc9dbb3e8c7800e69f8ece9e0b3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Tutorial: integración de Azure Active Directory con QPrism

En este tutorial, aprenderá a integrar QPrism con Azure Active Directory (Azure AD).

La integración de QPrism con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a QPrism.
- Puede permitir que los usuarios inicien sesión automáticamente en QPrism (inicio de sesión único) con su cuenta de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Para obtener más detalles sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con QPrism, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en QPrism

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de QPrism desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-qprism-from-the-gallery"></a>Incorporación de QPrism desde la galería
Para configurar la integración de QPrism en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar QPrism desde la galería:**

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una aplicación nueva, en la parte superior del cuadro de diálogo, seleccione **Nueva aplicación**.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **QPrism**y seleccione **QPrism** desde el panel de resultados. Haga clic en el botón **Agregar** para agregar la aplicación.

    ![QPrism en la lista de resultados](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, puede configurar y probar el inicio de sesión único de Azure AD con QPrism con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de QPrism para el usuario de Azure AD. Es decir, debe haber una relación de vínculo entre un usuario de Azure AD y el relacionado de QPrism.

Para establecer la relación, en QPrism, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con QPrism, complete los siguientes bloques de creación:

1. [Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on), para permitir que los usuarios utilicen esta característica.
2. [Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user), para probar el inicio de sesión único de Azure AD con Britta Simon.
3. [Creación de un usuario de prueba de QPrism](#create-a-qprism-test-user), para tener un homólogo de Britta Simon en QPrism vinculado a la representación del usuario en Azure AD.
4. [Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user), para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. [Prueba del inicio de sesión único](#test-single-sign-on), para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación QPrism.

1. En Azure Portal, en la página de integración de la aplicación **QPrism**, seleccione **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_samlbase.png)

3. En la sección **Dominio y direcciones URL de QPrism**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de QPrism](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<customer domain>.qmyzone.com/login`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<customer domain>.qmyzone.com/metadata.php`.
         
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y la dirección URL de inicio de sesión reales. Póngase en contacto con el [equipo de atención al cliente de QPrism](mailto:qsupport-ce@quatrro.com) para obtener estos valores. 

4. Para generar la dirección URL de **Metadatos**, haga lo siguiente:

    a. Seleccione **App registrations** (Registros de aplicaciones).
    
    ![Configuración de registros de la aplicación de inicio de sesión único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appregistrations.png)
   
    b. Seleccione **Puntos de conexión** para abrir el cuadro de diálogo **Puntos de conexión**.  
    
    ![Configuración del punto de conexión de inicio de sesión único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpointicon.png)

    c. Haga clic en el botón Copiar para copiar la dirección URL del **DOCUMENTO DE METADATOS DE FEDERACIÓN** y péguela en el Bloc de notas.
    
    ![Configuración del punto de conexión de inicio de sesión único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpoint.png)
     
    d. Ahora, vaya a la página de propiedades de **QPrism** y copie el valor del **identificador de la aplicación** con el botón **Copiar**. A continuación, péguelo en el Bloc de notas.
 
    ![Configuración del identificador de la aplicación de inicio de sesión único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appid.png)

    e. Genere la **dirección URL de metadatos** con el patrón siguiente: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>` 

5. Seleccione **Guardar**.

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-qprism-tutorial/tutorial_general_400.png)
    
6. Para configurar el inicio de sesión único en el lado de **QPrism**, envíe la **dirección URL de metadatos** al [equipo de atención al cliente de QPrism](mailto:qsupport-ce@quatrro.com). Ellos se aseguran de que la conexión de inicio de sesión único de SAML está establecida correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación. Después de agregar esta aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, basta con hacer clic en la pestaña **Inicio de sesión único** y acceder a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más aquí sobre la característica de documentación insertada: [Administración de inicio de sesión único para aplicaciones empresariales]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-qprism-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-qprism-tutorial/create_aaduser_02.png)

3. Para abrir el cuadro de diálogo **Usuario**, en la parte superior del cuadro de diálogo **Todos los usuarios**, seleccione **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-qprism-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario**, haga lo siguiente:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-qprism-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Seleccione **Crear**.
 
### <a name="create-a-qprism-test-user"></a>Creación de un usuario de prueba de QPrism

En esta sección, creará un usuario llamado Britta Simon en QPrism. Trabaje con el [equipo de atención al cliente de QPrism](mailto:qsupport-ce@quatrro.com) para agregar los usuarios a la plataforma de QPrism. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a QPrism.

![Asignación del rol de usuario][200] 

**Para asignar a Simon Britta a QPrism:**

1. En Azure Portal, abra la vista de aplicaciones y, a continuación, vaya a la vista de directorio. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **QPrism**.

    ![Vínculo a QPrism en la lista de aplicaciones](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_app.png)  

3. En el menú de la izquierda, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Seleccione **Agregar**. Después, en **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista **Usuarios**.

6. En el cuadro de diálogo **Usuarios y grupos**, elija **Seleccionar**.

7. En **Agregar asignación**, seleccione **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

En el panel de acceso, al seleccionar el icono de QPrism, debería iniciar sesión automáticamente en la aplicación QPrism.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_203.png

