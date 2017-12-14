---
title: "Tutorial: Integración de Azure Active Directory con SAP HANA| Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAP HANA."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 855525e2c1d3c33cc7134bbc1cd9b53ca59e1a70
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Tutorial: Integración de Azure Active Directory con SAP HANA

En este tutorial, obtendrá información sobre cómo integrar SAP HANA con Azure Active Directory (Azure AD).

La integración de SAP HANA con Azure AD le proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a SAP HANA.
- Puede permitir que los usuarios inicien sesión automáticamente en SAP HANA con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SAP HANA, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de SAP HANA habilitada para el inicio de sesión único (SSO)
- Una instancia de HANA en ejecución en cualquier IaaS pública, instancias locales, VM de Azure o instancias grandes de SAP en Azure
- La interfaz web de administración de XSA, así como HANA Studio instalados en la instancia HANA

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción de SAP HANA. Pruebe primero la integración en el entorno de desarrollo o de ensayo de la aplicación y, después, use el entorno de producción.

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener [un versión de evaluación gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de SAP HANA desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-sap-hana-from-the-gallery"></a>Adición de SAP HANA desde la galería
Para configurar la integración de SAP HANA en Azure AD, agregue SAP HANA desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SAP HANA desde la galería, realice los pasos siguientes:**

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), seleccione el icono **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar la nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **SAP HANA**. A continuación, seleccione **SAP HANA** en el panel de resultados. Por último, haga clic en el botón **Agregar** para agregar la aplicación. 

    ![Nueva aplicación](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con SAP HANA con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de SAP HANA para un usuario de Azure AD. Es decir, es necesario establecer un vínculo entre un usuario de Azure AD y el usuario relacionado de SAP HANA.

En SAP HANA, asigne al valor **Nombre de usuario** el mismo valor de **nombre de usuario** en Azure AD. Este paso establece el vínculo entre los dos usuarios.

Para configurar y probar el inicio de sesión único de Azure AD con SAP HANA, complete los siguientes bloques de creación:

1. [Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on), para permitir que los usuarios utilicen esta característica.
2. [Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user), para probar el inicio de sesión único de Azure AD con Britta Simon.
3. [Creación de un usuario de prueba de SAP HANA](#creating-a-sap-hana-test-user): el objetivo es tener un homólogo de Britta Simon en SAP HANA que esté vinculado a la representación del usuario en Azure AD.
4. [Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user), para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. [Prueba del inicio de sesión único](#testing-single-sign-on), para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación SAP HANA.

**Para configurar el inicio de sesión único de Azure AD con SAP HANA, siga estos pasos:**

1. En la página de integración de la aplicación **SAP HANA** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Inicio de sesión basado en SAML**, seleccione **Modo**.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. En la sección **Dominio y direcciones URL de SAP HANA**, lleve a cabo los pasos siguientes:

    ![Información sobre dominio y direcciones URL de inicio de sesión único](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. En el cuadro **Identificador**, escriba lo siguiente: `HA100` 

    b. En el cuadro **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico al cliente de SAP HANA](https://cloudplatform.sap.com/contact.html) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, seleccione **XML de metadatos**. Luego, guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Si el certificado no está activo, actívelo haciendo clic en la casilla **Make new certificate active** (Activar nuevo certificado) en Azure AD. 

5. La aplicación SAP HANA espera las aserciones de SAML en un formato específico. La siguiente captura de pantalla le muestra un ejemplo de este formato. 

    Aquí hemos asignado el **identificador de usuario** con la función **ExtractMailPrefix()** de **user.mail**. Esto da como resultado el valor de prefijo del correo electrónico del usuario, que es el identificador de usuario único. Este identificador de usuario se envía a la aplicación SAP HANA con cada respuesta correcta.

    ![Configurar inicio de sesión único](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, siga los pasos siguientes:

    a. En la lista desplegable **Identificador de usuario**, seleccione **ExtractMailPrefix**.
    
    b. En la lista desplegable **Correo**, seleccione **user.mail**.

7. Seleccione el botón **Guardar**.

    ![Botón Guardar de configuración de inicio de sesión único](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. Para configurar el inicio de sesión único en SAP HANA, inicie sesión en la **consola web de HANA XSA** en el punto de conexión HTTPS respectivo.

    > [!NOTE]
    > En la configuración predeterminada, la dirección URL redirige la solicitud a una pantalla de inicio de sesión, que requiere las credenciales de un usuario de base de datos de SAP HANA autenticado. El usuario que inicie sesión debe tener los permisos para realizar las tareas de administración de SAML.

9. En la interfaz web de XSA, vaya al **proveedor de identidades SAML**. A partir de ahí, seleccione el botón **+** en la parte inferior de la pantalla para mostrar el panel **Add Identity Provider Info** (Agregar información del proveedor de identidades). A continuación, siga estos pasos:

    ![Agregación del proveedor de identidades](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. En el panel **Add Identity Provider Info** (Agregar información del proveedor de identidades), pegue el contenido del archivo XML de metadatos, que descargó desde Azure Portal, en el cuadro **Metadatos**.

    ![Adición de la configuración del proveedor de identidades](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. Si el contenido del documento XML es válido, el proceso de análisis extrae la información necesaria para los campos **Asunto, Id. de entidad y Emisor** del área de pantalla **Datos generales**. También extrae la información necesaria para los campos de dirección URL de la área de pantalla **Destino** como, por ejemplo, los campos **URL base y Dirección URL de inicio de sesión único (*)**.

    ![Adición de la configuración del proveedor de identidades](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. En el cuadro **Nombre** del área de pantalla **Datos generales**, escriba un nombre para el nuevo proveedor de identidades de SSO de SAML.

    > [!NOTE]
    > El nombre del proveedor de identidades de SAML es obligatorio y debe ser único. Aparece en la lista de proveedores de identidades de SAML disponibles que se muestra cuando se selecciona SAML como método de autenticación que debe usarse para aplicaciones SAP HANA XS. Por ejemplo, puede hacer eso en el área de pantalla **Autenticación** de la herramienta XS Artifact Administration.

10. Seleccione **Guardar** para guardar los detalles del proveedor de identidades de SAML y para agregar el nuevo proveedor de identidades de SAML a la lista de conocidos.

    ![Botón Guardar](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. En HANA Studio, en las propiedades del sistema de la pestaña **Configuración**, filtre las opciones de configuración por **saml**. A continuación, ajuste **assertion_timeout** de **10 s** a **120 s**.

    ![assertion_timeout setting](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación. Después de agregar esta aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, seleccione la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada en el artículo sobre la [documentación insertada de Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de **Azure Portal**, seleccione el icono **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos**. Después, seleccione **Todos los usuarios**.
    
    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. En la parte superior del cuadro de diálogo, seleccione **Agregar** para abrir el cuadro de diálogo **Usuario**.
 
    ![Botón Agregar](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. En el cuadro de diálogo **Usuario**, realice los pasos siguientes:
 
    ![Cuadro de diálogo Usuario](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote la contraseña.

    d. Seleccione **Crear**.
 
### <a name="create-a-sap-hana-test-user"></a>Creación de un usuario de prueba en SAP HANA

Para permitir que los usuarios de Azure AD inicien sesión en SAP HANA, debe aprovisionarlos en SAP HANA.
SAP HANA admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

Si tiene que crear manualmente un usuario, siga los pasos siguientes:

>[!NOTE]
>Puede cambiar la autenticación externa que usa el usuario. Pueden autenticarse con un sistema externo, como Kerberos. Para obtener información detallada acerca de las identidades externas, póngase en contacto con su [administrador de dominio](https://cloudplatform.sap.com/contact.html).

1. Abra [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) como administrador y, después, active el usuario de base de datos de SSO de SAML.

    ![Crear usuario](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. Seleccione la casilla invisible a la izquierda de **SAML** y, después, seleccione el vínculo **Configurar**.

3. Seleccione **Agregar** para agregar el proveedor de identidades de SAML.  Seleccione el proveedor de identidades de SAML adecuado y, después, seleccione **Aceptar**.

4. Agregue la **Identidad externa** (en este caso, BrittaSimon) o elija **Cualquiera**. Después seleccione **Aceptar**.

    >[!Note]
    >Si no está seleccionada la casilla **Cualquiera**, el nombre de usuario en HANA debe coincidir exactamente con el nombre del usuario en UPN antes del sufijo de dominio. (Por ejemplo, BrittaSimon@contoso.com se convierte en BrittaSimon en HANA.)

5. Para realizar pruebas, asigne todos los roles de **XS** para el usuario.

    ![Asignación de roles](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > Debe conceder permisos adecuados solo para los casos de uso.

6. Guarde el usuario.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a SAP HANA.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a SAP HANA, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones. Vaya a **Aplicaciones empresariales** en la vista de directorio. Seleccione **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SAP HANA**.

    ![Asignar usuario](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. En el menú de la izquierda, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202] 

4. Seleccione el botón **Agregar**. En el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista **Usuarios**.

6. En el cuadro de diálogo **Usuarios y grupos**, haga clic en el botón **Seleccionar**.

7. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el panel de acceso.

Al seleccionar el icono de SAP HANA en el panel de acceso, debería iniciar sesión automáticamente en la aplicación SAP HANA.
Para más información sobre el panel de acceso, consulte [Introducción al panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_203.png

