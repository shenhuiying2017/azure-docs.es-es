---
title: 'Tutorial: integrar Azure Active Directory con Jamf Pro | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 687453b37e15f5d3dd1fa161f89b6d18f90ba279
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343426"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Tutorial: integrar Azure Active Directory con Jamf Pro

En este tutorial, aprenderá a integrar Jamf Pro con Azure Active Directory (Azure AD).

La integración de Jamf Pro con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Jamf Pro.
- Puede permitir que los usuarios inicien sesión automáticamente en Jamf Pro (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Jamf Pro, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único de Jamf Pro.

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporar Jamf Pro desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-jamf-pro-from-the-gallery"></a>Incorporar Jamf Pro desde la galería
Para configurar la integración de Jamf Pro en Azure AD, deberá agregar Jamf Pro desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Jamf Pro desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Jamf Pro**, seleccione **Jamf Pro** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Jamf Pro en la lista de resultados](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Jamf Pro con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Jamf Pro para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Jamf Pro.

Para configurar y probar el inicio de sesión único de Azure AD con Jamf Pro, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Jamf Pro](#create-a-jamf-pro-test-user)**: para tener un homólogo de Britta Simon en Jamf Pro que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Jamf Pro.

**Para configurar el inicio de sesión único de Azure AD con Jamf Pro, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Jamf Pro**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

3. En la sección **Dominio y direcciones URL de Jamf Pro**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. En el cuadro de texto **Identificador (id. de entidad)**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.jamfcloud.com/saml/SSO`.

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.jamfcloud.com`.
     
    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Obtendrá el valor del identificador real de la sección **Inicio de sesión único** en el portal de Jamf Pro, que se explica más adelante en el tutorial. Puede extraer el valor real del **subdominio** del valor del identificador y usar esa información del **subdominio** en las direcciones URL de inicio de sesión y de respuesta.

5. En la sección **Certificado de firma de SAML**, haga clic en el botón Copiar para copiar la **dirección URL de metadatos de federación de la aplicación** y péguela en el Bloc de notas.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
7. En otra ventana del explorador web, inicie sesión en el sitio de la empresa Jamf Pro como administrador.

8. Haga clic en el **icono de configuración** de la esquina superior derecha de la página.

    ![Configuración de Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure1.png)

9. Haga clic en **Inicio de sesión único de SAML**.

    ![Configuración de Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure2.png)

10. Desplácese a la sección **PROVEEDOR DE IDENTIDADES** en la sección **Inicio de sesión único** y realice los pasos siguientes:

    ![Configuración de Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure3.png)

    a. En el menú desplegable **PROVEEDOR DE IDENTIDADES**, seleccione **Otro**.

    b. En el cuadro de texto **OTRO PROVEEDOR**, escriba **Azure AD**.

    c. Seleccione la **dirección URL de metadatos** como una opción del menú desplegable **ORIGEN DE METADATOS DEL PROVEEDOR DE IDENTIDADES** y, en el siguiente cuadro de texto, pegue el valor de la **dirección URL de metadatos de la federación de aplicaciones** que copió en Azure Portal.

    d. Copie el valor del **id. de entidad** y péguelo en el cuadro de texto **Identificador (id. de entidad)**, que se encuentra en la sección **Dominio y direcciones URL de Jamf Pro** de Azure Portal.

    >[!NOTE]
    > En `aadsso` está la parte de subdominio (que se usa para fines de referencia). Use este valor para completar las direcciones URL de inicio de sesión y respuesta en la sección **Dominio y direcciones URL de Jamf Pro** en Azure Portal.

    e. Haga clic en **Save**(Guardar).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-jamf-pro-test-user"></a>Crear un usuario de prueba de Jamf Pro

Para permitir que los usuarios de Azure AD inicien sesión en Jamf Pro, tienen que aprovisionarse en Jamf Pro. En el caso de Jamf Pro, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su sitio de la empresa de Jamf Pro como administrador.

2. Haga clic en el **icono de configuración** de la esquina superior derecha de la página.

    ![Agregar empleado](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure1.png)

3. Haga clic en **Grupos y cuentas de usuario de Jamf Pro**.

    ![Agregar empleado](./media/active-directory-saas-jamfprosamlconnector-tutorial/user1.png)

4. Haga clic en **Nuevo**.

    ![Agregar empleado](./media/active-directory-saas-jamfprosamlconnector-tutorial/user2.png)

5. Seleccione **Crear cuenta estándar**.

    ![Agregar empleado](./media/active-directory-saas-jamfprosamlconnector-tutorial/user3.png)

6. En el cuadro de diálogo **Nueva cuenta**, realice los pasos siguientes:

    ![Agregar empleado](./media/active-directory-saas-jamfprosamlconnector-tutorial/user4.png)

    a. En el cuadro de texto **NOMBRE DE USUARIO**, escriba el nombre completo de Britta Simon.

    b. Seleccione las opciones adecuadas según su organización para **NIVEL DE ACCESO**, **CONJUNTO DE PRIVILEGIOS** y **ESTADO DE ACCESO**.
    
    c. En el cuadro de texto **NOMBRE COMPLETO**, escriba el nombre completo de Britta Simon.

    d. En el cuadro de texto **DIRECCIÓN DE CORREO ELECTRÓNICO**, escriba la dirección de correo electrónico de la cuenta de Britta Simon.

    e. En el cuadro de texto **CONTRASEÑA**, escriba la contraseña del usuario.

    f. En el cuadro de texto **CONFIRMAR CONTRASEÑA**, escriba la contraseña del usuario.

    g. Haga clic en **Save**(Guardar).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Jamf Pro.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Jamf Pro, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Jamf Pro**.

    ![Vínculo a Jamf Pro en la lista de aplicaciones](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Jamf Pro en el Panel de acceso, debe iniciar sesión automáticamente en la aplicación Jamf Pro.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_203.png

