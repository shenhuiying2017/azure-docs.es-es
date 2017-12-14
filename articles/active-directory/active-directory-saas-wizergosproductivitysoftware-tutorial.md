---
title: "Tutorial: Integración de Azure Active Directory con Wizergos Productivity Software | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Wizergos Productivity Software."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: acc04396-13c5-4c24-ab9a-30fbc9234ebd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: jeedes
ms.openlocfilehash: d3ed319b457abb677484d3ef32876b4af46a09ae
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>Tutorial: integración de Azure Active Directory con Wizergos Productivity Software

En este tutorial, obtendrá información sobre cómo integrar Wizergos Productivity Software con Azure Active Directory (Azure AD).

La integración de Wizergos Productivity Software con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Wizergos Productivity Software.
- Puede permitir que los usuarios inicien sesión automáticamente en Wizergos Productivity Software (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Wizergos Productivity Software, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Un suscripción habilitada para el inicio de sesión único en Wizergos Productivity Software

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Wizergos Productivity Software desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>Incorporación de Wizergos Productivity Software desde la galería
Para configurar la integración de Wizergos Productivity Software en Azure AD, deberá agregar Wizergos Productivity Software desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Wizergos Productivity Software desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Wizergos Productivity Software**, seleccione **Wizergos Productivity Software** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Wizergos Productivity Software en la lista de resultados](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Wizergos Productivity Software mediante el uso de un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Wizergos Productivity Software para un usuario de Azure AD. En otras palabras, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Wizergos Productivity Software.

En Wizergos Productivity Software, asigne el valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** para establecer la relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con Wizergos Productivity Software, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Wizergos Productivity Software](#create-a-wizergos-productivity-software-test-user)**: para tener un homólogo de Britta Simon en Wizergos Productivity Software que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Wizergos Productivity Software.

**Para configurar el inicio de sesión único de Azure AD con Wizergos Productivity Software, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Wizergos Productivity Software**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_samlbase.png)

3. En la sección **Dominio y direcciones URL de Wizergos Productivity Software**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Wizergos Productivity Software](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_url.png)

    En el cuadro de texto **Identificador**, escriba la dirección URL: `http://www.wizergos.net`

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Wizergos Productivity Software**, haga clic en **Configurar Wizergos Productivity Software** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de Wizergos Productivity Software](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_configure.png) 

7. En otra ventana del explorador web, inicie sesión en su inquilino de Wizergos Productivity Software como administrador.

8. En el menú lateral, seleccione **Admin**.

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

9. En el menú de la izquierda de la página Admin, seleccione **AUTHENTICACIÓN** y haga clic en **Azure AD**.

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

10. En la sección **AUTHENTICACIÓN** , realice los pasos siguientes:

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
    
    a. Para cargar el certificado descargado de Azure AD, haga clic en **UPLOAD** (Cargar).
    
    b. En el cuadro de texto **Issuer URL** (Dirección URL del emisor), pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que copió de Azure Portal.
    
    c. En el cuadro de texto **Single Sign-On URL** (Dirección URL de inicio de sesión único), pegue el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) que copió de Azure Portal.
    
    d. En el cuadro de texto **Single Sign-Out URL** (Dirección URL de cierre de sesión único), pegue el valor de **Dirección URL de cierre de sesión** que copió de Azure Portal.
    
    e. Haga clic en el botón **Guardar** .

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-wizergos-productivity-software-test-user"></a>Creación de un usuario de prueba de Wizergos Productivity Software

En esta sección, creará un usuario llamado Britta Simon en Wizergos Productivity Software. Trabaje con el [equipo de soporte técnico de Wizergos Productivity Software](mailTo:support@wizergos.com) para agregar los usuarios a la plataforma Wizergos Productivity Software.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Wizergos Productivity Software.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Wizergos Productivity Software, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Wizergos Productivity Software**.

    ![Vínculo a Wizergos Productivity Software en la lista de aplicaciones](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Wizergos Productivity Software del panel de acceso, debería iniciar sesión automáticamente en su aplicación Wizergos Productivity Software.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_203.png

