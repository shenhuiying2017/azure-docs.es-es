---
title: "Tutorial: integración de Azure Active Directory con Optimizely | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Optimizely."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: e44e1621632bf2c8a3c4050b718d2721f5132d06
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Tutorial: integración de Azure Active Directory con Optimizely

En este tutorial, obtendrá información sobre cómo integrar Optimizely con Azure Active Directory (Azure AD).

La integración de Optimizely con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Optimizely.
- Puede permitir que los usuarios inicien sesión automáticamente en Optimizely (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Optimizely, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Optimizely

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Optimizely desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-optimizely-from-the-gallery"></a>Adición de Optimizely desde la galería
Para configurar la integración de Optimizely en Azure AD, es preciso agregar Optimizely desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Optimizely desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Optimizely**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_search.png)

5. En el panel de resultados, seleccione **Optimizely** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Optimizely con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Optimizely para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Optimizely.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en Optimizely.

Para configurar y probar el inicio de sesión único de Azure AD con Optimizely, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Optimizely](#creating-an-optimizely-test-user)**: el objetivo es tener un homólogo de Britta Simon en Optimizely que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Optimizely.

**Para configurar el inicio de sesión único de Azure AD con Optimizely, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Optimizely** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_samlbase.png)

3. En la sección **Dominio y direcciones URL de Optimizely**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://app.optimizely.net/<instance name>`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `urn:auth0:optimizely:contoso`

    > [!NOTE] 
    > Estos valores no son reales. El valor se actualizará con la dirección URL de inicio de sesión y el identificador reales, que se explican más adelante en el tutorial. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-optimizely-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Optimizely**, haga clic en **Configurar Optimizely** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_configure.png) 

7. Para configurar el inicio de sesión único en **Optimizely**, póngase en contacto con el administrador de su cuenta de Optimizely y proporcione el **certificado (Base64)** descargado y la **dirección URL del servicio de inicio de sesión único de SAML**. 

8. En respuesta a su correo electrónico, Optimizely proporciona la URL de inicio de sesión (SSO iniciado por el proveedor de servicios) y los valores de Identificador (Id. de entidad del proveedor de servicios).

    a. Copie la **URL de inicio de sesión único iniciado por SP** que proporciona Optimizely y péguela en el cuadro de texto **URL de inicio de sesión** en la sección **Dominio y direcciones URL de Optimizely** de Azure Portal. 

    b. Copie el **identificador de entidad del proveedor de servicios** que proporciona Optimizely y péguelo en el cuadro de texto **Identificador** de la sección **Dominio y direcciones URL de Optimizely** de Azure Portal. 

9. En una ventana de explorador diferente, inicie sesión en su aplicación de Optimizely como administrador.

10. Haga clic en el nombre de la cuenta de la parte superior derecha y, después, en **Configuración de la cuenta**.
   
    ![Inicio de sesión único de Azure AD ](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

11. En la pestaña Cuenta, active la casilla **Habilitar SSO** que se encuentra debajo de Inicio de sesión único en la sección **Información general**.
   
    ![Inicio de sesión único de Azure AD ](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)
    
12. Haga clic en **Guardar**

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-an-optimizely-test-user"></a>Creación de un usuario de prueba de Optimizely

En esta sección, creará un usuario llamado Britta Simon en Optimizely.

1. En la página principal, seleccione la pestaña **Colaboradores**.

2. Para agregar un nuevo colaborador, haga clic en **New Collaborator** (Nuevo colaborador).
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3. Escriba la dirección de correo electrónico y asígnele un rol. Haga clic en **Invitar**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. El usuario recibirá una invitación por correo electrónico, con la que debe iniciar sesión en Optimizely.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Optimizely.

![Asignar usuario][200] 

**Para asignar Britta Simon a Optimizely, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Optimizely**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Optimizely en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Optimizely. 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png

