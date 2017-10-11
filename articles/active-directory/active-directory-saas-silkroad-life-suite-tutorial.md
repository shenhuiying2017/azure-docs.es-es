---
title: "Tutorial: Integración de Azure Active Directory con SilkRoad Life Suite | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y SilkRoad Life Suite."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
ms.openlocfilehash: ecf4e31ecea00d003fc47ea4cebb781ca58957f7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Tutorial: Integración de Azure Active Directory con SilkRoad Life Suite
El objetivo de este tutorial es mostrar cómo integrar SilkRoad Life Suite con Azure Active Directory (Azure AD). 

La integración de SilkRoad Life Suite con Azure AD proporciona las siguientes ventajas: 

* Puede controlar en Azure AD quién tiene acceso a SilkRoad Life Suite. 
* Puede permitir que los usuarios inicien sesión automáticamente en SilkRoad Life Suite mediante inicio de sesión único (SSO) con sus cuentas de Azure AD.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con SilkRoad Life Suite, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el SSO en SilkRoad Life Suite

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de SilkRoad Life Suite desde la galería 
2. Configuración y prueba del inicio de sesión único de Azure AD

## <a name="add-silkroad-life-suite-from-the-gallery"></a>Adición de SilkRoad Life Suite desde la galería
Para configurar la integración de SilkRoad Life Suite en Azure AD, deberá agregar SilkRoad Life Suite desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SilkRoad Life Suite desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
   
    ![Active Directory][1]

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **SilkRoad Life Suite**.
   
    ![Aplicaciones][5]

7. En el panel de resultados, seleccione **SilkRoad Life Suite** y, luego, haga clic en **Completar** para agregar la aplicación.
   
    ![Aplicaciones][50]

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el SSO de Azure AD con SilkRoad Life Suite mediante un usuario de prueba llamado "Britta Simon".

Para que el SSO funcione, Azure AD debe saber cuál es el usuario homólogo en SilkRoad Life Suite de un usuario de Azure AD. Es decir, hay que establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SilkRoad Life Suite.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en SilkRoad Life Suite.

Para configurar y probar el inicio de sesión único de Azure AD con SilkRoad Life Suite, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SilkRoad Life Suite](#creating-a-silkroad-life-suite-test-user)** : para tener un homólogo de Britta Simon en SilkRoad Life Suite que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el SSO de Azure AD en el Portal de Azure clásico y configurarlo en la aplicación SilkRoad Life Suite.

**Para configurar el inicio de sesión único de Azure AD con SilkRoad Life Suite, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la compañía de SilkRoad Life Suite como administrador. 

  >[!NOTE] 
  > Para obtener acceso a la aplicación de autenticación de SilkRoad Life Suite para configurar la federación con Microsoft Azure AD, póngase en contacto con el soporte técnico o el representante de servicios de SilkRoad.
  > 

2. Vaya a **Proveedor de servicios** y, luego, haga clic en **Detalles de federación**. 
   
    ![Inicio de sesión único de Azure AD ][10] 

3. Haga clic en **Download Federation Metadata** (Descargar los metadatos de federación). Después, guarde el archivo de metadatos en el equipo.
   
    ![Inicio de sesión único de Azure AD ][11] 

4. En el Portal de Azure clásico, en la página de integración de aplicaciones de **SilkRoad Life Suite**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 

5. En la página **¿Cómo desea que los usuarios inicien sesión en SilkRoad Life Suite?**, seleccione **Inicio de sesión único de Azure AD** y, luego, haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][7] 

6. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Inicio de sesión único de Azure AD ][8]   
 1. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que los usuarios usan para iniciar sesión en el sitio de SilkRoad Life Suite (por ejemplo: *https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*).  
 2. Abra el archivo de metadatos **Silkroad** descargado. 
 3. Busque la etiqueta **AssertionConsumerService** y, después, copie el atributo **Ubicación**.         
   
    ![Inicio de sesión único de Azure AD ][21] 
 4. Pegue el valor en el cuadro de texto **Dirección URL de respuesta** .  
 5. Haga clic en **Siguiente**.

