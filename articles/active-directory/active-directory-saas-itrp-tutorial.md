---
title: "Tutorial: Integración de Azure Active Directory con ITRP | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory e ITRP."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: aacfdbf9d644017c242feec90c97cd34cbd713e5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Tutorial: Integración de Azure Active Directory con ITRP

En este tutorial se aprende a integrar ITRP con Azure Active Directory (Azure AD).

La integración de ITRP con Azure AD le ofrece las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a ITRP.
- Puede permitir que los usuarios inicien sesión automáticamente en ITRP (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con ITRP, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en ITRP

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de ITRP desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-itrp-from-the-gallery"></a>Incorporación de ITRP desde la galería
Para configurar la integración de ITRP en Azure AD, tiene que agregar ITRP desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar ITRP desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **ITRP**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_search.png)

5. En el panel de resultados, seleccione **ITRP** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD

En esta sección se configura y prueba el inicio de sesión único de Azure AD con ITRP con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de ITRP para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ITRP.

Para establecer la relación de vínculo, en ITRP, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con ITRP, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de ITRP](#creating-an-itrp-test-user)**: para tener un homólogo de Britta Simon en ITRP vinculado a la representación de usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección se habilita el inicio de sesión único de Azure AD en Azure Portal y se configura el inicio de sesión único en la aplicación ITRP.

**Para configurar el inicio de sesión único de Azure AD con ITRP, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **ITRP**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_samlbase.png)

3. En la sección **Dominio y direcciones URL de ITRP**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.itrp.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.itrp.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Contacte con el [equipo de soporte técnico de cliente de ITRP](https://www.itrp.com/support) para obtener estos valores. 
 
4. En la sección **Certificado de firma de SAML**, copie el valor de **HUELLA DIGITAL** del certificado.

    ![Configurar inicio de sesión único](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-itrp-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de ITRP**, haga clic en **Configurar ITRP** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL del servicio de inicio de sesión único de SAML y la URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_configure.png) 

7. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de ITRP.

8. En la barra de herramientas de la parte superior, haga clic en el icono de **Configuración**.
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/ic775570.png "ITRP")

8. En el panel de navegación izquierdo, haga clic en **Inicio de sesión único**.
   
    ![Inicio de sesión único](./media/active-directory-saas-itrp-tutorial/ic775571.png "Inicio de sesión único")

9. Siga estos pasos en la sección de configuración de Single Sign-On (Inicio de sesión único):
   
    ![Inicio de sesión único](./media/active-directory-saas-itrp-tutorial/ic775572.png "Inicio de sesión único")
    
    ![Inicio de sesión único](./media/active-directory-saas-itrp-tutorial/ic775573.png "Inicio de sesión único")   

    a. Hacer clic en **Habilitar**.

    b. En el cuadro de texto **Remote Log Out URL** (Dirección URL de cierre de sesión remoto), pegue el valor de **dirección URL de cierre de sesión** que copió de Azure Portal.

    c. En el cuadro de texto **SAML SSO URL** (Dirección URL de inicio de sesión único de SAML), pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    En el cuadro de texto **Certificate Fingerprint** (Huella digital de certificado), pegue el valor de **Huella digital** del certificado que haya copiado de Azure Portal. 
      
10. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-itrp-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-itrp-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-itrp-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-itrp-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-an-itrp-test-user"></a>Creación de un usuario de prueba de ITRP

Para permitir que los usuarios de Azure AD inicien sesión en ITRP, tienen que aprovisionarse en ITRP.  

En el caso de ITRP, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **ITRP** .

2. En la barra de herramientas de la parte superior, haga clic en el icono de **Registros**.
   
    ![Administración](./media/active-directory-saas-itrp-tutorial/ic775575.png "Administración")

3. En el menú emergente, seleccione **Contactos**.
   
    ![Personas](./media/active-directory-saas-itrp-tutorial/ic775587.png "Personas")

4. Haga clic en **Agregar nueva persona** (“+”).
   
    ![Administración](./media/active-directory-saas-itrp-tutorial/ic775576.png "Administración")

5. En el cuadro de diálogo Add New Person (Agregar nueva persona), realice los pasos siguientes:
   
    ![Usuario](./media/active-directory-saas-itrp-tutorial/ic775577.png "Usuario") 
      
    a. Escriba el **nombre** y el **correo electrónico** de la cuenta válida de AAD que desee aprovisionar.

    b. Haga clic en **Save**.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de ITRP que proporcione ITRP para aprovisionar cuentas de usuario de AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a ITRP.

![Asignar usuario][200] 

**Para asignar el usuario Britta Simon a ITRP, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **ITRP**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de ITRP en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de ITRP.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_203.png

