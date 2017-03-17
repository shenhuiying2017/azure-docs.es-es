---
title: "Tutorial: integración de Azure Active Directory con Predictix Assortment Planning | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Predictix Assortment Planning."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 37e686ff-f8e5-40b1-9d7e-f64b076917b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 8314db293ac3c9cbc2882c9a981819d0c5c02df3
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>Tutorial: Integración de Azure Active Directory con Predictix Assortment Planning
En este tutorial, obtendrá información sobre cómo integrar Predictix Assortment Planning con Azure Active Directory (Azure AD).

Integrar Predictix Assortment Planning con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Predictix Assortment Planning
* Puede permitir que los usuarios inicien sesión automáticamente en Predictix Assortment Planning (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Predictix Assortment Planning, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en Predictix Assortment Planning

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Microsoft Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Predictix Assortment Planning desde la galería
2. Configuración y prueba del inicio de sesión único de Microsoft Azure AD

## <a name="add-predictix-assortment-planning-from-the-gallery"></a>Adición de Predictix Assortment Planning desde la galería
Para configurar la integración de Predictix Assortment Planning en Azure AD, deberá agregar Predictix Assortment Planning desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Predictix Assortment Planning desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Predictix Assortment Planning**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_01.png)
7. En el panel de resultados, seleccione **Predictix Assortment Planning** y, luego, haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_02.png)

## <a name="configure-and-test-microsoft-azure-ad-sso"></a>Configuración y prueba del inicio de sesión único de Microsoft Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Microsoft Azure AD con Predictix Assortment Planning con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Predictix Assortment Planning para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Predictix Assortment Planning.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Predictix Assortment Planning.

Para configurar y probar el inicio de sesión único de Microsoft Azure AD con Predictix Assortment Planning, es preciso completar los siguientes pasos preliminares:

1. **[Configuración del inicio de sesión único de Microsoft Azure AD](#configuring-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Microsoft Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Predictix Assortment Planning](#creating-a-predictix-price-reporting-test-user)**: para tener un homólogo de Britta Simon en Predictix Assortment Planning que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para que Britta Simon pueda usar el inicio de sesión único de Microsoft Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-microsoft-azure-ad-sso"></a>Configuración del inicio de sesión único de Microsoft Azure AD
En esta sección, habilitará el inicio de sesión único de Microsoft Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Predictix Assortment Planning.

**Para configurar el inicio de sesión único de Microsoft Azure AD con Predictix Assortment Planning, siga estos pasos:**

1. En el portal clásico, en la página de integración de aplicaciones de **Predictix Assortment Planning**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en Predictix Assortment Planning?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_03.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_04.png) 
  1. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que usan los usuarios para iniciar sesión en la aplicación de Predictix Assortment Planning con el siguiente patrón: **https://\<company name-pricing\>.ap.predictix.com/sso/request**.
  2. click **Siguiente**
4. En la página **Configurar inicio de sesión único en Predictix Assortment Planning** , siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_05.png)
  1. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.  
  2. Haga clic en **Siguiente**.
5. Para configurar el inicio de sesión único para su aplicación, póngase en contacto con el equipo de soporte técnico de Predictix Assortment Planning y proporcione lo siguiente:
   
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
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_05.png) 
  1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
  2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
  3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:

   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_06.png) 
  1. En el cuadro de texto **Nombre**, escriba **Britta**.  
  2. En el cuadro de texto **Apellidos**, escriba **Simon**.
  3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  4. En la lista **Rol**, seleccione **Usuario**.
  5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_08.png) 
  1. Anote el valor del campo **Nueva contraseña**. 
  2. Haga clic en **Completo**.   

### <a name="create-an-predictix-assortment-planning-test-user"></a>Creación de un usuario de prueba de Predictix Assortment Planning
En esta sección, creará un usuario llamado Britta Simon en Predictix Assortment Planning. Trabaje con el equipo de soporte técnico de Predictix Assortment Planning para agregar usuarios a la plataforma de Predictix Assortment Planning.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Predictix Assortment Planning.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Predictix Assortment Planning, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **Predictix Assortment Planning**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-sso"></a>Prueba de SSO
En esta sección, probará la configuración de inicio de sesión único de Microsoft Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Predictix Assortment Planning en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Predictix Assortment Planning.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_205.png

