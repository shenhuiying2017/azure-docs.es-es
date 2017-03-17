---
title: "Tutorial: Integración de Azure Active Directory con Predictix Ordering | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Predictix Ordering."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2fe2f976-e97f-4368-9695-3e1624409e8b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1e0ef693f8176e61bf18e0a37be9bde1cc5d03a2
ms.openlocfilehash: 8f6eca0c23e6886fd302ab8371a99c8b366f8d75
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-predictix-ordering"></a>Tutorial: integración de Azure Active Directory con Predictix Ordering
En este tutorial, obtendrá información sobre cómo integrar Predictix Ordering con Azure Active Directory (Azure AD).

La integración de Predictix Ordering con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Predictix Ordering.
* Puede permitir que los usuarios inicien sesión automáticamente en Predictix Ordering (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Predictix Ordering, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en A Predictix Ordering

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Predictix Ordering desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-predictix-ordering-from-the-gallery"></a>Adición de Predictix Ordering desde la galería
Para configurar la integración de Predictix Ordering en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Predictix Ordering desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Predictix Ordering**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_01.png)
7. En el panel de resultados, seleccione **Predictix Ordering** y luego haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único (SSO) de Azure AD con Predictix Ordering con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Predictix Ordering para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Predictix Ordering.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como valor de **Username** (Nombre de usuario) en Predictix Ordering.

Para configurar y probar el inicio de sesión único de Azure AD con Predictix Ordering, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Predictix Ordering](#creating-a-predictix-price-reporting-test-user)**: para tener un homólogo de Britta Simon en Predictix Ordering que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, se habilita el inicio de sesión único de Azure AD en el portal clásico y se configura el inicio de sesión único en la aplicación Predictix Ordering.

**Para configurar el inicio de sesión único de Azure AD con Predictix Ordering, siga estos pasos:**

1. En el portal clásico, en la página de integración de la aplicación **Predictix Price Reporting**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en Predictix Ordering?**, seleccione **Inicio de sesión único de Azure AD** y, luego, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_03.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_04.png) 
  1. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en la aplicación Predictix Ordering con el siguiente patrón: **https://\<nombreDeLaCompañía-precios\>.ordering.predictix.com/sso/request**.
  2. Haga clic en **Siguiente**.
4. En la página **Configurar inicio de sesión único en Predictix Ordering** , siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_05.png) 
  1. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
  2. Haga clic en **Siguiente**.
5. Para configurar SSO único para una aplicación, póngase en contacto con el equipo de soporte técnico de Predictix Ordering y proporciónele la siguiente información:
  * El certificado descargado
  * El **identificador de entidad**
  * La **dirección URL de inicio de sesión único de SAML**
  * La **dirección URL del servicio de cierre de sesión único**
6. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_05.png) 
  1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
  2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
  3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:

   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_06.png) 
  1. En el cuadro de texto **Nombre**, escriba **Britta**.  
  2. En el cuadro de texto **Apellidos**, escriba **Simon**.
  3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  4. En la lista **Rol**, seleccione **Usuario**.
  5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_08.png) 
  1. Anote el valor del campo **Nueva contraseña**.
  2. Haga clic en **Completo**.   

### <a name="create-an-predictix-ordering-test-user"></a>Creación de un usuario de prueba de Predictix Ordering
En esta sección, creará un usuario llamado Britta Simon en Predictix Ordering. Trabaje con el equipo de soporte técnico de Predictix Ordering para agregar usuarios a la plataforma de Predictix Ordering.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, se habilita que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Predictix Ordering.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Predictix Ordering, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **Predictix Ordering**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
En esta sección, probará la configuración de SSO de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Predictix Ordering en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Predictix Ordering.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_205.png

