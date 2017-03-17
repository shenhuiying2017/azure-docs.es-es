---
title: "Tutorial: Integración de Azure Active Directory con MOVEit Transfer | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y MOVEit Transfer."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: e1b4cda400205611a957c23d9a9b90c918d260f3
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer"></a>Tutorial: Integración de Azure Active Directory con MOVEit Transfer
El objetivo de este tutorial es mostrar cómo integrar MOVEit Transfer con Azure Active Directory (Azure AD).

La integración de MOVEit Transfer con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a MOVEit Transfer.
* Puede permitir que los usuarios inicien sesión automáticamente en el inicio de sesión único (SSO) de MOVEit Transfer con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con MOVEit Transfer, se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en MOVEit Transfer

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

1. Adición de MOVEit Transfer desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-moveit-transfer-from-the-gallery"></a>Adición de MOVEit Transfer desde la galería
Para configurar la integración de MOVEit Transfer en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar MOVEit Transfer desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **MOVEit Transfer**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_01.png)
7. En el panel de resultados, seleccione **MOVEit Transfer** y haga clic en **Completar** para agregar la aplicación.
   
    ![Selección de la aplicación en la galería](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con MOVEit Transfer con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de MOVEit Transfer para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de MOVEit Transfer.

Esta relación de vínculo se establece mediante la asignación del valor de **nombre de usuario** en Azure AD como valor del **nombre de usuario** en MOVEit Transfer.

Para configurar y probar el inicio de sesión único de Azure AD con MOVEit Transfer, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en MOVEit Transfer](#creating-a-moveit-transfer-test-user)** : para tener un homólogo de Britta Simon en MOVEit Transfer que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación MOVEit Transfer.

**Para configurar el inicio de sesión único de Azure AD con MOVEit Transfer, siga estos pasos:**

1. En el portal clásico, en la página de integración de aplicaciones de **MOVEit Transfer**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en MOVEit Transfer?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_03.png)
3. En el cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_04.png)
  1. En el cuadro de texto **URL de inicio de sesión** , escriba la dirección URL de inicio de sesión con su propio dominio.
  2. En el cuadro de texto **Identificador** , escriba la dirección URL del identificador de la entidad.
  3. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con la interfaz del consumidor de aserciones habilitada.
  4. Haga clic en **Siguiente**.
   
   >[!NOTE]
   >Tenga en cuenta que tiene que actualizar estos valores con los valores reales de URL de inicio de sesión e Identificador. Para obtener estos valores, puede consultar al paso 8 o ponerse en contacto con [MOVEit Transfer](https://www.ipswitch.com/support/technical-support).
   >  
4. En la página **Configurar inicio de sesión único en MOVEit Transfer**, siga estos pasos y haga clic en **Siguiente**:
   
  ![Configurar inicio de sesión único](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_05.png)  
  1. Haga clic en **Descargar metadatos**y luego guarde el archivo en el equipo.
  2. Haga clic en **Siguiente**.
5. Inicie la sesión en el inquilino de MOVEit Transfer como administrador.
6. En la barra de navegación de la izquierda, haga clic en **Settings**(Configuración).
   
  ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)
7. Haga clic en el vínculo **Single Signon** (Inicio de sesión único) que se encuentra en **Security Policies -> User Auth** (Directivas de seguridad -> Autenticación de usuario).
   
  ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)
8. Haga clic en el vínculo Metadata URL (Dirección URL de metadatos) para descargar el documento de metadatos.
   
  ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)   
  * Compruebe que **entityID** coincide con el valor de **Identificador** del paso&3;.
  * Compruebe que la dirección URL de la ubicación de **AssertionConsumerService** coincide con el valor de **URL DE RESPUESTA** del paso&3;.
     
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)
9. Haga clic en el botón **Add Identity Provider** (Agregar proveedor de identidades) para agregar un nuevo proveedor de identidad federada.
   
  ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)
10. Haga clic en **Browse...** (Examinar...) para seleccionar el archivo de metadatos que descargó en el paso 4 y, después, haga clic en **Add Identity Provider** (Agregar proveedor de identidades) para cargar el archivo descargado. 
    
  ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)
11. Seleccione "**Yes**" (Sí) en la opción **Enabled** (Habilitado) de la página **Edit Federated Identity Provider Settings** (Editar configuración de proveedor de identidades federada) y haga clic en **Save** (Guardar).
    
   ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)
12. En la página **Edit Federated Identity Provider Settings** (Editar configuración de proveedor de identidades federada), realice las siguientes acciones y haga clic en **Save** (Guardar).   
  1. Seleccione **SAML NameID** en **Login name** (Nombre de inicio de sesión). 
  2. Seleccione **Other** (Otro) en **Full name** (Nombre completo) y en el cuadro de texto **Attribute name** (Nombre de atributo) escriba el valor: http://schemas.microsoft.com/identity/claims/displayname. 
  3. Seleccione **Other** (Otro) en **Full name** (Nombre completo) y en el cuadro de texto **Attribute name** (Nombre de atributo) escriba el valor: http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress. 
  4. Seleccione **Yes** (Sí) en **Auto-create account on signon** (Crear automáticamente cuenta al iniciar sesión). 
  5. Haga clic en el botón **Guardar** .
 
   ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)
13. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
    
    ![Inicio de sesión único de Azure AD ][10]
14. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
    
   ![Inicio de sesión único de Azure AD ][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_09.png)
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_05.png)
  1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
  2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
  3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_06.png)
  1. En el cuadro de texto **Nombre**, escriba **Britta**.  
  2. En el cuadro de texto **Apellidos**, escriba **Simon**.
  3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  4. En la lista **Rol**, seleccione **Usuario**.
  5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_07.png)
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_08.png)
  1. Anote el valor del campo **Nueva contraseña**.
  2. Haga clic en **Completo**.   

### <a name="create-a-moveit-transfer-test-user"></a>Creación de un usuario de prueba de MOVEit Transfer
El objetivo de esta sección es crear un usuario llamado Britta Simon en MOVEit Transfer. MOVEit Transfer admite el aprovisionamiento Just-In-Time que ha habilitado.

No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a MOVEit Transfer se creará un nuevo usuario, en caso de que no exista.

>[!NOTE]
>Si necesita crear un usuario manualmente, es preciso que se ponga en contacto con el equipo de soporte técnico de MOVEit Transfer.
> 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo cual se le concederá acceso a MOVEit Transfer.

![Asignar usuario][200]

**Para asignar Britta Simon a MOVEit Transfer, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **MOVEit Transfer**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_50.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de MOVEit Transfer en el panel de acceso, debería iniciar sesión automáticamente en la aplicación MOVEit Transfer.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_205.png

