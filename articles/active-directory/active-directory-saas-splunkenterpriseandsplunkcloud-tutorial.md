---
title: "Tutorial: Integración de Azure Active Directory con Splunk Enterprise y Splunk Cloud | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Splunk Enterprise y Splunk Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b3e2b4a9-749c-4895-813d-db46f8dfdbf8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2017
ms.author: jeedes
ms.openlocfilehash: fe9a995a32f632c47aeed6d3f12d37a224823f06
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Tutorial: Integración de Azure Active Directory con Splunk Enterprise y Splunk Cloud

En este tutorial, aprenderá a integrar Splunk Enterprise y Splunk Cloud con Azure Active Directory (Azure AD).

La integración de Splunk Enterprise y Splunk Cloud con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Splunk Enterprise y Splunk Cloud.
- Puede permitir que los usuarios inicien sesión automáticamente en Splunk Enterprise y Splunk Cloud (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Splunk Enterprise y Splunk Cloud, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en Splunk Enterprise y Splunk Cloud

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Splunk Enterprise y Splunk Cloud desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Adición de Splunk Enterprise y Splunk Cloud desde la galería
Para configurar la integración de Splunk Enterprise y Splunk Cloud en Azure AD, deberá agregar Splunk Enterprise y Splunk Cloud desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Splunk Enterprise y Splunk Cloud desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Splunk Enterprise y Splunk Cloud**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_search.png)

5. En el panel de resultados, seleccione **Splunk Enterprise y Splunk Cloud** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Splunk Enterprise y Splunk Cloud mediante un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Splunk Enterprise y Splunk Cloud para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Splunk Enterprise y Splunk Cloud.

En Splunk Enterprise y Splunk Cloud, esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Splunk Enterprise y Splunk Cloud, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Splunk Enterprise y Splunk Cloud](#creating-a-splunk-enterprise-and-splunk-cloud-test-user)**: para tener un homólogo de Britta Simon en Splunk Enterprise y Splunk Cloud que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Splunk Enterprise y Splunk Cloud.

**Para configurar el inicio de sesión único en Azure AD con Splunk Enterprise y Splunk Cloud, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Splunk Enterprise y Splunk Cloud**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_samlbase.png)

3. En la sección **Splunk Enterprise and Splunk Cloud Domain and URLs** (Dominio y direcciones URL de Splunk Enterprise y Splunk Cloud), si quiere configurar la aplicación en el modo iniciado por **IdP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<splunkserverUrl>/en-US/app/launcher/home`.
    
    b. En el cuadro de texto **Identificador**, escriba la dirección URL de su instancia de Splunk Server:

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<splunkserver>/saml/acs`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Aquí le recomendamos que utilice el valor de cadena único en el identificador. Póngase en contacto con el [equipo de soporte de cliente de Splunk Enterprise y Splunk Cloud](https://www.splunk.com/content/splunkcom/en_us/about-us/contact.html#tabs/customer-support) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_400.png)

6. Para configurar el inicio de sesión único en el lado de **Splunk Enterprise y Splunk Cloud**, necesita enviar el archivo **XML de metadatos** descargado al [equipo de soporte técnico de Splunk Enterprise y Splunk Cloud](https://www.splunk.com/content/splunkcom/en_us/about-us/contact.html#tabs/customer-support).

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-splunk-enterprise-and-splunk-cloud-test-user"></a>Creación de un usuario de prueba de Splunk Enterprise y Splunk Cloud

En esta sección, creará un usuario llamado Britta Simon en Splunk Enterprise y Splunk Cloud. Trabaje con el [equipo de soporte técnico de Splunk Enterprise y Splunk Cloud](https://www.splunk.com/content/splunkcom/en_us/about-us/contact.html#tabs/customer-support) para agregar los usuarios a la plataforma de Splunk Enterprise y Splunk Cloud.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Splunk Enterprise y Splunk Cloud.

![Asignar usuario][200] 

**Para asignar Britta Simon a Splunk Enterprise y Splunk Cloud, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Splunk Enterprise y Splunk Cloud**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de SSO de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Splunk Enterprise y Splunk Cloud en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Splunk Enterprise y Splunk Cloud.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_203.png

