---
title: "Tutorial: Integración de Azure Active Directory con TargetProcess | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y TargetProcess."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 78d64f38edb07b9eeffbe444e8bf98845e2df8f8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Tutorial: Integración de Azure Active Directory con TargetProcess

En este tutorial, aprenderá a integrar TargetProcess con Azure Active Directory (Azure AD).

Integrar TargetProcess con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a TargetProcess.
- Puede permitir que los usuarios inicien sesión automáticamente en TargetProcess (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con TargetProcess, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en TargetProcess

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de TargetProcess desde la galería
2. Configuración y prueba del inicio de sesión único en Azure AD

## <a name="add-targetprocess-from-the-gallery"></a>Incorporación de TargetProcess desde la galería
Para configurar la integración de TargetProcess en Azure AD, deberá agregar TargetProcess desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar TargetProcess desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **TargetProcess**, seleccione **TargetProcess** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Incorporación de TargetProcess desde la galería](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con TargetProcess con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de TargetProcess para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de TargetProcess.

Para establecer la relación de vínculo, en TargetProcess, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con TargetProcess, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de TargetProcess](#create-a-targetprocess-test-user)**: para tener un homólogo de Britta Simon en TargetProcess que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación TargetProcess.

**Para configurar el inicio de sesión único de Azure AD con TargetProcess, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **TargetProcess**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Inicio de sesión basado en SAML](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_samlbase.png)

3. En la sección de **Dominio y direcciones URL de TargetProcess**, lleve a cabo los pasos siguientes:

    ![Sección Dominio y direcciones URL de TargetProcess](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.tpondemand.com/`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.tpondemand.com/`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de TargetProcess](mailto:support@targetprocess.com) para obtener estos valores. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Sección Certificado de firma SAML](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar](./media/active-directory-saas-target-process-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de TargetProcess**, haga clic en **Configurar TargetProcess** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Sección Configuración de TargetProcess](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_configure.png) 

7. Inicie sesión en su aplicación TargetProcess como administrador.

8. En el menú de la parte superior, haga clic en **Configuración**.
   
    ![Configuración](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_05.png)

9. Haga clic en **Configuración**.
   
    ![Configuración](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_06.png) 

10. Haga clic en **Inicio de sesión único**.
   
    ![clic en Inicio de sesión único](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_07.png) 

11. En el cuadro de diálogo Configuración de inicio de sesión único, siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_08.png)
    
    a. Haga lic en **Habilitar inicio de sesión único**.
    
    b. En el cuadro de texto **Sign-on URL** (Dirección URL de inicio de sesión), pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    c. Abra el certificado descargado en el Bloc de notas, copie el contenido y luego péguelo en el cuadro de texto **Certificado** .
    
    d. Haga clic en **Habilitar aprovisionamiento de JIT**.

    e. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-target-process-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Para mostrar la lista de usuarios](./media/active-directory-saas-target-process-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Botón Agregar](./media/active-directory-saas-target-process-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Sección Usuario](./media/active-directory-saas-target-process-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-targetprocess-test-user"></a>Creación de un usuario de prueba de TargetProcess

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en TargetProcess.

TargetProcess admite el aprovisionamiento Just-In-Time. Ya lo ha habilitado en [Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on).

No hay ningún elemento de acción para usted en esta sección.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a TargetProcess para que use el inicio de sesión único de Azure.

![Asignar usuario][200] 

**Para asignar a Britta Simon a TargetProcess, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **TargetProcess**.

    ![TargetProcess en la lista de aplicaciones](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de TargetProcess en el panel de acceso, debería iniciar sesión automáticamente en su aplicación TargetProcess. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_203.png

