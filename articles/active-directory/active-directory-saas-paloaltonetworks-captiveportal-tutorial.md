---
title: "Tutorial: Integración de Azure Active Directory con Palo Alto Networks - Captive Portal | Microsoft Docs"
description: "Obtenga más información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Palo Alto Networks - Captive Portal."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: 32b3a45d9baf0b53e05708abab78a462c6780daa
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Tutorial: Integración de Azure Active Directory con Palo Alto Networks - Captive Portal

En este tutorial obtendrá más información sobre cómo integrar Palo Alto Networks - Captive Portal con Azure Active Directory (Azure AD).

La integración de Palo Alto Networks - Captive Portal con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Palo Alto Networks - Captive Portal.
- Puede permitir que los usuarios inicien sesión automáticamente en Palo Alto Networks - Captive Portal (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Palo Alto Networks - Captive Portal, se necesita lo siguiente:

- Una suscripción de Azure AD
- Una suscripción con inicio de sesión único de Palo Alto Networks - Captive Portal

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar Palo Alto Networks - Captive Portal desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Agregar Palo Alto Networks - Captive Portal desde la galería
Para configurar la integración de Palo Alto Networks - Captive Portal en Azure AD, es preciso agregar Palo Alto Networks - Captive Portal desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Palo Alto Networks - Captive Portal desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Palo Alto Networks - Captive Portal**, seleccione **Palo Alto Networks - Captive Portal** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Palo Alto Networks - Captive Portal en la lista de resultados](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Palo Alto Networks - Captive Portal con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Palo Alto Networks - Captive Portal para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Palo Alto Networks - Captive Portal.

Para establecer la relación de vínculo, en Palo Alto Networks - Captive Portal, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Palo Alto Networks - Captive Portal, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Palo Alto Networks - Captive Portal](#create-a-palo-alto-networks---captive-portal-test-user)**: para tener un homólogo de Britta Simon en Palo Alto Networks - Captive Portal que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Palo Alto Networks - Captive Portal.

**Para configurar el inicio de sesión único de Azure AD con Palo Alto Networks - Captive Portal, siga estos pasos:**

1. En la página de integración de la aplicación **Palo Alto Networks - Captive Portal** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_samlbase.png)

3. En la sección **Dominio y direcciones URL de Palo Alto Networks - Captive Portal**, siga estos pasos:

    ![Información de inicio de sesión único con el dominio y las direcciones URL de Palo Alto Networks - Captive Portal](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<Customer Firewall Hostname>/SAML20/SP`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<Customer Firewall Hostname>/SAML20/SP/ACS`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico del cliente de Palo Alto Networks - Captive Portal](https://support.paloaltonetworks.com/support) para obtener estos valores.

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_400.png)

6. Abra el sitio de Palo Alto como administrador en otra ventana del explorador.

7. Haga clic en **Dispositivo**.

    ![Configuración del inicio de sesión único de Palo Alto](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

8. Seleccione **Proveedor de identidades SAML** en la barra de navegación izquierda y haga clic en "Importar" para importar el archivo de metadatos.

    ![Configuración del inicio de sesión único de Palo Alto](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

9. Realice las siguientes acciones en la ventana Importar:

    ![Configuración del inicio de sesión único de Palo Alto](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. En el cuadro de texto **Nombre de perfil**, proporcione un nombre, por ejemplo, Azure AD Admin UI.
    
    b. En **Metadatos del proveedor de identidades**, haga clic en **Examinar** y seleccione el archivo metadata.xml que ha descargado de Azure Portal.
    
    c. Haga clic en **Aceptar**

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
  
### <a name="create-a-palo-alto-networks---captive-portal-test-user"></a>Creación de un usuario de prueba de Palo Alto Networks - Captive Portal

El objetivo de esta sección es crear un usuario llamado Britta Simon en Palo Alto Networks - Captive Portal. Palo Alto Networks - Captive Portal admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a Palo Alto Networks - Captive Portal, se creará un nuevo usuario, en caso de que no exista. 

> [!NOTE]
> Si necesita crear manualmente un usuario, es preciso que se ponga en contacto con el [equipo de soporte técnico de Palo Alto Networks - Captive Portal](https://support.paloaltonetworks.com/support).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para usar el inicio de sesión único de Azure al concederle acceso a Palo Alto Networks - Captive Portal.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Palo Alto Networks - Captive Portal, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Palo Alto Networks - Captive Portal**.

    ![Vínculo a Palo Alto Networks - Captive Portal en la lista de aplicaciones](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

La configuración de Captive Portal se realiza tras el firewall de la máquina virtual de Windows.  Para probar el inicio de sesión único en Captive Portal, inicie sesión en la máquina virtual de Windows mediante RDP. En la sesión RDP, abra un explorador para acceder a cualquier sitio web. El explorador abrirá automáticamente la dirección URL de inicio de sesión único (SSO) y solicitará la autenticación. Una vez completada la autenticación, podrá navegar por los sitios web. 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_203.png

