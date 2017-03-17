---
title: "Tutorial: integración de Azure Active Directory con ADP GlobalView | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y ADP GlobalView."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0f767b96fa8f8f9d1a22709633bee64f0477ffb3
ms.openlocfilehash: 46fbe3233af22605d4cd89227b91313128e523f9
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adp-globalview"></a>Tutorial: Integración de Azure Active Directory con ADP GlobalView
El objetivo de este tutorial es mostrar cómo integrar ADP GlobalView con Azure Active Directory (Azure AD).  

La integración de ADP GlobalView con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a ADP GlobalView.
* Puede permitir que los usuarios inicien sesión automáticamente en ADP GlobalView (inicio de sesión único [SSO]) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con ADP GlobalView, se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para SSO en ADP GlobalView

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.  

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de ADP GlobalView desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-adp-globalview-from-the-gallery"></a>Adición de ADP GlobalView desde la galería
Para configurar la integración de ADP GlobalView en Azure AD, es preciso agregar ADP GlobalView desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar ADP GlobalView desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **ADP GlobalView**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_01.png)
7. En el panel de resultados, seleccione **ADP GlobalView** y haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_06.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único (SSO) de Azure AD con ADP GlobalView con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de ADP GlobalView para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ADP GlobalView.  

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en ADP GlobalView.

Para configurar y probar el inicio de sesión único de Azure AD con ADP GlobalView, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de ADP GlobalView](#creating-a-adp-globalview-test-user)** : para tener un homólogo de Britta Simon en ADP GlobalView que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurarlo en la aplicación ADP GlobalView.

La aplicación ADP GlobalView espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. 

La siguiente captura de pantalla le muestra un ejemplo de esto. El nombre de la notificación siempre será **"PersonImmutableID"** cuyo valor hemos asignado a ExtensionAttribute2 que contiene el EmployeeID del usuario. Aquí se realizará la asignación de usuario desde Azure AD a ADP GlobalView en el valor EmployeeID, pero puede asignarlo a un valor diferente que también se base en la configuración de la aplicación. 

Puede trabajar primero con el equipo de ADP GlobalView para usar el identificador correcto de un usuario y asignar ese valor con la notificación **"PersonImmutableID"**. También puede asignar la notificación Email y UserID como se muestra en la imagen.

![Configurar inicio de sesión único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_02.png) 

Antes de configurar la aserción SAML, debe ponerse en contacto con el equipo de soporte técnico de ADP GlobalView y solicitar el valor del atributo de identificador único para el inquilino. Necesitará este valor para configurar la notificación personalizada para su aplicación.

**Para configurar el inicio de sesión único de Azure AD con ADP GlobalView, siga estos pasos:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **ADP GlobalView**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en ADP GlobalView?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_03.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_04.png) 
  1. En el cuadro de texto **Identificador**, escriba la dirección URL usada para identificar la aplicación ADP GlobalView mediante uno de los siguientes patrones: `https://<server name>.globalview.adp.com/federate2` o `https://<server name>.globalview.adp.com/federate`.
  2. En el cuadro de texto **URL de respuesta**, escriba la dirección URL usada por Azure AD para enviar la respuesta a la aplicación ADP GlobalView, mediante uno de los siguientes patrones: `https://<server name>.globalview.adp.com/federate2/sp/ACS.saml2` o `https://<server name>.globalview.adp.com/federate/sp/ACS.saml2`.
  3. Haga clic en **Siguiente**.
4. En la página **Configurar inicio de sesión único en ADP GlobalView** , siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_05.png)   
  1. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
  2. Haga clic en **Siguiente**.
5. Para configurar el inicio de sesión único para su aplicación, póngase en contacto con el equipo de soporte técnico de ADP GlobalView y proporcione lo siguiente: 
   
   * El **certificado** descargado
   * El **identificador de entidad**
   * La **dirección URL de inicio de sesión único de SAML**
   * La **dirección URL del servicio de cierre de sesión único**

    >[!NOTE]
    >Una vez que el equipo de **ADP GlobalView** configure la instancia, pídales el valor de **RelayState**. Siga los pasos a continuación para configurarlo. Después de la configuración, puede probar la integración. Tenga en cuenta que se trata de una configuración importante para que esta integración de la aplicación funcione.
    >

6. Para configurar el valor de RelayState en Azure AD, siga estos pasos:   
 1. Inicie sesión en el [Portal de administración de Azure](https://portal.azure.com) como administrador.
 2. En el panel de navegación izquierdo, haga clic en **Más servicios**. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_07.png)  
 3. En el cuadro de texto **Buscar**, escriba **Azure Active Directory** y haga clic en el vínculo relacionado.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_08.png)  
 4. Haga clic en **Aplicaciones empresariales**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_09.png) 
 5. En la sección **Administrar**, haga clic en **Todas las aplicaciones**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_10.png) 
 6. En el cuadro de texto **Buscar**, escriba **ADP eTime** y haga clic en el vínculo relacionado. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_11.png) 
 7. En la sección **Administrar**, haga clic en **Inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_12.png)  
 8. Seleccione **Mostrar configuración avanzada de URL**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_13.png)
 9. En el cuadro de texto **Estado de la retransmisión**, escriba un valor con los patrones siguientes:
   
    `https://<server name>.globalview.adp.com/gvolution/session/<instance name>/sso` 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_14.png)  
 10. Guarde la configuración
7. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
8. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.  

* En la lista Usuarios, seleccione **Britta Simon**.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_05.png)  
 1. En Tipo de usuario, seleccione Nuevo usuario de la organización.  
 2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**. 
 3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_06.png)  
 1. En el cuadro de texto **Nombre**, escriba **Britta**.    
 2. En el cuadro de texto **Apellidos**, escriba **Simon**. 
 3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**. 
 4. En la lista **Rol**, seleccione **Usuario**. 
 5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_08.png)  
 1. Anote el valor del campo **Nueva contraseña**.  
 2. Haga clic en **Completo**.   

### <a name="create-a-adp-globalview-test-user"></a>Creación de un usuario de prueba de ADP GlobalView
El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en ADP GlobalView. Trabaje con el equipo de soporte técnico de ADP GlobalView para agregar usuarios a la cuenta de ADP GlobalView. 

>[!NOTE]
>Si necesita crear manualmente un usuario, es preciso que se ponga contacto con el equipo de soporte técnico de ADP GlobalView.
> 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo cual se le concederá acceso a ADP GlobalView.

![Asignar usuario][200] 

**Para asignar a Britta Simon a ADP GlobalView, siga estos pasos:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **ADP GlobalView**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  

Al hacer clic en el icono de ADP GlobalView en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación ADP GlobalView.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_205.png

