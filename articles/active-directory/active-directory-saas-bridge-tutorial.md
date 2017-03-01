---
title: "Tutorial: Integración de Azure Active Directory con Bridge | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Bridge."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 3dbb6499-80c1-4d00-a0b4-e0ad5522cf0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1b01c8a16c217350e82db1bc7a6cc54b84888b9e
ms.openlocfilehash: 17898ff5a2a98383a9956346d7932c31587386a7
ms.lasthandoff: 12/13/2016


---
# <a name="tutorial-azure-active-directory-integration-with-bridge"></a>Tutorial: Integración de Azure Active Directory con Bridge

En este tutorial, obtendrá información sobre cómo integrar Bridge con Azure Active Directory (Azure AD).

La integración de Bridge con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Bridge.
- Puede permitir que los usuarios inicien sesión automáticamente en Bridge (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Bridge, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Bridge


> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Bridge desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-bridge-from-the-gallery"></a>Adición de Bridge desde la galería
Para configurar la integración de Bridge en Azure AD, es preciso agregar Bridge desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Bridge desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.

    ![Applications][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Bridge**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bridge-tutorial/tutorial_bridge_001.png)

7. En el panel de resultados, seleccione **Bridge** y haga clic en **Completar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bridge-tutorial/tutorial_bridge_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Bridge con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Bridge para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Bridge.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Bridge.

Para configurar y probar el inicio de sesión único de Azure AD con Bridge, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba para Bridge](#creating-a-bridge-test-user)**: para tener un homólogo de Britta Simon en Bridge que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Bridge.


**Para configurar el inicio de sesión único de Azure AD con Bridge, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de aplicaciones de **Bridge**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único][6]

2. En la página **¿Cómo desea que los usuarios inicien sesión en Bridge?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-bridge-tutorial/tutorial_bridge_02.png)

3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-bridge-tutorial/tutorial_bridge_03.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<company name>.bridgeapp.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<company name>.bridgeapp.com`

    c. Haga clic en **Siguiente**.

    > [!NOTE] 
    > Tenga en cuenta que estos no son valores reales. Tendrá que actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Puede presentar la incidencia de soporte técnico con Bridge <a href="https://bridgeapp.zendesk.com/hc/en-us/requests/new">aquí</a> para obtener estos valores.

4. En la página **Configuración de inicio de sesión único en Bridge**, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo:

    ![Configurar inicio de sesión único](./media/active-directory-saas-bridge-tutorial/tutorial_bridge_04.png) 

5. Para configurar el inicio de sesión único para la aplicación, puede presentar la incidencia de soporte técnico al equipo de soporte técnico con Bridge <a href="https://bridgeapp.zendesk.com/hc/en-us/requests/new">aquí</a> y proporcionar lo siguiente: 

    • El **archivo de certificado** descargado

    • El **identificador de entidad**

    • La **dirección URL de servicio de inicio de sesión único**

    La **dirección URL del servicio de cierre de sesión único**

6. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.

    ![Inicio de sesión único de Azure AD ][10]

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
  
    ![Inicio de sesión único de Azure AD ][11]


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bridge-tutorial/create_aaduser_09.png) 

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bridge-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bridge-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bridge-tutorial/create_aaduser_05.png) 

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bridge-tutorial/create_aaduser_06.png) 

    a. En el cuadro de texto **Nombre**, escriba **Britta**.  

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bridge-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bridge-tutorial/create_aaduser_08.png) 

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.   



### <a name="creating-a-bridge-test-user"></a>Creación de un usuario de prueba para Bridge

En esta sección, creará un usuario llamado Britta Simon en Bridge. Trabaje con el equipo de soporte técnico de Bridge para crear un usuario en la plataforma. Puede presentar la incidencia de soporte técnico con Bridge <a href="https://bridgeapp.zendesk.com/hc/en-us/requests/new">aquí</a> para agregar los usuarios en la plataforma Bridge.


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Bridge.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Bridge, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Bridge**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bridge-tutorial/tutorial_bridge_50.png) 

3. En el menú de la parte superior, haga clic en **Usuarios**.

    ![Asignar usuario][203] 

4. En la lista Usuarios, seleccione **Britta Simon**.

5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
    
    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Bridge en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de Bridge.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-bridge-tutorial/tutorial_general_205.png

