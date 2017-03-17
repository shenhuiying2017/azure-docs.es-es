---
title: "Tutorial: Integración de Azure Active Directory con Nomadesk | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Nomadesk."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d261b776-b48e-45f0-9722-0297adefabb8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: d8dc70a5217d21871d229dece61bb9ad147fa1c2
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-nomadesk"></a>Tutorial: integración de Azure Active Directory con Nomadesk
El objetivo de este tutorial es mostrar cómo integrar Nomadesk con Azure Active Directory (Azure AD).

La integración de Nomadesk con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Nomadesk.
* Puede permitir que los usuarios inicien sesión automáticamente en Nomadesk (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Nomadesk, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en Nomadesk

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Nomadesk desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-nomadesk-from-the-gallery"></a>Adición de Nomadesk desde la galería
Para configurar la integración de Nomadesk en Azure AD, será preciso que agregue Nomadesk desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Nomadesk desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Nomadesk**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_01.png)
7. En el panel de resultados, seleccione **Nomadesk** y haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Nomadesk con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Nomadesk para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Nomadesk.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en Nomadesk.

Para configurar y probar el inicio de sesión único de Azure AD con Nomadesk, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Crear un usuario de prueba de Nomadesk](#creating-a-nomadesk-test-user)** : para tener un homólogo de Britta Simon en Nomadesk que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurarlo en la aplicación Nomadesk.

**Para configurar el inicio de sesión único de Azure AD con Nomadesk, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de la aplicación **Nomadesk**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6]
2. En la página **¿Cómo desea que los usuarios inicien sesión en Nomadesk?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_03.png)
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_04.png)
  * En el cuadro de texto URL de inicio de sesión, escriba la dirección URL de inicio de sesión de los usuarios en la aplicación Nomadesk con el siguiente patrón: **“https://mynomadesk.com/logon/saml/TENANTID”**. Cuando se hace referencia a un nombre genérico, **TENANTID** debe reemplazarse por un identificador de inquilino real.
4. En la página **Configure single sign-on at Nomadesk** (Configurar inicio de sesión único en Nomadesk), siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_05.png)
  1. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
  2. Haga clic en **Siguiente**.
5. Para configurar el inicio de sesión único para la aplicación, póngase en contacto con el equipo de soporte de Nomadesk a través de support@nomadesk.com. Adjunte el archivo de certificado descargado a su correo y comparta las direcciones URL de los metadatos (identidad de entidad, dirección URL de inicio de sesión de SSO y dirección URL de cierre de sesión de SSO) con el equipo de Nomadesk para configurar SSO en su lado.
6. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_09.png)
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_03.png)
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_04.png)
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_05.png)
  1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
  2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
  3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_06.png)
 1. En el cuadro de texto **Nombre**, escriba **Britta**.  
 2. En el cuadro de texto **Apellidos**, escriba **Simon**. 
 3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**. 
 4. En la lista **Rol**, seleccione **Usuario**.  
 5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_07.png)
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_08.png) 
  1. Anote el valor del campo **Nueva contraseña**.
  2. Haga clic en **Completo**.   

### <a name="create-a-nomadesk-test-user"></a>Creación de un usuario de prueba de Nomadesk
El objetivo de esta sección es crear un usuario llamado Britta Simon en Nomadesk. Nomadesk admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a Nomadesk se creará un nuevo usuario, en caso de que no exista. [Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Si necesita crear manualmente un usuario, es preciso que se ponga contacto con el equipo de soporte técnico de Nomadesk. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Nomadesk.

![Asignar usuario][200]

**Para asignar Britta Simon a Nomadesk, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **Nomadesk**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_50.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Nomadesk en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Nomadesk.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_205.png

