---
title: "Tutorial: Integración de Azure Active Directory con GoToMeeting | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y GoToMeeting."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bcaf19f2-5809-4e1c-acbc-21a8d3498ccf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: jeedes
ms.openlocfilehash: 4826dee82e62ffac70d7ca3d6dcfe005129de764
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="tutorial-azure-active-directory-integration-with-gotomeeting"></a>Tutorial: Integración de Azure Active Directory con GoToMeeting

En este tutorial, aprenderá a integrar GoToMeeting con Azure Active Directory (Azure AD).

La integración de GoToMeeting con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a GoToMeeting.
- Puede permitir que los usuarios inicien sesión automáticamente en GoToMeeting (Inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con GoToMeeting, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en GoToMeeting

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de GoToMeeting desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-gotomeeting-from-the-gallery"></a>Incorporación de GoToMeeting desde la galería
Para configurar la integración de GoToMeeting en Azure AD, tendrá que agregar GoToMeeting desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar GoToMeeting desde la galería, realice los siguientes pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **GoToMeeting**, seleccione **GoToMeeting** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![GoToMeeting en la lista de resultados](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con GoToMeeting con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD tiene saber cuál es el usuario homólogo en GoToMeeting de un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de GoToMeeting.

Para establecer la relación de vínculo, en GoToMeeting, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con GoToMeeting, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de GoToMeeting ](#create-a-gotomeeting-test-user)**: para tener un homólogo de Britta Simon en GoToMeeting que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación GoToMeeting.

**Para configurar el inicio de sesión único de Azure AD con GoToMeeting, realice los siguientes pasos:**

1. En la página de integración de la aplicación **GoToMeeting** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_samlbase.png)

3. En la sección **Dominio y direcciones URL de GoToMeeting**, lleve a cabo los pasos siguientes:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de GoToMeeting](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_url.png)

    En el cuadro de texto **Identificador**, escriba la dirección URL: `https://login.citrixonline.com/saml/sp`

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_400.png)

6. Para generar la dirección URL de **Metadatos**, lleve a cabo los pasos siguientes:

    a. Haga clic en **Registros de aplicaciones**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_appregistrations.png)
   
    b. Haga clic en **Puntos de conexión** para abrir el cuadro de diálogo **Puntos de conexión**.  
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_endpointicon.png)

    c. Haga clic en el botón Copiar para copiar la dirección URL del **DOCUMENTO DE METADATOS DE FEDERACIÓN** y péguela en el Bloc de notas.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_endpoint.png)
     
    d. Ahora, vaya a la página de propiedades de **GoToMeeting** y copie el **Identificador de la aplicación** con el botón **Copiar** y péguelo en el Bloc de notas.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_appid.png)

    e. Genere la **Dirección URL de metadatos** con el patrón siguiente: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`   

7. En la sección **Configuración de GoToMeeting**, haga clic en **Configurar GoToMeeting** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de GoToMeeting](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_configure.png) 

8. En otra ventana del explorador, inicie sesión en su [Centro de organización de GoToMeeting](https://organization.logmeininc.com/)

9. En la pestaña de **proveedor de identidades**, puede configurar los valores de Azure o bien proporcionando la **Dirección URL de metadatos** generada o el **Archivo de metadatos** o **Manual** descargados.

10. Para la **dirección URL de Metadatos**, lleve a cabo los pasos siguientes:

    ![Configuración de GoToMeeting](./media/active-directory-saas-gotomeeting-tutorial/config1.png)

    a. En **How would you like to configure your SAML IDP?**(¿Cómo desearía configurar el IDP de SAML?), seleccione **Automática** en la lista desplegable.

    b. Pegue la **dirección URL de metadatos**, que han generado en los pasos anteriores en el  cuadro de texto **URL de metadatos**.

    c. Haga clic en **Save**(Guardar).

11. Para el **archivo de metadatos**, lleve a cabo los pasos siguientes:

    ![Configuración de GoToMeeting](./media/active-directory-saas-gotomeeting-tutorial/config2.png)

    a. En **How would you like to configure your SAML IDP?**(¿Cómo desearía configurar el IDP de SAML?), seleccione **Upload SAML metadata file** (Cargar archivo de metadatos de SAML) en la lista desplegable.

    b. Haga clic en **Upload metadata file**(Cargar archivo de metadatos) para cargar el archivo de metadatos descargado.

    c. Haga clic en **Save**(Guardar).

12. Para **Manual** lleve a cabo los siguiente pasos:

    ![Configuración de GoToMeeting](./media/active-directory-saas-gotomeeting-tutorial/config3.png)

    a.  Copie el valor de **SAML Single Sign-On Service URL** (Dirección URL de inicio de sesión único de SAML) que ha copiado de Azure Portal en el cuadro de texto **Sign-in page URL** (Dirección URL de la página de inicio de sesión).

    b.  En el cuadro de texto **Sign-out page URL** (Dirección URL de la página de cierre de sesión), pegue el valor de **Dirección URL de cierre de sesión** que copió de Azure Portal.

    c.  En el cuadro de texto **Identity provider Entity ID** (Identificador de entidad del proveedor de identidades), pegue el valor del **Identificador de entidad de SAML** que ha copiado de Azure Portal.

    d. Extraiga X509Certificate desde el archivo de metadatos descargado y cargue este certificado, haciendo clic en **Cargar certificado**.

    e. Haga clic en **Save**(Guardar).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-gotomeeting-test-user"></a>Creación de un usuario de prueba de GoToMeeting

En esta sección, creará un usuario llamado a Britta Simon en GoToMeeting. GoToMeeting admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe aún en GoToMeeting., se crea uno nuevo cuando se intenta acceder a esta aplicación.

> [!NOTE]
> Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de GoToMeeting](https://support.logmeininc.com/gotomeeting).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure mediante la concesión de acceso a GoToMeeting.

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a GoToMeeting, lleve a cabo los siguientes pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **GoToMeeting**.

    ![Vínculo a GoToMeeting en la lista de aplicaciones](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de GoToMeeting en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación GoToMeeting.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del aprovisionamiento de usuarios](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrixgotomeeting-provisioning-tutorial)


<!--Image references-->

[1]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_203.png

