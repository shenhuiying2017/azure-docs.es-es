---
title: "Tutorial: Integración de Azure Active Directory con el Portal de administración en la nube de Microsoft Azure | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y el Portal de administración en la nube de Microsoft Azure."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 5eabff9399f35328feb973ad4a0abbf793fe7458
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Tutorial: Integración de Azure Active Directory con el Portal de administración en la nube de Microsoft Azure

En este tutorial, aprenderá a integrar Cloud Management Portal for Microsoft Azure con Azure Active Directory (Azure AD).

Integrar el Portal de administración en la nube de Microsoft Azure con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso al Portal de administración en la nube de Microsoft Azure.
- Puede permitir que los usuarios inicien sesión automáticamente en el Portal de administración en la nube para Microsoft Azure (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con el Portal de administración en la nube de Microsoft Azure, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Un Portal de administración en la nube para suscripciones con inicio de sesión único habilitado de Microsoft Azure

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación del Portal de administración en la nube de Microsoft Azure desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Incorporación del Portal de administración en la nube de Microsoft Azure desde la galería
Para configurar la integración del Portal de administración en la nube de Microsoft Azure en Azure AD, debe agregar el Portal de administración en la nube de Microsoft Azure desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar el Portal de administración en la nube de Microsoft Azure desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Portal de administración en la nube de Microsoft Azure**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_search.png)

5. En el panel de resultados, seleccione **Cloud Management Portal for Microsoft Azure** y, luego, haga clic en **Completar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Cloud Management Portal for Microsoft Azure con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Cloud Management Portal for Microsoft Azure para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado del Portal de administración en la nube de Microsoft Azure.

Para establecer la relación de vínculo, en Cloud Management Portal for Microsoft Azure, asigne el valor del **nombre de usuario** de Azure AD como el valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con el Portal de administración en la nube de Microsoft Azure, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba para Cloud Management Portal for Microsoft Azure](#creating-a-cloud-management-portal-for-microsoft-azure-test-user)**: el objetivo es tener un homólogo de Britta Simon en Cloud Management Portal for Microsoft Azure que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Cloud Management Portal for Microsoft Azure.

**Para configurar el inicio de sesión único de Azure AD con el Portal de administración en la nube de Microsoft Azure, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Cloud Management Portal for Microsoft Azure** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_samlbase.png)

3. En la sección **Dominio y direcciones URL de Cloud Management Portal for Microsoft Azure**, realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: 
    
    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |
    
    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con los siguientes patrones: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE] 
    > Estos valores no son reales. Actualícelos con la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Póngase en contacto con el [equipo de atención al cliente de Cloud Management Portal for Microsoft Azure](mailto:jczernuszka@newsignature.com) para obtener estos valores. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-newsignature-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Cloud Management Portal for Microsoft Azure**, haga clic en **Configurar Cloud Management Portal for Microsoft Azure** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_configure.png) 

7. Para configurar el inicio de sesión único en **Cloud Management Portal for Microsoft Azure**, debe enviar el **certificado** descargado, la **URL de cierre de sesión**, la **dirección URL del servicio de inicio de sesión único de SAML** y el **id. de entidad de SAML** al [equipo de soporte técnico de Cloud Management Portal for Microsoft Azure](mailto:jczernuszka@newsignature.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-cloud-management-portal-for-microsoft-azure-test-user"></a>Creación de un usuario de prueba para el Portal de administración en la nube de Microsoft Azure

El objetivo de esta sección es crear un usuario llamado Britta Simon en el Portal de administración en la nube de Microsoft Azure. Trabaje con el equipo de soporte técnico de [Cloud Management Portal for Microsoft Azure](mailto:jczernuszka@newsignature.com) para agregar los usuarios a la cuenta de Cloud Management Portal for Microsoft Azure.


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure, para lo que se le concederá acceso a Cloud Management Portal for Microsoft Azure.

![Asignar usuario][200] 

**Para asignar a Britta Simon al Portal de administración en la nube de Microsoft Azure, siga los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Portal de administración en la nube de Microsoft Azure**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.
Al hacer clic en el icono del Portal de administración en la nube de Microsoft Azure en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación del Portal de administración en la nube de Microsoft Azure.

Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_203.png

