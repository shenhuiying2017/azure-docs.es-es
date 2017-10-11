---
title: "Tutorial: Integración de Azure Active Directory con SciQuest Spend Director | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SciQuest Spend Director."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
ms.openlocfilehash: 84b707668dc45e92e6151f422f1c919f638533b1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Tutorial: Integración de Azure Active Directory con SciQuest Spend Director
El objetivo de este tutorial es mostrar cómo integrar SciQuest Spend Director con Azure Active Directory (Azure AD).  
La integración de SciQuest Spend Director con Azure AD le proporciona las siguientes ventajas: 

* Puede controlar en Azure AD quién tiene acceso a SciQuest Spend Director. 
* Puede permitir que los usuarios inicien sesión automáticamente en SciQuest Spend Director (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con SciQuest Spend Director, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción de SciQuest Spend Director habilitada para el inicio de sesión único

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.  
La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar SciQuest Spend Director desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>Agregar SciQuest Spend Director desde la galería
Para configurar la integración de SciQuest Spend Director en Azure AD, deberá agregar SciQuest Spend Director desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SciQuest Spend Director desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1]

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **SciQuest Spend Director**.
   
    ![Aplicaciones][5]

7. En el panel de resultados, seleccione **SciQuest Spend Director** y, después, haga clic en **Completar** para agregar la aplicación.
   
    ![Aplicaciones][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con SciQuest Spend Director según un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de SciQuest Spend Director para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SciQuest Spend Director.  
Para establecer esta relación de vínculo, se asigna el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en SciQuest Spend Director.

Para configurar y probar el inicio de sesión único de Azure AD con SciQuest Spend Director, debe completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SciQuest Spend Director](#creating-a-halogen-software-test-user)** : para tener un homólogo de Britta Simon en SciQuest Spend Director que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación de SciQuest Spend Director.

**Para configurar el inicio de sesión único de Azure AD con SciQuest Spend Director, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **SciQuest Spend Director**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][8]

2. En la página **¿Cómo desea que los usuarios inicien sesión en SciQuest Spend Director?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][9]

3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes: 
   
    ![Configurar las opciones de la aplicación][10]
   
     a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en la aplicación de SciQuest Spend Director con el patrón siguiente: *https://.*sciquest.com/.**
   
     b. En el cuadro de texto **URL de respuesta**, escriba el mismo valor que ha escrito en el cuadro de texto **Dirección URL de inicio de sesión**. 
   
     c. Haga clic en **Siguiente**.

4. En la página **Configurar inicio de sesión único en SciQuest Spend Director**, haga clic en **Descargar metadatos** y, después, guarde el archivo de metadatos de forma local en el equipo.
   
    ![Qué es Azure AD Connect][11]

5. Póngase en contacto con el soporte técnico de SciQuest para habilitar este método de autenticación mediante los datos descargados anteriores.

6. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**. 
   
    ![Qué es Azure AD Connect][15]

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Qué es Azure AD Connect][100] 

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Qué es Azure AD Connect][101] 

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. 
   
    ![Qué es Azure AD Connect][102] 

5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Qué es Azure AD Connect][103] 
   
    a. En **Tipo de usuario**, seleccione **Nuevo usuario de la organización**.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.

6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes: 
   
    ![Qué es Azure AD Connect][104] 
   
    a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
    b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
    d. En la lista **Rol**, seleccione **Usuario**.
   
    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Qué es Azure AD Connect][105]  

8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Qué es Azure AD Connect][106]   
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-sciquest-spend-director-test-user"></a>Creación de un usuario de prueba de SciQuest Spend Director
El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en SciQuest Spend Director.

Deberá ponerse en contacto con el equipo de soporte técnico de SciQuest Spend Director y proporcionarles los detalles acerca de la cuenta de prueba que se ha creado.

Como alternativa, también puede aprovechar el aprovisionamiento Just-In-Time, una característica de inicio de sesión único que es compatible con SciQuest Spend Director.  
Si el aprovisionamiento Just-In-Time está habilitado, los usuarios se crean automáticamente en SciQuest Spend Director durante un intento de inicio de sesión único, si no existen. Esta característica elimina la necesidad de crear manualmente usuarios homólogos de inicio de sesión único.

Para habilitar el aprovisionamiento Just-In-Timed, deberá ponerse en contacto con el equipo de soporte técnico de SciQuest Spend Director.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon utilice el inicio de sesión único de Azure concediéndole acceso a SciQuest Spend Director.

![Qué es Azure AD Connect][200]

**Para asignar a Britta Simon a SciQuest Spend Director, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Qué es Azure AD Connect][201]

2. En la lista de aplicaciones, seleccione **SciQuest Spend Director**.
   
    ![Qué es Azure AD Connect][202]

3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Qué es Azure AD Connect][203]

4. En la lista Usuarios, seleccione **Britta Simon**.
   
    ![Qué es Azure AD Connect][204]

5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Qué es Azure AD Connect][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  
Al hacer clic en el icono de SciQuest Spend Director en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de SciQuest Spend Director.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_09.png 
[101]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_10.png 
[102]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_11.png 
[103]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_12.png 
[104]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_13.png 
[105]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_14.png 
[106]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_15.png 
[200]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_16.png 
[201]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_17.png 
[202]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_20.png

