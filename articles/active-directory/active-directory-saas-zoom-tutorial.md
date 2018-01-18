---
title: "Tutorial: Integración de Azure Active Directory con Zoom | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Zoom."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/28/2017
ms.author: jeedes
ms.openlocfilehash: 5a6d9ea9de1035bf9c84cf3c451cc1121f04a82a
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Tutorial: Integración de Azure Active Directory con Zoom

En este tutorial, obtendrá información sobre cómo integrar Zoom con Azure Active Directory (Azure AD).

La integración de Zoom con Azure AD le proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a Zoom.
- Puede permitir que los usuarios inicien sesión automáticamente en Zoom (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Zoom, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Zoo

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Zoom desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-zoom-from-the-gallery"></a>Adición de Zoom desde la galería
Para configurar la integración de Zoom en Azure AD, deberá agregar Zoom desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Zoom desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Zoom**, seleccione **Zoom** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Zoom en la lista de resultados](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Zoom con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Zoom para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Zoom.

Para establecer la relación de vínculo, en Zoom, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Zoom, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Zoom](#create-a-zoom-test-user)** : para tener un homólogo de Britta Simon en Zoom que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en su aplicación Zoom.

**Para configurar el inicio de sesión único de Azure AD con Zoom, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Zoom**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_samlbase.png)

3. En la sección **Dominio y direcciones URL de Zoom**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Zoom](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.zoom.us`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `<companyname>.zoom.us`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Contacte con el [equipo de soporte al cliente de Zoom](https://support.zoom.us/hc) para obtener estos valores.

4. La aplicación Zoom espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-Zoom-tutorial/tutorial_attribute.png)

5. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo del token de SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo | Valor de atributo | Valor de espacio de nombres |
    | ------------------- | -----------|--------- |    
    | Dirección de correo electrónico | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail`|
    | Nombre | user.givenname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`|
    | Apellidos | user.surname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname `|
    | Número de teléfono | user.telephonenumber | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone`|
    | department | user.department | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department`|

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-Zoom-tutorial/tutorial_attribute_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-Zoom-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. En el cuadro de texto **Espacio de nombres**, escriba el valor del espacio de nombres que se muestra para esa fila.
    
    e. Haga clic en **Aceptar**. 
 
6. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_certificate.png)

7. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-zoom-tutorial/tutorial_general_400.png)

8. En la sección **Configuración de Zoom**, haga clic en **Configurar Zoom** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de Zoom](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_configure.png)

9. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Zoom como administrador.

10. Haga clic en la pestaña **Inicio de sesión único** .
   
    ![Pestaña de Inicio de sesión único](./media/active-directory-saas-zoom-tutorial/IC784700.png "Inicio de sesión único")

11. Haga clic en la pestaña **Control de seguridad** y luego vaya a la configuración de **Inicio de sesión único**.

12. En la sección Inicio de sesión único, siga estos pasos:
   
    ![Sección de Inicio de sesión único](./media/active-directory-saas-zoom-tutorial/IC784701.png "Inicio de sesión único")
   
    a. Copie el valor de **SAML Single Sign-On Service URL** (Dirección URL de inicio de sesión único de SAML) que ha copiado de Azure Portal en el cuadro de texto **Dirección URL de la página de inicio de sesión**.
   
    b. En el cuadro de texto **Dirección URL de la página de cierre de sesión**, pegue el valor de **dirección URL de cierre de sesión** que copió de Azure Portal.
     
    c. Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **Certificado de proveedor de identidades** .

    d. En el cuadro de texto **Emisor**, pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que ha copiado de Azure Portal. 

    e. Haga clic en **Save**(Guardar).

    > [!NOTE] 
    > Para obtener más información, visite la documentación de Zoom [https://zoomus.zendesk.com/hc/en-us/articles/115005887566](https://zoomus.zendesk.com/hc/en-us/articles/115005887566)

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-zoom-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-zoom-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-zoom-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-zoom-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-zoom-test-user"></a>Crear un usuario de prueba de Zoom

Para permitir que los usuarios de Azure AD inicien sesión en Zoom, deben aprovisionarse en Zoom. En el caso de Zoom, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:

1. Inicie sesión en su sitio de la compañía de **Zoom** como administrador.
 
2. En la pestaña **Administración de cuentas** haga clic en **Administración de usuarios**.

3. En la sección Administración de usuarios, haga clic en **Agregar usuarios**.
   
    ![Administración de usuarios](./media/active-directory-saas-zoom-tutorial/IC784703.png "Administración de usuarios")

4. En la página **Agregar usuarios** , realice los pasos siguientes:
   
    ![Agregar usuarios](./media/active-directory-saas-zoom-tutorial/IC784704.png "Agregar usuarios")
   
    a. Como **Tipo de usuario**, seleccione **Básico**.

    b. En el cuadro de texto **Correos electrónicos** , escriba la dirección de correo electrónico de una cuenta de Azure AD válida que quiera suministrar.

    c. Haga clic en **Agregar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Zoom que proporcione Zoom para aprovisionar cuentas de usuario de Azure Active Directory.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Zoom.

![Asignación de rol de usuario][200] 

**Para asignar el usuario Britta Simon a Zoom, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Zoom**.

    ![Vínculo a Zoom en la lista de aplicaciones](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Zoom en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Zoom.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_203.png

