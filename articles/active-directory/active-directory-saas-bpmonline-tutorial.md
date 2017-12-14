---
title: "Tutorial: integración de Azure Active Directory con Bpm’online | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Bpm’online."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 052db91d-ccff-4098-8ae3-2f76eca90539
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: jeedes
ms.openlocfilehash: 0214fcbdde886bd14d84917e496568027d417096
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bpmonline"></a>Tutorial: integración de Azure Active Directory con Bpm’online

En este tutorial, aprenderá a integrar Bpm’online con Azure Active Directory (Azure AD).

Integrar Bpm’online con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Bpm’online.
- Puede permitir que los usuarios inicien sesión automáticamente en Bpm’online (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Bpm’online, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Bpm’online

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Bpm’online desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-bpmonline-from-the-gallery"></a>Incorporación de Bpm’online desde la galería
Para configurar la integración de Bpm’online en Azure AD, deberá agregarlo de la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Bpm’online desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Bpm’online**, seleccione **Bpm’online** en el panel de resultados y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Bpm’online en la lista de resultados](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Bpm’online con un usuario de prueba denominado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Bpm’online para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Bpm’online.

Para establecer la relación de vínculo, en Bpm’online, asigne el valor del **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Bpm’online, es preciso completar los siguientes bloques de compilación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Bpm’online](#create-a-bpmonline-test-user)**: para tener un homólogo de Britta Simon en Bpm’online vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Bpm’online.

**Para configurar el inicio de sesión único de Azure AD con Bpm’online, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Bpm’online**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_samlbase.png)

3. En la sección **Bpm’online Domain and URLs** (Dominio y direcciones URL de Bpm’online), realice los siguientes pasos si desea configurar la aplicación en el modo iniciado por el **punto de distribución de emisión**:

    ![Información sobre el dominio y las direcciones URL de inicio de sesión único de Bpm’online](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<client site name>.bpmonline.com/`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<client site name>.bpmonline.com/ServiceModel/AuthService.svc/SsoLogin`.

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información sobre el dominio y las direcciones URL de inicio de sesión único de Bpm’online](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<client site name>.bpmonline.com/`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Bpm’online](mailto:support@bpmonline.com) para obtener estos valores. 

5. Para generar la dirección URL de **Metadatos**, lleve a cabo los pasos siguientes:

    a. Haga clic en **Registros de aplicaciones**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_appregistrations.png)
   
    b. Haga clic en **Puntos de conexión** para abrir el cuadro de diálogo **Puntos de conexión**.  
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_endpointicon.png)

    c. Haga clic en el botón Copiar para copiar la dirección URL del **DOCUMENTO DE METADATOS DE FEDERACIÓN** y péguela en el Bloc de notas.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_endpoint.png)
     
    d. Ahora, vaya a la página de propiedades de **Bpm’online** y copie el **Identificador de la aplicación** con el botón **Copiar** y péguelo en el bloc de notas.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_appid.png)

     e. Genere la **Dirección URL de metadatos** con el patrón siguiente: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>` 

6. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-bpmonline-tutorial/tutorial_general_400.png)
    
8. Para configurar el inicio de sesión único del lado de **Bpm’online**, debe enviar la **URL de metadatos** al [equipo de soporte técnico de Bpm’online](mailto:support@bpmonline.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-bpmonline-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-bpmonline-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-bpmonline-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-bpmonline-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-bpmonline-test-user"></a>Creación de un usuario de prueba de Bpm’online

En esta sección se crea un usuario llamado Britta Simon en Bpm’online. Trabaje con el [equipo de soporte técnico de Bpm’online](mailto:support@bpmonline.com) para agregar los usuarios a la plataforma de Bpm’online. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección se habilita a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Bpm’online.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Bpm’online, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones. seleccione **Bpm’online**.

    ![Vínculo a Bpm’online en la lista de aplicaciones](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Bpm’online en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación Bpm’online.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_203.png

