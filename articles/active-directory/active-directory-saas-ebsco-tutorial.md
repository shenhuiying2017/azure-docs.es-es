---
title: "Tutorial: Integración de Azure Active Directory con EBSCO | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y EBSCO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.openlocfilehash: ea7fe09c31d88cf2095b3a3777b6b1f9feb8df46
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Tutorial: Integración de Azure Active Directory con EBSCO

En este tutorial, aprenderá a integrar EBSCO con Azure Active Directory (Azure AD).

La integración de EBSCO con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a EBSCO.
- Puede permitir que los usuarios inicien sesión automáticamente en EBSCO (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con EBSCO, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en EBSCO

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de EBSCO desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-ebsco-from-the-gallery"></a>Adición de EBSCO desde la galería
Para configurar la integración de EBSCO en Azure AD, será preciso agregar EBSCO desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar EBSCO desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **EBSCO**, seleccione **EBSCO** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![EBSCO en la lista de resultados](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con EBSCO con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de EBSCO para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de EBSCO.

Para configurar y probar el inicio de sesión único de Azure AD con EBSCO, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de EBSCO](#create-an-ebsco-test-user)**: se puede automatizar el aprovisionamiento o la personalización de usuarios de EBSCOhost. EBSCO admite el aprovisionamiento de usuarios Just-In-Time.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación EBSCO.

**Para configurar el inicio de sesión único de Azure AD con EBSCO, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de aplicaciones **EBSCO**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_samlbase.png)

3. En la sección **Dominio y direcciones URL de EBSCO**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de EBSCO](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_url.png)

    En el cuadro de texto **Identificador**, escriba una dirección URL como: `pingsso.ebscohost.com`

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de EBSCO](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`.
     
    > [!NOTE] 
    > El valor de la dirección URL de inicio de sesión no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de atención al cliente de EBSCO](mailto:sso@ebsco.com) para obtener el valor. 

    o   **Elementos únicos:**  

    o   **Custid** = escriba el identificador de cliente único de EBSCO 

    o   **Profile** = los clientes pueden personalizar el vínculo para dirigir a los usuarios a un perfil específico (en función de lo que compran en EBSCO). Pueden especificar un identificador de perfil específico. Los identificadores principales son eds (servicio de detección de EBSCO) y ehost (bases de datos EBSOCOhost). Se proporcionan [aquí](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile) instrucciones.

5. La aplicación EBSCO espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_attribute.png)

    > [!Note]
    > El atributo **name** es obligatorio y se asigna con el **identificador de usuario** en la aplicación EBSCO. Se agrega de forma predeterminada, por lo que no es necesario agregarla manualmente.
    
6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo | Valor de atributo |
    | ---------------| --------------- |    
    | Nombre   | user.givenname |
    | Apellidos   | user.surname |
    | Email   | user.mail |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-ebsco-tutorial/tutorial_officespace_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-ebsco-tutorial/tutorial_attribute_05.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.

7. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_certificate.png) 

8. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-ebsco-tutorial/tutorial_general_400.png)
    
9. Para configurar el inicio de sesión único en **EBSCO**, debe enviar el archivo **XML de metadatos** descargado al [equipo de soporte técnico de EBSCO](mailto:sso@ebsco.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-ebsco-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-ebsco-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-ebsco-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-ebsco-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-an-ebsco-test-user"></a>Creación de un usuario de prueba de EBSCO

En el caso de EBSCO, el aprovisionamiento de usuarios es automática.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

Azure AD pasa los datos necesarios a la aplicación de EBSCO. El aprovisionamiento de usuarios de EBSCO puede ser automático O requerir un formulario de un solo uso. Depende de si el cliente tiene muchas cuentas EBSCOhost preexistentes con configuraciones personales guardadas. Esto mismo puede analizarse con el [equipo de soporte técnico de EBSCO](mailto:sso@ebsco.com) durante la implementación. En cualquier caso, el cliente no tiene que crear las cuentas de EBSCOhost antes de la prueba.

   >[!Note]
   >Puede automatizar el aprovisionamiento o la personalización de usuarios de EBSCOhost. Póngase en contacto con el [equipo de soporte técnico de EBSCO](mailto:sso@ebsco.com) sobre el aprovisionamiento de usuarios Just-In-Time. 
 
### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a EBSCO.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a EBSCO, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **EBSCO**.

    ![Vínculo a EBSCO en la lista de aplicaciones](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

1. Al hacer clic en el icono de EBSCO en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación EBSCO.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

2. Cuando inicie sesión en la aplicación, haga clic en el botón **Iniciar sesión** en la esquina superior derecha.

    ![Inicio de sesión en EBSCO en la lista de aplicaciones](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Recibirá un aviso único para emparejar el inicio de sesión institucional o de SAML y **enlazar la cuenta MyEBSCOhost existente a la cuenta de su institución ahora** O BIEN para **crear una nueva cuenta MyEBSCOhost y vincularla a la cuenta de su institución**. La cuenta se utiliza para la personalización en la aplicación EBSCOhost. Seleccione la opción **Crear una cuenta** y verá que el formulario de personalización está completado previamente con los valores de la respuesta saml, tal como se muestra en la captura de pantalla siguiente. Haga clic en **'Continuar'** para guardar esta selección.
    
     ![Usuario de EBSCO en la lista de aplicaciones](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_user.png)

4. Después de completar la instalación anterior, limpie las cookies y la memoria caché y vuelva a iniciar sesión. No tendrá que iniciar sesión manualmente de nuevo ya que la configuración de personalización se recuerda.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_203.png

