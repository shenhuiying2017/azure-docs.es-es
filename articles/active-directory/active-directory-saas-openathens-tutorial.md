---
title: "Tutorial: Integración de Azure Active Directory con OpenAthens | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y OpenAthens."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: af26e007c953c4157f5ee7a4251a52e9c45a6eac
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Tutorial: Integración de Azure Active Directory con OpenAthens

En este tutorial, obtendrá información sobre cómo integrar OpenAthens con Azure Active Directory (Azure AD).

La integración de OpenAthens con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a OpenAthens.
- Puede permitir que los usuarios inicien sesión automáticamente en OpenAthens (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con OpenAthens, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en OpenAthens

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba gratuita durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de OpenAthens desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-openathens-from-the-gallery"></a>Adición de OpenAthens desde la galería
Para configurar la integración de OpenAthens en Azure AD, será preciso que agregue OpenAthens desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar OpenAthens desde la galería**

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), seleccione el icono **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales** y, a continuación, vaya a **Todas las aplicaciones**.

    ![Panel Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **OpenAthens**, seleccione **OpenAthens** en el panel de resultados y, a continuación, seleccione el botón **Agregar**.

    ![OpenAthens en la lista de resultados](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con OpenAthens con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de OpenAthens para el usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de OpenAthens.

Para establecer la relación de vínculo en OpenAthens, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con OpenAthens, es preciso completar los siguientes bloques de creación:

1. [Configuración del inicio de sesión único en Azure AD](#configure-azure-ad-single-sign-on), para permitir a los usuarios usar esta característica.
2. [Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user), para probar el inicio de sesión único de Azure AD con Britta Simon.
3. [Creación de un usuario de prueba de OpenAthens](#create-a-openathens-test-user), para tener un homólogo de Britta Simon en OpenAthens que esté vinculado a la representación del usuario en Azure AD.
4. [Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user), para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. [Prueba del inicio de sesión único](#test-single-sign-on), para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación OpenAthens.

**Para configurar el inicio de sesión único de Azure AD con OpenAthens**

1. En Azure Portal, en la página de integración de la aplicación **OpenAthens**, seleccione **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_samlbase.png)

3. En la sección **Dominio y direcciones URL de OpenAthens**, escriba el valor `https://login.openathens.net/saml/2/metadata-sp` en el cuadro de texto **Identificador**.

    ![Información de dominio y direcciones URL de inicio de sesión único de OpenAthens](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_url.png)

4. En la sección **Certificado de firma de SAML**, seleccione **XML de metadatos** y, a continuación, guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado de firma de SAML](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_certificate.png) 

5. Seleccione el botón **Guardar**.

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-openathens-tutorial/tutorial_general_400.png)

6. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la empresa de OpenAthens.

7. Seleccione **Connections** (Conexiones) de la lista de la pestaña **Management** (Administración). 

    ![Configurar inicio de sesión único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application1.png)

8. Seleccione **SAML 1.1/2.0** y, a continuación, seleccione el botón **Configure** (Configurar).

    ![Configurar inicio de sesión único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application2.png)
    
9. Para agregar la configuración, seleccione el botón **Browse** (Examinar) para cargar el archivo .xml de metadatos que descargó desde Azure Portal y, a continuación, seleccione **Add** (Agregar).

    ![Configurar inicio de sesión único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application3.png)

10. Realice los pasos siguientes en la pestaña **Details** (Detalles).

    ![Configurar inicio de sesión único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application4.png)

    a. En **Display name mapping** (Asignación de nombre para mostrar), seleccione **Use attribute** (Usar atributo).

    b. En el cuadro de texto **Display name attribute** (Atributo de nombre para mostrar), escriba el valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. En **Unique user mapping** (Asignación de usuario único), seleccione **Use attribute** (Usar atributo).

    d. En el cuadro de texto **Unique user attribute** (Atributo de usuario único), introduzca el valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. En **Status** (Estado), seleccione las tres casillas de verificación.

    f. En **Create local accounts** (Crear cuentas locales), seleccione **automatically** (automáticamente).

    g. Seleccione **Save changes** (Guardar los cambios).

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación. Después de agregar esta aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, seleccione la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Para más información sobre la característica de documentación insertada, consulte la [documentación insertada de Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Para crear un usuario de prueba en Azure AD**

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-openathens-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, seleccione **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-openathens-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, seleccione **Agregar** para abrir el cuadro de diálogo **Usuario**.

    ![Botón Agregar](./media/active-directory-saas-openathens-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-openathens-tutorial/create_aaduser_04.png)

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico de Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, a continuación, anote el valor que se muestra en el cuadro de texto **Contraseña**.

    d. Seleccione **Crear**.
  
### <a name="create-an-openathens-test-user"></a>Creación de un usuario de prueba de OpenAthens

OpenAthens admite el aprovisionamiento Just-In-Time y los usuarios se crean automáticamente después de que se realice la autenticación correctamente. No es necesario realizar ninguna acción en esta sección.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a OpenAthens.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a OpenAthens**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio, vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista **Aplicaciones**, seleccione **OpenAthens**.

    ![Vínculo a OpenAthens en la lista de aplicaciones](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_app.png)  

3. En el menú de la izquierda, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Seleccione el botón **Agregar**. Después, en el panel **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Panel Agregar asignación][203]

5. En la lista **Usuarios y grupos**, seleccione **Britta Simon**.

6. En la lista **Usuarios y grupos**, seleccione el botón **Seleccionar**.

7. En el panel **Agregar asignación**, seleccione el botón **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al seleccionar el icono **OpenAthens** en el panel de acceso, debería iniciar sesión automáticamente en la aplicación OpenAthens.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* Para obtener una lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory, consulte [Tutoriales de integración de aplicaciones SaaS para su uso con Azure AD](active-directory-saas-tutorial-list.md).
* Para más información sobre el acceso de aplicaciones y el inicio de sesión único con Azure Active Directory, consulte [¿Qué es el acceso de aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

<!--Image references-->

[1]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_203.png

