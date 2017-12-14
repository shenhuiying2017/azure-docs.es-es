---
title: "Tutorial: Integración de Azure Active Directory con Ceridian Dayforce HCM | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Ceridian Dayforce HCM."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 7adf1eb3-d063-45d6-96a8-fd53b329b3f3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 9b87fe59f2761c26319ce9e13168dc6c4bf95f8b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Tutorial: Integración de Azure Active Directory con Ceridian Dayforce HCM

En este tutorial, obtendrá información sobre cómo integrar Ceridian Dayforce HCM con Azure Active Directory (Azure AD).

La integración de Ceridian Dayforce HCM con Azure AD ofrece las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a Ceridian Dayforce HCM.
- Puede permitir que los usuarios inicien sesión automáticamente en Ceridian Dayforce HCM (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Ceridian Dayforce HCM, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Ceridian Dayforce HCM

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Ceridian Dayforce HCM desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Incorporación de Ceridian Dayforce HCM desde la galería
Para configurar la integración de Ceridian Dayforce HCM en Azure AD, es preciso agregar Ceridian Dayforce HCM desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Ceridian Dayforce HCM desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Ceridian Dayforce HCM**, seleccione **Ceridian Dayforce HCM** en el panel de resultados y después haga clic en el botón **Agregar** para agregar la aplicación.

    ![Ceridian Dayforce HCM en la lista de resultados](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Ceridian Dayforce HCM con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Ceridian Dayforce HCM para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Ceridian Dayforce HCM.

Para establecer la relación de vínculo, en Ceridian Dayforce HCM, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Ceridian Dayforce HCM, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Crear un usuario de prueba de Ceridian Dayforce HCM](#create-a-ceridian-dayforce-hcm-test-user)**: para tener un homólogo de Britta Simon en Ceridian Dayforce HCM que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Ceridian Dayforce HCM.

**Para configurar el inicio de sesión único de Azure AD con Ceridian Dayforce HCM, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de aplicaciones de **Ceridian Dayforce HCM**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_samlbase.png)

3. En la sección **Dominio y direcciones URL de Ceridian Dayforce HCM**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_url.png)
    
    a. En el cuadro de texto **URL de inicio de sesión** , escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación de Ceridian Dayforce HCM.
    
    | Environment | URL |
    | :-- | :-- |
    | Para producción | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Para pruebas | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |
    
    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón:
    
    | Environment | URL |
    | :-- | :-- |
    | Para producción | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Para pruebas | `https://fs-test.dayforcehcm.com/sp` |
    
    c. En el cuadro de texto **URL de respuesta** , escriba la dirección URL que se usa en Azure AD para exponer la respuesta.
    
    | Environment | URL |
    | :-- | :-- |
    | Para producción | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Para pruebas | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |
    
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de Ceridian Dayforce HCM](https://www.ceridian.com/contact-us/index.html) para obtener estos valores.

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_certificate.png) 

5. La aplicación Ceridian Dayforce HCM espera las aserciones de SAML en un formato concreto. Colabore con el [equipo de soporte técnico de Ceridian Dayforce HCM](https://www.ceridian.com/contact-us/index.html) para identificar el identificador de usuario correcto. Microsoft recomienda utilizar el atributo **"name"** como identificador de usuario. Puede administrar los valores de estos atributos en la sección **Atributos de usuario** de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.  

    ![Configurar inicio de sesión único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_07.png)

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo  | Valor de atributo |
    | --------------- | -------------------- |    
    | name  | user.extensionattribute2 |    

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_attribute_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_attribute_05.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor** , seleccione el atributo de usuario que desea usar en su implementación.
    Por ejemplo, si quiere usar EmployeeID como identificador de usuario único y ha almacenado el valor del atributo en ExtensionAttribute2, seleccione **user.extensionattribute2**.
    
    d. Haga clic en **Aceptar**.

7. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_400.png)
    
8. En la sección **Configuración de Ceridian Dayforce HCM**, haga clic en **Configurar Ceridian Dayforce HCM** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de Ceridian Dayforce HCM](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_configure.png) 

9. Para configurar el inicio de sesión en **Ceridian Dayforce HCM**, necesita enviar el **XML de metadatos** descargado y **la dirección URL de cierre de sesión, el identificador de entidad de SAML y la dirección URL del servicio de inicio de sesión único de SAML** al [equipo de soporte técnico de Ceridian Dayforce HCM](https://www.ceridian.com/contact-us/index.html).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-ceridian-dayforce-hcm-test-user"></a>Crear un usuario de prueba de Ceridian Dayforce HCM

El objetivo de esta sección es crear una usuaria llamada Britta Simon en Ceridian Dayforce HCM. Colabore con el [equipo de soporte técnico de Ceridian Dayforce HCM](https://www.ceridian.com/contact-us/index.html) para que agreguen los usuarios a la aplicación de Ceridian Dayforce HCM. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Ceridian Dayforce HCM.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Ceridian Dayforce HCM, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Ceridian Dayforce HCM**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Ceridian Dayforce HCM.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Ceridian Dayforce HCM, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Ceridian Dayforce HCM**.

    ![Vínculo de Ceridian Dayforce HCM en la lista de aplicaciones](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  
Al hacer clic en el icono de Ceridian Dayforce HCM en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Ceridian Dayforce HCM. 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_203.png

