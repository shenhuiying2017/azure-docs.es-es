---
title: "Tutorial: Integración de Azure Active Directory con Tableau Server | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Tableau Server."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 9ba94f4f90f49fb7850f4cad2bb49ab4faa3fa92
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Tutorial: Integración de Azure Active Directory con Tableau Server

En este tutorial, aprenderá a integrar Tableau Server con Azure Active Directory (Azure AD).

Integrar Tableau Server con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Tableau Server.
- Puede permitir que los usuarios inicien sesión automáticamente en Tableau Server (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Tableau Server, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Tableau Server

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Tableau Server desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-tableau-server-from-the-gallery"></a>Incorporación de Tableau Server desde la galería
Para configurar la integración de Tableau Server en Azure AD, será preciso que agregue Tableau Server desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Tableau Server desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Tableau Server**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_search.png)

5. En el panel de resultados, seleccione **Tableau Server** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Tableau Server con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Tableau Server para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Tableau Server.

Para establecer la relación de vínculo, en Tableau Server, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Tableau Server, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Tableau Server](#creating-a-tableau-server-test-user)** : para tener un homólogo de Britta Simon en Tableau Server vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Tableau Server.

**Para configurar el inicio de sesión único de Azure AD con Tableau Server, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Tableau Server**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_samlbase.png)

3. En la sección **Tableau Server Domain and URLs** (Dominio y direcciones URL de Tableau Server), lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://azure.<domain name>.link`.
    
    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://azure.<domain name>.link`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://azure.<domain name>.link/wg/saml/SSO/index.html`.
     
    > [!NOTE] 
    > Los valores anteriores no son valores reales. Más adelante, actualice los valores con la dirección URL y el identificador reales de la página de configuración de Tableau Server. 

4. La aplicación Tableau Server espera las aserciones de SAML en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. En la siguiente captura de pantalla se muestra un ejemplo.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/3.png)
    
5. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo | Valor de atributo |
    | ---------------| --------------- |    
    | nombre de usuario | *user.displayname* |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_05.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.


6. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_certificate.png) 

7. Haga clic en el botón **Guardar**.

    ![Configuración del inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_400.png)
<CS>
8. Para configurar SSO para la aplicación, debe iniciar sesión en su inquilino de Tableau Server como administrador.
   
   a. En la configuración de Tableau Server, haga clic en la pestaña **SAML** .
  
    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 
  
   b. Active la casilla de **Use SAML for single sign-on**(Usar SAML para inicio de sesión único).
   
   c. URL de retorno de Tableau Server: es la URL a la que accederán los usuarios de Tableau Server, como http://servidor_de_tableau. No se recomienda usar http://localhost. No se permite usar una dirección URL con una barra diagonal final (por ejemplo, http://servidor_de_tableau/). Copie el valor de **Tableau Server return URL** (URL de retorno de Tableau Server) y péguelo en el cuadro de texto **URL de inicio de sesión** de la sección **Tableau Server Domain and URLs** (Dominio y direcciones URL de Tableau Server) de Azure AD.
   
   d. SAML entity ID (Id. de entidad SAML): el identificador de entidad identifica de forma exclusiva la instalación de Tableau Server en el IdP. Aquí, si quiere, puede especificar de nuevo la dirección URL de Tableau Server, pero no tiene que ser esa misma URL. Copie el valor de **SAML entity ID** (Id. de entidad SAML) y péguelo en el cuadro de texto **Identificador** de la sección **Tableau Server Domain and URLs** (Dominio y direcciones URL de Tableau Server) de Azure AD.
     
   e. Haga clic en **Export Metadata File** (Exportar archivo de metadatos) y ábralo en la aplicación del editor de texto. Busque la URL del Servicio de consumidor de aserciones con Http Post e índice 0 y copie la URL. Ahora, péguelo en el cuadro de texto **URL de respuesta** de la sección **Tableau Server Domain and URLs** (Dominio y direcciones URL de Tableau Server) de Azure AD.
   
   f. Busque el archivo de metadatos de federación descargado desde Azure Portal y cárguelo en el **SAML Idp metadata file**(Archivo de metadatos del proveedor de identidades SAML).
   
   g. Haga clic en el botón **Aceptar** de la página de configuración de Tableau Server.
   
    >[!NOTE] 
    >El cliente tiene que cargar los certificados en la configuración de SSO de SAML de Tableau Server para que se omitan en el flujo de SSO.
    >Si necesita ayuda para configurar SAML en Tableau Server, vea el artículo [Configuración de SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).
    >
<CE>

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-tableau-server-test-user"></a>Crear un usuario de prueba de Tableau Server

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Tableau Server. Debe aprovisionar todos los usuarios en el servidor de Tableau. 

Tenga en cuenta que el nombre de usuario debe coincidir con el valor que ha configurado en el atributo personalizado de **nombre de usuario** de Azure AD. Con la asignación correcta, la integración debería funcionar. [Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Si necesita crear un usuario manualmente, póngase en contacto con el administrador de Tableau Server de su organización.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Tableau Server.

![Asignar usuario][200] 

**Para asignar Britta Simon a Tableau Server, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Tableau Server**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Tableau Server en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Tableau Server.
Para más información sobre el Panel de acceso, vea la [introducción al Panel de acceso](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png

