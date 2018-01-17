---
title: "Tutorial: Integración de Azure Active Directory con Sequr | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Sequr."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a491e2ce-b4e8-41b8-8f4a-a2e263e462c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2017
ms.author: jeedes
ms.openlocfilehash: 8a2984bc2c77e9d92d4236b01c21912c27e167f9
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sequr"></a>Tutorial: Integración de Azure Active Directory con Sequr

En este tutorial, obtendrá información sobre cómo integrar Sequr con Azure Active Directory (Azure AD).

La integración de Sequr con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Sequr.
- Puede permitir que los usuarios inicien sesión automáticamente en Sequr (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Sequr, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Suscripción habilitada para el inicio de sesión único en Sequr

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Sequr desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-sequr-from-the-gallery"></a>Adición de Sequr desde la galería
Para configurar la integración de Sequr en Azure AD, deberá agregar Sequr desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar Sequr desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Sequr**, seleccione **Sequr** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Sequr en la lista de resultados](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Sequr con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Sequr para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Sequr.

Para establecer la relación de vínculo, en Sequr, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Sequr, es necesario completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en Sequr](#create-a-sequr-test-user)**: para tener un homólogo de Britta Simon en Sequr que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Sequr.

**Para configurar el inicio de sesión único de Azure AD con Sequr, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Sequr**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_samlbase.png)

3. En la sección **Dominio y direcciones URL de Sequr**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Sequr](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_url.png)

    En el cuadro de texto **Identificador**, escriba la dirección URL: `https://login.sequr.io`

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Sequr](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_url1.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://login.sequr.io`

    b. En el cuadro de texto **Estado de la retransmisión**, obtendrá este valor, que se explica más adelante en el tutorial.
     
5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-sequr-tutorial/tutorial_general_400.png)
    
7. En la sección **Configuración de Sequr**, haga clic en **Configurar Sequr** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configuración de Sequr](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_configure.png)

8. En otra ventana del explorador web, inicie sesión en su sitio de la empresa de Sequr como administrador.

9. Haga clic en **Integraciones**, en el panel de navegación izquierdo.

    ![Configuración de Sequr](./media/active-directory-saas-sequr-tutorial/configure1.png)

10. Desplácese hacia abajo hasta la sección **Inicio de sesión único** y haga clic en **Administrar**.

    ![Configuración de Sequr](./media/active-directory-saas-sequr-tutorial/configure2.png)

11. En la sección **Administración de inicio de sesión único**, lleve a cabo los pasos siguientes:

    ![Configuración de Sequr](./media/active-directory-saas-sequr-tutorial/configure3.png)

    a. En el cuadro de texto **Dirección URL del inicio de sesión único del proveedor de identidades**, pegue el valor de **Dirección URL del servicio de inicio de sesión único de SAML**, que ha copiado desde Azure Portal.

    b. Arrastre y coloque el archivo **Certificado** que ha descargado desde Azure Portal o escriba manualmente el contenido del certificado.

    c. Después de guardar la configuración, se generará el valor de estado de la retransmisión. Copie el valor de **estado de la retransmisión** y péguelo en el cuadro de texto **Estado de la retransmisión** de la sección **Dominio y direcciones URL de Sequr** de Azure Portal.

    d. Haga clic en **Save**(Guardar).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-sequr-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-sequr-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-sequr-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-sequr-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-sequr-test-user"></a>Creación de un usuario de prueba de Sequr

En esta sección, creará un usuario llamado Britta Simon en Sequr. Trabaje con el [equipo de soporte técnico al cliente de Sequr](mailto:support@sequr.io) para agregar los usuarios a la plataforma de Sequr. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Sequr.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Sequr, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Sequr**.

    ![Vínculo a Sequr en la lista de aplicaciones](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Sequr en el Panel de acceso, se debería iniciar sesión automáticamente en su aplicación Sequr.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_203.png

