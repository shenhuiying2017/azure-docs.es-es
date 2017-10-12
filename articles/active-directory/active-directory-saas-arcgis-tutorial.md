---
title: "Tutorial: Integración de Azure Active Directory con ArcGIS Online | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y ArcGIS Online."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: jeedes
ms.openlocfilehash: df72270ca6443b456c079b22425f1660aa522389
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Tutorial: Integración de Azure Active Directory con ArcGIS Online

En este tutorial, obtendrá información sobre cómo integrar ArcGIS Online con Azure Active Directory (Azure AD).

La integración de ArcGIS Online con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a ArcGIS Online
- Puede permitir que los usuarios inicien sesión automáticamente en ArcGIS Online (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with ArcGIS Online, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in ArcGIS Online.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con ArcGIS Online, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en ArcGIS Online

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de ArcGIS Online desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-arcgis-online-from-the-gallery"></a>Incorporación de ArcGIS Online desde la galería
Para configurar la integración de ArcGIS Online en Azure AD, deberá agregar ArcGIS Online desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar ArcGIS Online desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Nueva aplicación** en la parte superior del cuadro de diálogo para agregar la nueva aplicación.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **ArcGIS Online**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_search.png)

5. En el panel de resultados, seleccione **ArcGIS Online** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con ArcGIS Online con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de ArcGIS Online para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ArcGIS Online.

Para establecer la relación de vínculo, en ArcGIS Online, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con ArcGIS Online, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de ArcGIS Online](#creating-an-arcgis-online-test-user)**: para tener un homólogo de Britta Simon en ArcGIS Online que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación ArcGIS Online.

**Para configurar el inicio de sesión único de Azure AD con ArcGIS Online, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **ArcGIS Online**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

3. En la sección **Dominio y direcciones URL de ArcGIS Online**, lleve a cabo el paso siguiente:

    ![Configurar inicio de sesión único](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba el valor con el siguiente patrón: `https://<company>.maps.arcgis.com`

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de ArcGIS Online](http://support.esri.com/) para obtener este valor. 

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo XML en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-arcgis-tutorial/tutorial_general_400.png)

6. En otra ventana del explorador web, inicie sesión en como administrador en el sitio de la compañía de ArcGIS.

7. Haga clic en **EDITAR CONFIGURACIÓN**.

    ![Editar configuración](./media/active-directory-saas-arcgis-tutorial/ic784742.png "Editar configuración")

8. Haga clic en **Seguridad**.

    ![Seguridad](./media/active-directory-saas-arcgis-tutorial/ic784743.png "Seguridad")

9. En **Inicios de sesión de la empresa**, haga clic en **ESTABLECER PROVEEDOR DE IDENTIDADES**.

    ![Enterprise Logins (Inicios de sesión de la empresa)](./media/active-directory-saas-arcgis-tutorial/ic784744.png "Enterprise Logins (Inicios de sesión de la empresa)")

10. En la sección **Configurar proveedor de identidades** , realice los pasos siguientes:
   
    ![Set Identity Provider (Establecer proveedor de identidades)](./media/active-directory-saas-arcgis-tutorial/ic784745.png "Set Identity Provider (Establecer proveedor de identidades)")
   
    a. En el cuadro de texto **Nombre**, escriba el nombre de la organización.

    b. En **Metadata for the Enterprise Identity Provider will be supplied using** (Los metadatos para el proveedor de identidades de la empresa se proporcionarán con), seleccione **A File** (Un archivo).

    c. Haga clic en **Choose file**(Elegir archivo) para cargar el archivo de metadatos descargado.

    d. Haga clic en **ESTABLECER PROVEEDOR DE IDENTIDADES**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-arcgis-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-arcgis-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-arcgis-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-arcgis-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-an-arcgis-online-test-user"></a>Creación de un usuario de prueba de ArcGIS Online

Para permitir que los usuarios de Azure AD inicien sesión en ArcGIS Online, tienen que aprovisionarse en ArcGIS Online.  
En el caso de ArcGIS Online, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **ArcGIS** .

2. Haga clic en **INVITAR A MIEMBROS**.
   
    ![Invitar a miembros](./media/active-directory-saas-arcgis-tutorial/ic784747.png "Invitar a miembros")

3. Seleccione **Agregar miembros automáticamente sin enviar un correo electrónico** y luego haga clic en **SIGUIENTE**.
   
    ![Agregar miembros automáticamente](./media/active-directory-saas-arcgis-tutorial/ic784748.png "Agregar miembros automáticamente")

4. En la página de diálogo **Miembros** , realice los pasos siguientes:
   
     ![Agregar y revisar](./media/active-directory-saas-arcgis-tutorial/ic784749.png "Agregar y revisar")
    
     a. Escriba los valores de **correo electrónico**, **nombre** y **apellido** de una cuenta de AAD válida que desea aprovisionar.
  
     b. Haga clic en **AGREGAR Y REVISAR**.
5. Revise los datos que ha escrito y luego haga clic en **AGREGAR MIEMBROS**.
   
    ![Agregar miembros](./media/active-directory-saas-arcgis-tutorial/ic784750.png "Agregar miembros")
        
    > [!NOTE]
    > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a ArcGIS Online.

![Asignar usuario][200] 

**Para asignar a Britta Simon a ArcGIS Online, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones. seleccione **ArcGIS Online**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de ArcGIS Online en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación ArcGIS Online.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_203.png