6. En la página **Configurar inicio de sesión único en SilkRoad Life Suite** , siga estos pasos:
   
    ![Inicio de sesión único de Azure AD ][9]  
 1. Haga clic en Descargar certificado y después guarde el archivo en el equipo.  
 2. Haga clic en **Siguiente**.

7. En la aplicación **SilkRoad**, haga clic en **Authentication Sources** (Orígenes de autenticación).
   
    ![Inicio de sesión único de Azure AD ][12] 

8. Haga clic en **Add Authentication Source**(Agregar origen de autenticación). 
   
    ![Inicio de sesión único de Azure AD][13] 

9. En la sección **Add Authentication Source** (Agregar origen de autenticación), realice los siguientes pasos: 
   
    ![Inicio de sesión único de Azure AD ][14]  
 1. En **Option 2 - Metadata File** (Opción 2 - Archivo de metadatos), haga clic en **Browse** (Examinar) para cargar el archivo de metadatos descargado.  
 2. Haga clic en **Create Identity Provider using File Data**(Crear proveedor de identidades con los datos del archivo).

10. En la sección **Authentication Sources** (Orígenes de autenticación), haga clic en **Edit** (Editar). 
    
     ![Inicio de sesión único de Azure AD ][15] 

11. En el cuadro de diálogo **Edit Authentication Source** (Editar origen de autenticación), realice los siguientes pasos: 
    
     ![Inicio de sesión único de Azure AD ][16] 
 1. En **Enabled** (Habilitado), seleccione **Yes** (Sí).   
 2. En el cuadro de texto **IdP Description** (Descripción de IdP), escriba una descripción para la configuración (por ejemplo, *SSO de Azure AD*).  
 3. En el cuadro de texto **IdP Name** (Nombre de IdP), escriba un nombre que sea específico para su configuración (por ejemplo, *Azure SP*).  
 4. Haga clic en **Guardar**.

12. Deshabilite todos los demás orígenes de autenticación. 
    
     ![Inicio de sesión único de Azure AD ][17]

13. En el Portal de Azure clásico, en la página **Confirmación del inicio de sesión único**, haga clic en **Siguiente**.  
    
     ![Inicio de sesión único de Azure AD ][18]

14. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
    
     ![Inicio de sesión único de Azure AD ][19]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png)  

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes: 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png)  
 1. En Tipo de usuario, seleccione Nuevo usuario de la organización.  
 2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**. 
 3. Haga clic en **Siguiente**.

6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes: 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png)  
 1. En el cuadro de texto **Nombre**, escriba **Britta**.    
 2. En el cuadro de texto **Apellidos**, escriba **Simon**. 
 3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**. 
 4. En la lista **Rol**, seleccione **Usuario**.
 5. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png)  
 1. Anote el valor del campo **Nueva contraseña**. 
 2. Haga clic en **Completo**.   

### <a name="create-a-silkroad-life-suite-test-user"></a>Creación de un usuario de prueba de SilkRoad Life Suite
El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en SilkRoad Life Suite. Britta debe tener un Id. de SSO (a veces se conoce como *AuthParam*) que coincida con su valor de **emailaddress** en Azure AD.

**Para crear un usuario llamado Britta Simon en SilkRoad Life Suite, realice los pasos siguientes:**

- Pida al equipo de soporte de SilkRoad Life Suite que cree un usuario que tenga como atributo **SSO ID** el mismo valor que el campo **emailaddress** de Britta Simon en Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el SSO de Azure concediéndole acceso a SilkRoad Life Suite.

![Asignar usuario][200] 

**Para asignar Britta Simon a SilkRoad Life Suite, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en la opción **Aplicaciones** del menú superior.
   
    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SilkRoad Life Suite**.
   
    ![Asignar usuario][202] 

3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 

4. En la lista Usuarios, seleccione **Britta Simon**.

5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  

Al hacer clic en el icono SilkRoad Life Suite en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de SilkRoad Life Suite.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png





