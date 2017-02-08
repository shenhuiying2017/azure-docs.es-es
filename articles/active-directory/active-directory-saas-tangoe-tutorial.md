---
title: "Tutorial: integración de Azure Active Directory con Tangoe Command Premium Mobile | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Tangoe Command Premium Mobile."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: ac82535515f346d43bbceeb274ce61d81ade60b1


---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Tutorial: Integración de Azure Active Directory con Tangoe Command Premium Mobile
En este tutorial, obtendrá información sobre cómo integrar Tangoe Command Premium Mobile con Azure Active Directory (Azure AD).

La integración de Tangoe Command Premium Mobile con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Tangoe Command Premium Mobile.
* Puede permitir que los usuarios inicien sesión automáticamente en Tangoe Command Premium Mobile (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Tangoe Command Premium Mobile, necesita los siguientes elementos:

* Una suscripción de Azure
* Una suscripción habilitada para el inicio de sesión único en Tangoe Command Premium Mobile

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

1. Incorporación de Tangoe Command Premium Mobile desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-tangoe-command-premium-mobile-from-the-gallery"></a>Incorporación de Tangoe Command Premium Mobile desde la galería
Para configurar la integración de Tangoe Command Premium Mobile en Azure AD, deberá agregar Tangoe Command Premium Mobile desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Tangoe Command Premium Mobile desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Tangoe Command Premium Mobile**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_01.png)
7. En el panel de resultados, seleccione **Tangoe Command Premium Mobile** y, después, haga clic en **Completar** para agregar la aplicación.

![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Tangoe Command Premium Mobile con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Tangoe Command Premium Mobile para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Tangoe Command Premium Mobile.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en Tangoe Command Premium Mobile.

Para configurar y probar el inicio de sesión único de Azure AD con Tangoe Command Premium Mobile, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Tangoe Command Premium Mobile](#creating-an-tangoe-test-user)** : para tener un homólogo de Britta Simon en Tangoe Command Premium Mobile que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Tangoe Command Premium Mobile.

**Para configurar el inicio de sesión único de Azure AD con Tangoe Command Premium Mobile, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de la aplicación de la aplicación **Tangoe Command Premium Mobile**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6]
2. En la página **¿Cómo desea que los usuarios inicien sesión en Tangoe Command Premium Mobile?**, seleccione **Inicio de sesión único de Azure AD** y, después, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_03.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_04.png) 

    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en la aplicación Tangoe Command Premium Mobile con el siguiente patrón: “**https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=\<emisor de inquilino\>&Target=\<URL de página de destino\>”**.

    b. En el cuadro de texto **URL de respuesta**, escriba la dirección URL con el siguiente patrón: **"https://sso.tangoe.com/sp/ACS.saml2"**

    > [AZURE.NOTE]  Si no conoce los valores correctos de las direcciones URL, puede usar los valores indicados anteriormente como marcadores de posición y solicitar los valores correctos al servicio de asistencia al cliente de Tangoe.


1. En la página **Configurar inicio de sesión único en Tangoe Command Premium Mobile** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_05.png) 
   
    a. Haga clic en **Descargar metadatos**y luego guarde el archivo en el equipo.
   
    b. Haga clic en **Siguiente**.
2. Para configurar el inicio de sesión único para su aplicación, póngase en contacto con el servicio de asistencia al cliente de Tangoe y proporcione lo siguiente:

    - El archivo de metadatos descargado
    - La **URL del emisor**
    - La **dirección URL de SSO de SAML**
    - La **dirección URL del servicio de cierre de sesión único**



1. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
2. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

En la lista Usuarios, seleccione **Britta Simon**.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_04.png)
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_06.png) 
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-an-tangoe-command-premium-mobile-test-user"></a>Creación de un usuario de prueba de Tangoe Command Premium Mobile
En esta sección, creará un usuario llamado Britta Simon en Tangoe Command Premium Mobile. La aplicación Tangoe Command Premium Mobile necesita que todos los usuarios estén aprovisionados en la aplicación antes de realizar el inicio de sesión único. Así pues, colabore con el servicio de asistencia al cliente de Tangoe para aprovisionar todos estos usuarios en la aplicación. 

> [!NOTE]
> Si necesita crear un usuario de forma manual o por lotes de los usuarios, póngase en contacto con el equipo de soporte técnico de Tangoe Command Premium Mobile.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Tangoe Command Premium Mobile.

![Asignar usuario][200] 

**Para asignar Britta Simon a Tangoe Command Premium Mobile, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **Tangoe comando Premium Mobile**.

![Configurar inicio de sesión único](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_50.png) 

1. En el menú de la parte superior, haga clic en **Usuarios**.

![Asignar usuario][203] 

1. En la lista Usuarios, seleccione **Britta Simon**.
2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Tangoe Command Premium Mobile en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Tangoe Command Premium Mobile.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


