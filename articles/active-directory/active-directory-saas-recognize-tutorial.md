---
title: "Tutorial: integración de Azure Active Directory con Recognize | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Recognize."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: cf0945b478d284da0519a6da49f22f0940b80944
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Tutorial: Integración de Azure Active Directory con Recognize

En este tutorial, obtendrá información sobre cómo integrar SCC LifeCycle con Azure Active Directory (Azure AD).

Integrar Recognize con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Recognize
- Puede permitir que los usuarios inicien sesión automáticamente en Recognize (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Recognize, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Recognize

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, aquí puede obtener una versión de evaluación de un mes: [Oferta de prueba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Recognize desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-recognize-from-the-gallery"></a>Adición de Recognize desde la galería
Para configurar la integración de Recognize en Azure AD, deberá agregar Recognize desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Recognize desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Recognize**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_search.png)

5. En el panel de resultados, seleccione **Recognize** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Recognize con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Recognize para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Recognize.

Para establecer la relación de vínculo, en Recognize, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Recognize, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Recognize](#creating-a-recognize-test-user)**: para tener un homólogo de Britta Simon en Recognize que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Recognize.

**Para configurar el inicio de sesión único de Azure AD con Recognize, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Recognize**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_samlbase.png)

3. En la sección **Dominio y direcciones URL de Recognize**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://recognizeapp.com/<your-domain>/saml/sso`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://recognizeapp.com/<your-domain>`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte al cliente de Recognize](mailto:support@recognizeapp.com) para obtener la dirección URL de inicio de sesión. Puede obtener el valor del identificador abriendo la dirección URL de metadatos del proveedor de servicios desde la sección Configuración de SSO que se explica más adelante en el tutorial. . 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-recognize-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Recognize**, haga clic en **Configurar Recognize** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_configure.png) 

7. En otra ventana del explorador web, inicie sesión en el inquilino de Recognize como administrador.

8. En la esquina superior derecha, haga clic en **Menú**. Vaya a **Company Admin** (Administrador de empresa).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)

9. En la barra de navegación de la izquierda, haga clic en **Settings**(Configuración).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)

10. Siga estos pasos en la sección **SSO Settings** (Configuración de SSO).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
    
    a. En **Enable SSO** (Habilitar SSO), seleccione **ON** (Activado).

    b. En el cuadro de texto **IdP Entity ID** (Id. de entidad IdP), pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que copió de Azure Portal.
    
    c. En el cuadro de texto **SSO Target URL** (Dirección URL de destino de SSO), pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.
    
    d. En el cuadro de texto **SLO target URL**  (Dirección URL de objetivo de SLO), pegue el valor de la **dirección URL de cierre de sesión** que copió de Azure Portal. 
    
    e. Abra el archivo descargado **Certificado (Base64)** en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **Certificado**.
    
    f. Haga clic en el botón **Guardar configuración**. 

11. Junto a la sección de **configuración de SSO**, copie la dirección URL en **Service Provider Metadata url** (Dirección URL de metadatos del proveedor de servicio).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)

12. Abra el vínculo **Dirección URL de metadatos** en un explorador en blanco para descargar el documento de metadatos. Después, copie el valor de EntityDescriptor (entityID) del archivo y péguelo en el cuadro de texto **Identificador** en la sección **Dominio y direcciones URL de Recognize** en Azure Portal.
    
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-recognize-test-user"></a>Creación de un usuario de prueba de Recognize

Para permitir que los usuarios de Azure AD inicien sesión en Recognize, deben aprovisionarse en Recognize. En el caso de Recognize, el aprovisionamiento es una tarea manual.

Esta aplicación no admite el aprovisionamiento SCIM, pero tiene una sincronización de usuario alternativa que aprovisiona a los usuarios. 

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su sitio de la compañía de Recognize como administrador.

2. En la esquina superior derecha, haga clic en **Menú**. Vaya a **Company Admin** (Administrador de empresa).

3. En la barra de navegación de la izquierda, haga clic en **Settings**(Configuración).

4. En la sección **User Sync** (Sincronización de usuario), siga estos pasos.
   
   ![Nuevo usuario](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "Nuevo usuario")
   
   a. En **Sync Enabled** (Sincronización habilitada), seleccione **ON** (Activado).
   
   b. En **Choose sync provider** (Elegir proveedor de sincronización), seleccione **Microsoft/Office 365**.
   
   c. Haga clic en **Run User Sync** (Ejecutar sincronización de usuario).

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a Recognize para que use el inicio de sesión único de Azure.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Recognize, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Recognize**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Recognize en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Recognize. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png

