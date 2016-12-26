---
title: "Tutorial: Integración de Azure Active Directory con Jive | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Jive."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 91f8a95bdab98f079b748b5391e9b611378c6e79
ms.openlocfilehash: 8e5af99c6c0a8ce4d1b239b3271846f39bc387be


---
# <a name="tutorial-azure-active-directory-integration-with-jive"></a>Tutorial: Integración de Azure Active Directory con Jive
En este tutorial, obtendrá información sobre cómo integrar Jive con Azure Active Directory (Azure AD).

Integrar Jive con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Jive.
* Puede permitir que los usuarios inicien sesión automáticamente en Jive (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Jive, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para inicio de sesión único en Jive

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Jive desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-jive-from-the-gallery"></a>Adición de Jive desde la galería
Para configurar la integración de Jive en Azure AD, deberá agregar Jive desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Jive desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Jive**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_01.png)
7. En el panel de resultados, seleccione **Jive** y luego haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Jive con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Jive para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Jive.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Jive.

Para configurar y probar el inicio de sesión único de Azure AD con Jive, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Jive](#creating-a-jive-test-user)** : para tener un homólogo de Britta Simon en Jive que esté vinculado a la representación de ella en Azure AD.
4. **[Configuración del aprovisionamiento de usuario](#configuring-user-provisioning)** : para describir cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory para Jive.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
6. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Jive.

**Para configurar el inicio de sesión único de Azure AD con Jive, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de aplicaciones de **Jive**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en Jive?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-jive-tutorial/tutorial_jive_03.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-jive-tutorial/tutorial_jive_04.png) 
   
    a. En el cuadro de texto **DirecciónURL de inicio de sesión**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en la aplicación de Jive con el siguiente patrón: **https://\<nombreDeCliente\>.jivecustom.com**.
   
    b. click **Siguiente**
4. En la página **Configurar inicio de sesión único en Jive** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-jive-tutorial/tutorial_jive_05.png)
   
    a. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
   
    b. Haga clic en **Next**.
5. Inicie la sesión en el inquilino de Jive como administrador.
6. En el menú de la parte superior, haga clic en "**Saml**".
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)
   
    a. Seleccione **Enabled** (Habilitado) en la pestaña **General** (General).
   
    b. Haga clic en el botón "**Guardar toda la configuración de saml**".
7. Vaya a la pestaña "**Metadatos de IdP**".
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)
   
    a. Copie el contenido del archivo XML de metadatos descargado y, después, péguelo en el cuadro de texto **Identity Provider (IDP) Metadata** (Metadatos del proveedor de identidades [IDP]).
   
    b. Haga clic en el botón "**Guardar toda la configuración de saml**". 
8. Vaya a la pestaña "**User Attribute Mapping**" (Asignación de atributos de usuario).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)
   
    a. En el cuadro de texto **Email** (Correo electrónico), copie y pegue el nombre del atributo de valor **mail**.
   
    b. En el cuadro de texto **First Name** (Nombre), copie y pegue el nombre del atributo de valor **givenname**.
   
    c. En el cuadro de texto **Last Name** (Apellidos), copie y pegue el nombre del atributo de valor **surname**.
9. En el Portal de Azure AD, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   ![Inicio de sesión único de Azure AD][10]
10. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
    ![Inicio de sesión único de Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png) 
5. En el cuadro de diálogo **Proporcione información sobre este usuario**, siga estos pasos:  ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_06.png) 
   
    a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
    b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
    d. En la lista **Rol**, seleccione **Usuario**.
   
    e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Complete**.   

### <a name="creating-a-jive-test-user"></a>Creación de un usuario de prueba de Jive
En esta sección, creará un usuario llamado Britta Simon en Jive. Trabaje con el equipo de soporte técnico de Jive para agregar los usuarios a la plataforma de Jive.

### <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory para Jive.  
Como parte de este procedimiento, es necesario proporcionar un token de seguridad de usuario que deberá solicitar a Jive.com.

La captura de pantalla siguiente muestra un ejemplo del cuadro de diálogo relacionado en Azure AD:

![Configurar aprovisionamiento de usuarios](./media/active-directory-saas-jive-tutorial/IC698794.png "Configure User Provisioning")

#### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. En el Portal de administración de Azure, en la página de integración de aplicaciones de **Jive**, haga clic en **Configurar aprovisionamiento de usuarios** para abrir el cuadro de diálogo de **Configurar aprovisionamiento de usuarios**.
2. En la página **Especifique sus credenciales de Jive para habilitar el aprovisionamiento automático de usuarios** , proporcione los valores de configuración siguientes:
   
    a. En el cuadro de texto **Nombre de usuario del administrador de Jive**, escriba un nombre de cuenta de Jive que tenga asignado el perfil **Administrador del sistema** en Jive.com.
   
    b. En el cuadro de texto **Contraseña de administrador de Jive** , escriba la contraseña para esta cuenta.
   
    c. En el cuadro de texto **Dirección URL de inquilino de Jive** , escriba la dirección URL del inquilino de Jive.
      
      > [!NOTE]
      > La dirección URL del inquilino de Jive es la que se usa en su organización para iniciar sesión en Jive.  
      > Normalmente, la dirección URL tiene el formato siguiente: **www.\<organización\>.jive.com**.
      > 
      > 
   
    d. Haga clic en **validar** para comprobar la configuración.

    e. Haga clic en el botón **Siguiente** para abrir la página **Confirmación**.

3. En la página **Confirmación** , haga clic en la marca de verificación para guardar la configuración.

Ahora ya puede crear una cuenta de prueba, espere 10 minutos y compruebe si la cuenta se ha sincronizado en Jive.com.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Jive.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Jive, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **Jive**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-jive-tutorial/tutorial_jive_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Jive en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Jive.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-jive-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-jive-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-jive-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jive-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jive-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


