---
title: "Tutorial: integración de Azure Active Directory con LogicMonitor | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y LogicMonitor."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: d3ef333fcb0c517c44d2d5d83c7f7a51872f1490
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Tutorial: Integración de Azure Active Directory con LogicMonitor

En este tutorial, aprenderá a integrar LogicMonitor con Azure Active Directory (Azure AD).

La integración de LogicMonitor con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a LogicMonitor.
- Puede permitir que los usuarios inicien sesión automáticamente en LogicMonitor (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con LogicMonitor, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en LogicMonitor

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de LogicMonitor desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-logicmonitor-from-the-gallery"></a>Adición de LogicMonitor desde la galería
Para configurar la integración de LogicMonitor en Azure AD, deberá agregar LogicMonitor desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar LogicMonitor desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **LogicMonitor**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_search.png)

5. En el panel de resultados, seleccione **LogicMonitor** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con LogicMonitor con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de LogicMonitor para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de LogicMonitor.

Para establecer la relación de vínculo, en LogicMonitor, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con LogicMonitor, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de LogicMonitor](#creating-a-logicmonitor-test-user)**: el objetivo es tener un homólogo de Britta Simon en LogicMonitor que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación LogicMonitor.

**Para configurar el inicio de sesión único de Azure AD con LogicMonitor, realice los pasos siguientes:**

1. En la página de integración de la aplicación **LogicMonitor** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_samlbase.png)

3. En la sección **Dominio y direcciones URL de LogicMonitor**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.logicmonitor.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.logicmonitor.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de LogicMonitor](https://www.logicmonitor.com/contact/) para obtener estos valores. 
 


4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_400.png)

6. Inicie sesión como administrador en el sitio de la compañía de **LogicMonitor** .

7. En el menú de la parte superior, haga clic en **Configuración**.
   
   ![Configuración](./media/active-directory-saas-logicmonitor-tutorial/ic790052.png "Configuración")

8. En la barra de navegación del lado izquierdo, haga clic en **Inicio de sesión único**
   
   ![Inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/ic790053.png "Inicio de sesión único")

9. En la sección **Configuración del inicio de sesión único** , siga estos pasos:
   
   ![Configuración de inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/ic790054.png "Configuración de inicio de sesión único")
   
   a. Seleccione **Habilitar inicio de sesión único**.

   b. En **Default Role Assignment** (Asignación de rol predeterminado), seleccione **readonly**.
   
   c. Abra el archivo de metadatos descargado en el Bloc de notas y luego pegue el contenido del archivo en el cuadro de texto **Metadatos del proveedor de identidades** .
   
   d. Haga clic en **Guardar cambios**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-logicmonitor-test-user"></a>Creación de un usuario de prueba de LogicMonitor

Para que los usuarios de AAD puedan inician sesión, deben aprovisionarse para la aplicación LogicMonitor con sus nombres de usuario de Azure Active Directory.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. Inicie sesión como administrador en el sitio de la compañía de LogicMonitor.

2. En el menú de la parte superior, haga clic en **Settings** (Configuración) y, luego, en **Roles and Users** (Roles y usuarios).
   
   ![Roles y usuarios](./media/active-directory-saas-logicmonitor-tutorial/ic790056.png "Roles y usuarios")

3. Haga clic en **Agregar**.

4. En la sección **Agregar una cuenta** , realice estos pasos:
   
   ![Agregar una cuenta](./media/active-directory-saas-logicmonitor-tutorial/ic790057.png "Agregar una cuenta")
   
   a. En los cuadros de texto correspondientes, escriba los valores de **Nombre de usuario**, **Correo electrónico**, **Contraseña** y **Vuelva a escribir contraseña** del usuario de Azure Active Directory que quiera aprovisionar.
   
   b. Seleccione **Roles**, **Ver permisos** y **Estado**.
   
   c. Haga clic en **Enviar**.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de LogicMonitor que proporcione LogicMonitor para aprovisionar cuentas de usuario de Azure Active Directory. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a LogicMonitor.

![Asignar usuario][200] 

**Para asignar a Britta Simon a LogicMonitor, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **LogicMonitor**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.
 
Al hacer clic en el icono de LogicMonitor en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación LogicMonitor.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_203.png

