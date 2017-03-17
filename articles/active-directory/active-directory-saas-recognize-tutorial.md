---
title: "Tutorial: integración de Azure Active Directory con Recognize | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Recognize."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a2ce8c3a6aad76ca491ca17beab8555ad77a1142
ms.openlocfilehash: 3bfb340639674406a0a983106b56ca4a993aaf14
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Tutorial: Integración de Azure Active Directory con Recognize
El objetivo de este tutorial es mostrar cómo integrar Recognize con Azure Active Directory (Azure AD).

Integrar Recognize con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Recognize
* Puede permitir que los usuarios inicien sesión automáticamente en Recognize (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Recognize, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en Recognize

 >[!NOTE]
 >Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
 > 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Recognize desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-recognize-from-the-gallery"></a>Adición de Recognize desde la galería
Para configurar la integración de Recognize en Azure AD, deberá agregar Recognize desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Recognize desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Recognize**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_01.png)
7. En el panel de resultados, seleccione **Recognize** y haga clic en **Completar** para agregar la aplicación.
   
    ![Selección de la aplicación en la galería](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Recognize con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Recognize para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Recognize.

Esta relación de vínculo se establece asignando el valor de **nombre de usuario** de Azure AD como el valor de **nombre de usuario** de Recognize.

Para configurar y probar el inicio de sesión único de Azure AD con Recognize, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Recognize](#creating-a-recognize-test-user)**: para tener un homólogo de Britta Simon en Recognize que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Recognize.

**Para configurar el inicio de sesión único de Azure AD con Recognize, realice los pasos siguientes:**

1. En el Portal clásico, en la página de integración de aplicaciones de **Recognize**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en Recognize?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_03.png)
3. En el cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_04.png)
  1. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://recognizeapp.com/<your-domain>/saml/sso`. 
  2. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://recognizeapp.com/<your-domain>/saml/metadata`.
  3. Haga clic en **Siguiente**.
  
    >[!NOTE]
    >Si no conoce estas direcciones URL, escriba las direcciones URL de ejemplo con el patrón de ejemplo. Para obtener estos valores, puede consultar al paso 9 o ponerse en contacto con el equipo de soporte técnico de Recognize en <mailto:support@recognizeapp.com>.
    >

4. En la página **Configuración de inicio de sesión único en Recognize**, haga clic en **Descargar certificado** y guarde el archivo de certificado en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_05.png)
5. En otra ventana del explorador web, inicie sesión en el inquilino de Recognize como administrador.
6. En la esquina superior derecha, haga clic en **Menú**. Vaya a **Company Admin** (Administrador de empresa).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)
7. En la barra de navegación de la izquierda, haga clic en **Settings**(Configuración).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)
8. Siga estos pasos en la sección **SSO Settings** (Configuración de SSO).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
 1. En **Enable SSO** (Habilitar SSO), seleccione **ON** (Activado).
 2. En el cuadro de texto **IDP Entity ID** (Id. de entidad de IDP), coloque el valor de **Issuer URL** (URL del emisor) del Asistente para configuración de aplicaciones de Azure AD. 
 3. En el cuadro de texto **SSO target URL** (Dirección URL de destino de SSO), coloque el valor de **Dirección URL del servicio de inicio de sesión único** en el Asistente para configuración de aplicaciones de Azure AD. 
 4. En el cuadro de texto **SSO target URL** (Dirección URL de destino de SSO), coloque el valor de **Dirección URL del servicio de cierre de sesión único** en el Asistente para configuración de aplicaciones de Azure AD. 
 5. Abra el archivo de certificado descargado en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado**.  
 6. Haga clic en el botón **Guardar configuración**. 
9. Junto a la sección de **configuración de SSO**, copie la dirección URL en **Service Provider Metadata url** (Dirección URL de metadatos del proveedor de servicio).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)
10. Abra el vínculo **Dirección URL de metadatos** en un explorador en blanco para descargar el documento de metadatos. Después, utilice el valor de EntityDescriptor que proporciona Recognize para **Identificador** en el cuadro de diálogo **Configurar las opciones de la aplicación**.
    
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)
11. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
    
    ![Inicio de sesión único de Azure AD ][10]
12. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
    
    ![Inicio de sesión único de Azure AD ][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_09.png)
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png)
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png)
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_05.png)
 1. En Tipo de usuario, seleccione Nuevo usuario de la organización.  
 2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**. 
 3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_06.png) 
 1. En el cuadro de texto **Nombre**, escriba **Britta**.   
 2. En el cuadro de texto **Apellidos**, escriba **Simon**. 
 3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**. 
 4. En la lista **Rol**, seleccione **Usuario**. 
 5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_07.png)
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_08.png) 
 1. Anote el valor del campo **Nueva contraseña**. 
 2. Haga clic en **Completo**.   

### <a name="create-a-recognize-test-user"></a>Creación de un usuario de prueba de Recognize
Para permitir que los usuarios de Azure AD inicien sesión en Recognize, deben aprovisionarse en Recognize. En el caso de Recognize, el aprovisionamiento es una tarea manual.

Esta aplicación no admite el aprovisionamiento SCIM, pero tiene una sincronización de usuario alternativa que aprovisiona a los usuarios. 

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su sitio de la compañía de Recognize como administrador.
2. En la esquina superior derecha, haga clic en **Menú**. Vaya a **Company Admin** (Administrador de empresa).
3. En la barra de navegación de la izquierda, haga clic en **Settings**(Configuración).
4. En la sección **User Sync** (Sincronización de usuario), siga estos pasos.
   
   ![Nuevo usuario](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "Nuevo usuario")   
 1. En **Sync Enabled** (Sincronización habilitada), seleccione **ON** (Activado). 
 2. En **Choose sync provider** (Elegir proveedor de sincronización), seleccione **Microsoft/Office 365**. 
 3. Haga clic en **Run User Sync** (Ejecutar sincronización de usuario).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Recognize.

![Asignar usuario][200]

**Para asignar a Britta Simon a Recognize, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **Recognize**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_50.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Recognize en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Recognize.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_205.png

