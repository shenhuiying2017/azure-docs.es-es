---
title: "Tutorial: Integración de Azure Active Directory con Halogen Software | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Halogen Software."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: af9c9d4b9bd562169bfe6c9158254beec5c3e1e7
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Tutorial: Integración de Azure Active Directory con Halogen Software

En este tutorial, aprenderá a integrar Halogen Software con Azure Active Directory (Azure AD).

La integración de Halogen Software con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Halogen Software.
- Puede permitir que los usuarios inicien sesión automáticamente en Halogen Software (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Halogen Software, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Un suscripción habilitada para el inicio de sesión único en Halogen Software

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar Halogen Software desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-halogen-software-from-the-gallery"></a>Agregar Halogen Software desde la galería

Para configurar la integración de Halogen Software en Azure AD, deberá agregar Halogen Software desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Halogen Software desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Halogen Software**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_search.png)

5. En el panel de resultados, seleccione **Halogen Software** y haga clic en **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Halogen Software con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Halogen Software para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Halogen Software.

En Halogen Software, asigne el valor del **nombre de usuario** en Azure AD como valor de **nombre de usuario** para establecer la relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con Halogen Software, debe completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Halogen Software](#creating-a-halogen-software-test-user)** : para tener un homólogo de Britta Simon en Halogen Software que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Halogen Software.

**Para configurar el inicio de sesión único de Azure AD con Halogen Software, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Halogen Software**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_samlbase.png)

3. En la sección **Dominio y direcciones URL de Halogen Software**, siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://global.hgncloud.com/<companyname>`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://global.halogensoftware.com/<companyname>`, `https://global.hgncloud.com/<companyname>`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Para obtener estos valores, póngase en contacto con el [equipo de soporte de cliente de Halogen Software](https://support.halogensoftware.com/). 
 


4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-halogen-software-tutorial/tutorial_general_400.png)

6. En una ventana de explorador diferente, inicie sesión en su aplicación de **Halogen Software** como administrador.

7. Haga clic en la pestaña **Opciones** . 
   
    ![Qué es Azure AD Connect][12]

8. En el panel de navegación izquierdo, haga clic en **Configuración de SAML**. 
   
    ![Qué es Azure AD Connect][13]

9. En la página **Configuración de SAML** , realice los siguientes pasos: 

    ![Qué es Azure AD Connect][14]

     a. En **Identificador único**, seleccione **NameID**.

     b. En **El identificador único se asigna a**, seleccione **Nombre de usuario**.
  
     c. Para cargar el archivo de metadatos descargado, haga clic en **Examinar** para seleccionar el archivo y en **Cargar archivo**.
 
     d. Para probar la configuración, haga clic en **Ejecutar prueba**. 
    
    >[!NOTE]
    >Deberá esperar a que aparezca el mensaje "*La prueba de SAML está completa. Cierre esta ventana* ". A continuación, cierre la ventana del explorador abierta. La casilla de verificación **Habilitar SAML** solo está habilitada si se ha completado la prueba. 
     
     e. Seleccione **Habilitar SAML**.
    
     f. Haga clic en **Guardar cambios**. 

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-halogen-software-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-halogen-software-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-halogen-software-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-halogen-software-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-halogen-software-test-user"></a>Creación de un usuario de prueba de Halogen Software

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en el Portal de Azure.

**Para crear un usuario llamado Britta Simon en Halogen Software, realice los pasos siguientes:**

1. Inicie sesión en su aplicación de **Halogen Software** como administrador.

2. Haga clic en la pestaña **Centro de usuarios** y en **Crear usuario**.
   
    ![Qué es Azure AD Connect][300]  

3. En la página del cuadro de diálogo **Nuevo usuario** , realice los pasos siguientes:
   
    ![Qué es Azure AD Connect][301]

    a. En el cuadro de texto **Nombre**, escriba el nombre del usuario, en este caso **Britta**.
    
    b. En el cuadro de texto **Apellidos**, escriba el apellido del usuario, en este caso **Simon**. 

    c. En el cuadro de texto **Nombre de usuario**, escriba el nombre de usuario **Britta Simon** como en Azure Portal.

    d. En el cuadro de texto **Contraseña** , escriba una contraseña para Britta.
    
    e. Haga clic en **Guardar**.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Halogen Software.

![Asignar usuario][200] 

**Para asignar un usuario llamado Britta Simon a Halogen Software, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Halogen Software**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Halogen Software en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de Halogen Software.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_04.png

[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png

[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png

[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png

[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_203.png

[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png

[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png
