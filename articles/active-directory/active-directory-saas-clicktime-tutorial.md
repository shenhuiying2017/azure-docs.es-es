---
title: "Tutorial: integración de Azure Active Directory con ClickTime | Microsoft Docs"
description: "Aprenda a usar ClickTime con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0837cb33bf438fb7fd9665d21d411f0170cdd393
ms.openlocfilehash: d5171cdc9048837385bfb99d553e496a9f56846e
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Tutorial: integración de Azure Active Directory con ClickTime
En este tutorial, aprenderá a integrar ClickTime con Azure Active Directory (Azure AD).

La integración de ClickTime con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a ClickTime.
* Puede habilitar que los usuarios inicien sesión automáticamente en ClickTime (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con ClickTime, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en ClickTime

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

1. Agregar ClickTime desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-clicktime-from-the-gallery"></a>Agregar ClickTime desde la galería
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para ClickTime.

### <a name="to-enable-the-application-integration-for-clicktime-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para ClickTime:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-clicktime-tutorial/tic700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-clicktime-tutorial/tic700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-clicktime-tutorial/tic749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-clicktime-tutorial/tic749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **ClickTime**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-clicktime-tutorial/tic777275.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **ClickTime** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![ClickTime](./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con ClickTime con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de ClickTime para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ClickTime.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como valor de **Username** (Nombre de usuario) en ClickTime.

Para configurar y probar el inicio de sesión único de Azure AD con ADP ClickTime, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de ClickTime](#creating-a-clicktime-test-user)**: para tener un homólogo de Britta Simon en ClickTime que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en ClickTime con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  

> [!IMPORTANT]
> Para poder configurar el inicio de sesión único en el inquilino de ClickTime, deberá ponerse en contacto primero con el soporte técnico de ClickTime para habilitar esta característica.
> 
> 

**Para configurar el inicio de sesión único de Azure AD con ClickTime, siga estos pasos:**

1. En el Portal de Azure clásico, en la página de integración de la aplicación **ClickTime**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Habilitar inicio de sesión único](./media/active-directory-saas-clicktime-tutorial/tic777277.png "Habilitar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en ClickTime?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-clicktime-tutorial/tic777278.png "Configurar inicio de sesión único")
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-clicktime-tutorial/tic777286.png) 
   
    a. En el cuadro de texto **Identificador**, escriba la dirección URL con el siguiente patrón: **https://app.clicktime.com/sp/**
   
    b. En el cuadro de texto **URL de respuesta**, escriba la dirección URL con el siguiente patrón: **https://app.clicktime.com/Login/**
   
    c. click **Siguiente**
4. En la página **Configuración de inicio de sesión único en ClickTime**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-clicktime-tutorial/tic777279.png "Configurar inicio de sesión único")
5. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de ClickTime como administrador.
6. En la barra de herramientas de la parte superior, haga clic en**Preferencias** y luego haga clic en **Configuración de seguridad**.
7. En la sección de configuración **Preferencias de inicio de sesión único** , siga estos pasos:
   
   ![Configuración de seguridad](./media/active-directory-saas-clicktime-tutorial/tic777280.png "Configuración de seguridad")
   
   a.  Seleccione **Allow** sign-in using Single Sign-On (SSO) with **Azure AD** [Permitir inicio de sesión mediante inicio de sesión único (SSO) con Azure AD].
   
   b.  En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en ClickTime**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Identity Provider Endpoint** (Punto de conexión de proveedor de identidades).
   
   c.  Abra el certificado codificado en base&64; en el **Bloc de notas**, copie el contenido y, a continuación, péguelo en el cuadro de texto **X.509 Certificate** (Certificado X.509).
   
   d.  Haga clic en **Save**.
8. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-clicktime-tutorial/tic777281.png "Configurar inicio de sesión único")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en ClickTime, deben aprovisionarse en ClickTime.  
En el caso de ClickTime, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en su inquilino de **ClickTime** .
2. En la barra de herramientas de la parte superior, haga clic en **Compañía** y, luego, en **Contactos**.
   
   ![Personas](./media/active-directory-saas-clicktime-tutorial/tic777282.png "Personas")
3. Haga clic en **Add Person**(Agregar persona).
   
   ![Agregar persona](./media/active-directory-saas-clicktime-tutorial/tic777283.png "Agregar persona")
4. En la sección New Person (Nueva persona), lleve a cabo estos pasos:
   
   ![Personas](./media/active-directory-saas-clicktime-tutorial/tic777284.png "Personas")
   
   a.  En el cuadro de texto **dirección de correo electrónico** , escriba la dirección de correo electrónico de la cuenta de Azure AD.
   
   b.  En el cuadro de texto **nombre completo** , escriba el nombre de la cuenta de Azure AD.  
   
   > [!NOTE]
   > Si lo desea, puede establecer propiedades adicionales del nuevo objeto persona.
   > 
   > 
   
   c.  Haga clic en **Save**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de ClickTime ofrecida por ClickTime para aprovisionar cuentas de usuario de Azure AD.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a ClickTime.

![Asignar usuario][200]

Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar a Britta Simon a ClickTime, siga estos pasos**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **ClickTime**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

## <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de ClickTime en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación ClickTime.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png

