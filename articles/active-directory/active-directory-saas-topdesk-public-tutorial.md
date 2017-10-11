---
title: "Tutorial: integración de Azure Active Directory con TOPdesk - Public | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y TOPdesk - Public."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: f21fe0b363776974108ff460060e4c15a51a58a3
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Tutorial: Integración de Azure Active Directory con TOPdesk - Public

En este tutorial, obtendrá información sobre cómo integrar TOPdesk - Public con Azure Active Directory (Azure AD).

La integración de TOPdesk - Public con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a TOPdesk - Public.
- Puede permitir que los usuarios inicien sesión automáticamente en TOPdesk - Public (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con TOPdesk - Public, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en TOPdesk - Public

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar TOPdesk - Public desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-topdesk---public-from-the-gallery"></a>Agregar TOPdesk - Public desde la galería
Para configurar la integración de TOPdesk - Public en Azure AD, deberá agregar TOPdesk - Public desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar TOPdesk - Public desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **TOPdesk - Public**, seleccione **TOPdesk - Public** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![TOPdesk - Public en la lista de resultados](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con TOPdesk - Public con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de TOPdesk - Public para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de TOPdesk - Public.

Para establecer la relación de vínculo, en TOPdesk - Public asigne el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con TOPdesk - Public, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de TOPdesk - Public](#create-a-topdesk---public-test-user)**: para tener un homólogo de Britta Simon en TOPdesk - Public que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación TOPdesk - Public.

**Para configurar el inicio de sesión único de Azure AD con TOPdesk - Public, realice los pasos siguientes:**

1. En la página de integración de la aplicación **TOPdesk - Public** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_samlbase.png)

3. En la sección **Dominio y direcciones URL de TOPdesk - Public**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de TOPdesk - Public](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.topdesk.net`.
    
    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.topdesk.net/tas/public/login/verify`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.topdesk.net/tas/public/login/saml`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. La dirección URL de respuesta se explica más adelante en el tutorial. Póngase en contacto con el [equipo de soporte técnico de TOPdesk - Public](https://help.topdesk.com/saas/enterprise/user/) para obtener estos valores.  

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_400.png)
    
6. En la sección **TOPdesk - Public Configuration** (Configuración de TOPdesk - Public), haga clic en **Configure TOPdesk - Public** (Configurar TOPdesk - Public) para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de TOPdesk - Public](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_configure.png) 

7. Inicie sesión en el sitio de la compañía de **TOPdesk - Public** como administrador.

8. En el menú **TOPdesk**, haga clic en **Configuración**.
   
    ![Configuración](./media/active-directory-saas-topdesk-public-tutorial/ic790598.png "Configuración")

9. Haga clic en **Login Settings**(Configuración de inicio de sesión).
   
    ![Configuración de inicio de sesión](./media/active-directory-saas-topdesk-public-tutorial/ic790599.png "Configuración de inicio de sesión")

10. Expanda el menú **Login Settings** (Configuración de inicio de sesión) y luego haga clic en **General**.
   
    ![General](./media/active-directory-saas-topdesk-public-tutorial/ic790600.png "General")

11. En la sección **Público** de la sección de configuración **Inicio de sesión SAML**, siga estos pasos:
   
    ![Configuración técnica](./media/active-directory-saas-topdesk-public-tutorial/ic790601.png "Configuración técnica")
   
    a. Haga clic en **Download** (Descargar) para descargar el archivo de metadatos público y luego guárdelo localmente en el equipo.
   
    b. Abra el archivo de metadatos descargado y luego busque el nodo **AssertionConsumerService**.

    ![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Copia el valor de **AssertionConsumerService**, péguelo en el cuadro de texto **Dirección URL de respuesta** de la sección **Dominio y direcciones URL de TOPdesk - Public**.      
   
12. Lleve a cabo los siguientes pasos para crear un archivo de certificado:
    
    ![Certificado](./media/active-directory-saas-topdesk-public-tutorial/ic790606.png "Certificado")
    
    a. Abra el archivo de metadatos descargado de Azure Portal.
    
    b. Expanda el nodo **RoleDescriptor** cuyo **xsi:type** es **fed:ApplicationServiceType**.
    
    c. Copie el valor del nodo **X509Certificate** .
    
    d. Guarde el valor de **X509Certificate** copiado localmente en el equipo en un archivo.

13. En la sección **Public** (Público), haga clic en **Add** (Agregar).
    
    ![Inicio de sesión SAML](./media/active-directory-saas-topdesk-public-tutorial/ic790625.png "Inicio de sesión SAML")

14. En la página de diálogo del **SAML configuration assistant** (Asistente de configuración de SAML), realice los siguientes pasos:
    
    ![Asistente para configuración de SAML](./media/active-directory-saas-topdesk-public-tutorial/ic790608.png "Asistente de configuración de SAML")
    
    a. Para cargar el archivo de metadatos descargado de Azure Portal, en **Federation Metadata** (Metadatos de federación) haga clic en **Browse** (Examinar).

    b. Para cargar el archivo del certificado, en **Certificate (RSA)** (Certificado [RSA]), haga clic en **Browse** (Examinar).

    c. Para cargar el archivo de logotipo que obtuvo del equipo de soporte técnico de TOPdesk, en **Logo icon** (Icono de logotipo), haga clic en **Browse** (Examinar).

    d. En el cuadro de texto **User name attribute** (Atributo de nombre de usuario), escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. En el cuadro de texto **Display name** (Nombre para mostrar), escriba un nombre para su configuración.

    f. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-topdesk---public-test-user"></a>Creación de un usuario de prueba de TOPdesk - Public

Para permitir que los usuarios de Azure AD inicien sesión en TOPdesk - Public, deben aprovisionarse en TOPdesk - Public.  
En el caso de TOPdesk - Public, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en su sitio de la compañía de **TOPdesk - Public** como administrador.

2. En el menú de la parte superior, haga clic en **TOPdesk \> New \> Support Files \> Person** (TOPdesk > Nuevo > Archivos de soporte > Persona).
   
    ![Persona](./media/active-directory-saas-topdesk-public-tutorial/ic790628.png "Persona")

3. En el cuadro de diálogo Nueva persona, realice los pasos siguientes:
   
    ![Nueva persona](./media/active-directory-saas-topdesk-public-tutorial/ic790629.png "Nueva persona")
   
    a. Haga clic en la pestaña General.

    b. En el cuadro de texto **Apellidos**, escriba los apellidos del usuario, en este caso, Simon.
 
    c. Seleccione un **sitio** para la cuenta.
 
    d. Haga clic en **Guardar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de TOPdesk - Public ofrecida por TOPdesk - Public para aprovisionar cuentas de usuario de Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a TOPdesk - Public.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a TOPdesk - Public, lleve a cabo los siguientes pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **TOPdesk - Public**.

    ![Vínculo a TOPdesk - Public en la lista de aplicaciones](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de TOPdesk - Public en el panel de acceso, debería iniciar sesión automáticamente en la aplicación TOPdesk - Public.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_203.png

