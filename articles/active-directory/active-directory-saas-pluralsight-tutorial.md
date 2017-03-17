---
title: "Tutorial: integración de Azure Active Directory con Pluralsight | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Pluralsight."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4c3f07d2-4e1f-4ea3-9025-c663f1f2b7b4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: f77f459219a5c9a0e218de924c0c7578647a3594
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-pluralsight"></a>Tutorial: Integración de Azure Active Directory con Pluralsight
El objetivo de este tutorial es mostrar cómo integrar Pluralsight con Azure Active Directory (Azure AD).

Integrar Pluralsight con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Pluralsight.
* Puede permitir que los usuarios inicien sesión automáticamente en Pluralsight (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Pluralsight, se necesitan los siguientes elementos:

* Una suscripción de Azure
* Una suscripción habilitada para el inicio de sesión único en Pluralsight

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. 

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Pluralsight desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-pluralsight-from-the-gallery"></a>Adición de Pluralsight desde la galería
Para configurar la integración de Pluralsight en Azure AD, deberá agregar Pluralsight desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Pluralsight desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Pluralsight**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_01.png)
7. En el panel de resultados, seleccione **Pluralsight** y haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_06.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Pluralsight con un usuario de prueba llamado "Britta Simon".

Para configurar y probar el inicio de sesión único de Azure AD con Pluralsight, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Pluralsight](#creating-a-pluralsight-test-user)** : para tener un homólogo de Britta Simon en Pluralsight que esté vinculado a la representación de esta en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Pluralsight.

La aplicación Pluralsight espera las aserciones de SAML en un formato específico, lo cual requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla le muestra un ejemplo de esto. 

![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_02.png) 

También puede agregar el atributo **"Id. exclusivo"** con el valor correspondiente como EmployeeID o cualquier otro aspecto que se adapte a su organización. Tenga en cuenta que no es un atributo obligatorio, pero puede agregarlo para identificar al usuario único. 

**Para configurar el inicio de sesión único de Azure AD con Pluralsight, siga estos pasos:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Pluralsight**, en el menú de la parte superior, haga clic en **Atributos**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_81.png) 
2. En el cuadro de diálogo **Atributos de token de SAML**, realice los siguientes pasos: 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/2829.png) 
  * Para cada atributo de usuario en el cuadro rojo de la tabla anterior, mantenga el puntero sobre el atributo y, a continuación, haga clic en Eliminar. 
3. Para agregar los **atributos de token de SAML**requeridos, para cada fila mostrada en la tabla siguiente, realice los pasos que se indican a continuación:
   
   | Nombre del atributo | Valor de atributo |
   | --- | --- |
   | Nombre de usuario |user.givenname |
   | Apellidos |user.surname |
   | Email |user.mail |
4. Haga clic en **agregar atributo de usuario** para abrir el cuadro de diálogo **Agregar atributo de usuario**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_82.png)
  1. En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo que se muestra para esa fila.
  2. En la lista **Valor de atributo** , seleccione el valor de atributo que se muestra para esa fila.
  3. Haga clic en **Completo**.    
  4. Haga clic en **Aplicar cambios**.
 
   ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/3232.png) 
    
5. En el menú de la parte superior, haga clic en **Inicio rápido**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_83.png)  
6. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Pluralsight**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
7. En la página **¿Cómo desea que los usuarios inicien sesión en Pluralsight?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_03.png) 
8. En la página del cuadro de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_04.png) 
  1. En el cuadro de texto URL de inicio de sesión, escriba la dirección URL que usan los usuarios para iniciar sesión en su aplicación de Pluralsight con el siguiente patrón: `https://<instance name>.pluralsight.com/sso/<comapny name>`
  2. Haga clic en **Siguiente**.
9. En la página **Configurar inicio de sesión único en Pluralsight**, siga estos pasos: 

  ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_05.png)   
  1. Haga clic en **Descargar metadatos**y luego guarde el archivo en el equipo.
  2. Haga clic en **Siguiente**.
10. Para configurar el inicio de sesión único para la aplicación, póngase en contacto con el equipo de [servicios profesionales](mailTo:professionalservices@pluralsight.com) de Pluralsight y proporcione el archivo de metadatos descargado.
11. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
12. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

* En la lista Usuarios, seleccione **Britta Simon**.

  ![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_05.png)  
  1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
  2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
  3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_06.png) 
  1. En el cuadro de texto **Nombre**, escriba **Britta**.  
  2. En el cuadro de texto **Apellidos**, escriba **Simon**.
  3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  4. En la lista **Rol**, seleccione **Usuario**.
  5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_08.png) 
  1. Anote el valor del campo **Nueva contraseña**.
  2. Haga clic en **Completo**.   

### <a name="create-a-pluralsight-test-user"></a>Creación de un usuario de prueba de Pluralsight
El objetivo de esta sección es crear un usuario llamado Britta Simon en Pluralsight. Trabaje con el equipo de soporte técnico de Pluralsight para agregar los usuarios en la cuenta de Pluralsight. 

>[!NOTE]
>Si necesita crear un usuario manualmente, es preciso que se ponga en contacto con el equipo de soporte técnico de Pluralsight. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo cual se le concederá acceso a Pluralsight.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Pluralsight, siga estos pasos:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **Pluralsight**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Pluralsight en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de Pluralsight.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_205.png

