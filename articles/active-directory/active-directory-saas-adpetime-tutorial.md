---
title: "Tutorial: integración de Azure Active Directory con ADP eTime | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y ADP eTime."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a3e9f0be-19ed-4b99-a180-619e7624fc26
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 84c23a43b87c7357ed99f33c275717c68cf7c8bb
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adp-etime"></a>Tutorial: Integración de Azure Active Directory con ADP eTime
El objetivo de este tutorial es mostrar cómo integrar ADP eTime con Azure Active Directory (Azure AD).

La integración de ADP eTime con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a ADP eTime.
* Puede permitir que los usuarios inicien sesión automáticamente en ADP eTime (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con ADP eTime, se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en ADP eTime

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de ADP eTime desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-adp-etime-from-the-gallery"></a>Adición de ADP eTime desde la galería
Para configurar la integración de ADP eTime en Azure AD, es preciso agregar ADP eTime desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar ADP eTime desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **ADP eTime**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_01.png)
7. En el panel de resultados, seleccione **ADP eTime** y haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_06.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con ADP eTime con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de ADP eTime para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ADP eTime.  

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en ADP eTime.

Para configurar y probar el inicio de sesión único de Azure AD con ADP eTime , es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de ADP eTime](#creating-a-adpetime-test-user)** : para tener un homólogo de Britta Simon en ADP eTime que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación ADP eTime.

La aplicación ADP eTime espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla le muestra un ejemplo de esto. El nombre de la notificación siempre será **"PersonImmutableID"** cuyo valor hemos asignado a ExtensionAttribute2 que contiene el EmployeeID del usuario. 

Aquí se realizará la asignación de usuario desde Azure AD a ADP eTime en el valor EmployeeID pero puede asignarlo a un valor diferente que también se base en la configuración de la aplicación. Así pues, trabaje con el equipo de ADP eTime primero para usar el identificador correcto de un usuario y asigne ese valor a la notificación **"PersonImmutableID"** .  

![Configurar inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_02.png) 

Antes de configurar la aserción SAML, debe ponerse en contacto con el equipo de soporte técnico de ADP eTime y solicitar el valor del atributo de identificador único para el inquilino. Necesitará este valor para configurar la notificación personalizada para su aplicación.

**Para configurar el inicio de sesión único de Azure AD con ADP eTime, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **ADP eTime**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en ADP eTime?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_03.png) 
3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_04.png) 
  1. En el cuadro de texto **URL de respuesta**, escriba la dirección URL que usan los usuarios para iniciar sesión en la aplicación de ADP eTime con el siguiente patrón: `https://<server name>.adp.com/affwebservices/public/saml2assertionconsumer`.
  2. Haga clic en **Siguiente**.
4. En la página **Configurar inicio de sesión único en ADP eTime** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_05.png)  
  1. Haga clic en **Descargar metadatos**y luego guarde el archivo en el equipo. 
  2. Haga clic en **Siguiente**.
2. Para configurar el inicio de sesión único para la aplicación, póngase en contacto con el equipo de soporte técnico de ADP eTime y envíe el archivo de metadatos descargado adjunto para que puedan configurarse para la integración con el inicio de sesión único.
   
   >[!NOTE]
   >Una vez que el equipo de **ADP eTime** configure la instancia, obtenga el valor de **RelayState** de ellos. Siga los pasos mencionados a continuación para configurarlo. Después de la configuración, puede probar la integración. Tenga en cuenta que se trata de una configuración importante para que esta integración de la aplicación funcione.
   >  
6. Para configurar el valor de RelayState en Azure AD, siga estos pasos: 
  1. Inicie sesión en el [Portal de administración de Azure](https://portal.azure.com) como administrador.
  2. En el panel de navegación izquierdo, haga clic en **Más servicios**.  

    ![Configurar inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_07.png) 
 3. En el cuadro de texto **Buscar**, escriba **Azure Active Directory** y haga clic en el vínculo relacionado. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_08.png)   
 4. Haga clic en **Aplicaciones empresariales**. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_09.png)  
 5. En la sección **Administrar**, haga clic en **Todas las aplicaciones**.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_10.png) 
 6. En el cuadro de texto **Buscar**, escriba **ADP eTime** y haga clic en el vínculo relacionado. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_11.png)  
 7. En la sección **Administrar**, haga clic en **Inicio de sesión único**. 
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_12.png) 
 8. Seleccione **Mostrar configuración avanzada de URL**.
 
     ![Configurar inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_13.png) 
 9. En el cuadro de texto **Estado de la retransmisión**, escriba un valor con los patrones siguientes y luego guarde la configuración: 
 
    * Entorno de producción: `https://fed.adp.com/saml/fedlanding.html?<id>` 
    * Entorno de ensayo: `https://fed-stag.adp.com/saml/fedlanding.html?PORTAL`
     
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_14.png) 

4. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
 
 ![Inicio de sesión único de Azure AD ][10]
5. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
 ![Inicio de sesión único de Azure AD ][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.  

* En la lista Usuarios, seleccione **Britta Simon**.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_05.png)  
 1. En **Tipo de usuario**, seleccione **Nuevo usuario de la organización**.
 2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**. 
 3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_06.png)  
 1. En el cuadro de texto **Nombre**, escriba **Britta**.   
 2. En el cuadro de texto **Apellidos**, escriba **Simon**. 
 3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**. 
 4. En la lista **Rol**, seleccione **Usuario**. 
 5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_08.png)  
 1. Anote el valor del campo **Nueva contraseña**.
 2. Haga clic en **Completo**.   

### <a name="create-a-adp-etime-test-user"></a>Creación de un usuario de prueba de ADP eTime
El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en ADP eTime. Trabaje con el equipo de soporte técnico de ADP eTime para agregar usuarios a la cuenta de ADP eTime. 

>[!NOTE]
>Si necesita crear manualmente un usuario, es preciso que se ponga contacto con el equipo de soporte técnico de ADP eTime.
> 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo cual se le concederá acceso a ADP eTime.

![Asignar usuario][200] 

**Para asignar a Britta Simon a ADP eTime, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **ADP eTime**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de ADP eTime en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación ADP eTime.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_205.png

