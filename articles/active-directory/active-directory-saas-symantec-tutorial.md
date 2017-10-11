---
title: "Tutorial: Integración de Azure Active Directory con Symantec Web Security Service (WSS) | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Symantec Web Security Service (WSS)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 61576d3a915d209e7355e04432e586dcf66e7c5a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Tutorial: Integración de Azure Active Directory con Symantec Web Security Service (WSS)

En este tutorial aprenderá cómo integrar su cuenta de Symantec Web Security Service (WSS) con su cuenta de Azure Active Directory (Azure AD) para que WSS pueda autenticar un usuario final que se aprovisiona en Azure AD mediante la autenticación de SAML y aplicar reglas de directiva de nivel de usuario o grupo.

La integración de Symantec Web Security Service (WSS) con Azure AD proporciona las siguientes ventajas:

- Administrar todos los usuarios finales y los grupos que usan la cuenta de WSS desde el portal de Azure AD. 

- Permitir que los usuarios finales se autentiquen en WSS con sus credenciales de Azure AD.

- Habilitar la aplicación de reglas de directiva de nivel de grupo y usuario definidas en la cuenta de WSS.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Symantec Web Security Service (WSS), necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una cuenta de Symantec Web Security Service (WSS)

> [!NOTE]
> Para probar los pasos de este tutorial, se recomienda no utilizar una cuenta de WSS que se esté usando actualmente en producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use una cuenta de WSS que se esté usando actualmente en producción para esta prueba a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, va a configurar Azure AD para habilitar el inicio de sesión único con WSS utilizando las credenciales de usuario final definidas en la cuenta de Azure AD.
La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregación de Symantec Web Security Service (WSS) desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Agregación de Symantec Web Security Service (WSS) desde la galería
Para configurar la integración de Symantec Web Security Service (WSS) en Azure AD, es preciso agregar Symantec Web Security Service (WSS) desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Symantec Web Security Service (WSS) desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Symantec Web Security Service (WSS)**, seleccione **Symantec Web Security Service (WSS)** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Symantec Web Security Service (WSS) en la lista de resultados](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Symantec Web Security Service (WSS) con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Symantec Web Security Service (WSS) para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Symantec Web Security Service (WSS).

Para establecer la relación de vínculo, en Symantec Web Security Service (WSS), asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Symantec Web Security Service (WSS), es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Symantec Web Security Service (WSS)](#create-a-symantec-web-security-service-wss-test-user)**: para tener un homólogo de Britta Simon en Symantec Web Security Service (WSS) que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Symantec Web Security Service (WSS).

**Para configurar el inicio de sesión único de Azure AD con Symantec Web Security Service (WSS), siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Symantec Web Security Service (WSS)**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

3. En la sección **Dominio y direcciones URL de Symantec Web Security Service (WSS)**, siga estos pasos:

    ![Información de dominio y direcciones URL de inicio de sesión único de Symantec Web Security Service (WSS)](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. En el cuadro de texto **URL de respuesta**, escriba la siguiente dirección URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Póngase en contacto con el [equipo de soporte técnico de Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) si los valores para el **identificador** y **dirección URL de respuesta** no funcionasen por algún motivo.

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-symantec-tutorial/tutorial_general_400.png)
    
6. Para configurar el inicio de sesión único en Symantec Web Security Service (WSS), consulte la documentación en línea de WSS. El archivo **XML de metadatos** descargado debe ser importado en el portal de WSS. Póngase en contacto con el [equipo de soporte técnico de Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) si necesita ayuda con la configuración en el portal de WSS.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-symantec-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-symantec-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-symantec-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-symantec-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>Creación de un usuario de prueba de Symantec Web Security Service (WSS)

En esta sección, creará un usuario llamado Britta Simon en Symantec Web Security Service (WSS). El nombre de usuario final correspondiente se puede crear manualmente en el portal de WSS o puede esperar a que los usuarios y grupos que se han aprovisionado en Azure AD se sincronicen con el portal de WSS transcurridos unos minutos (unos 15 minutos). Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. La dirección IP pública del equipo del usuario final, que se utilizará para examinar sitios web, también debe aprovisionarse en el portal de Symantec Web Security Service (WSS).

> [!NOTE]
> [Haga clic aquí](http://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) para obtener la dirección IP pública del equipo.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Symantec Web Security Service (WSS).

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Symantec Web Security Service (WSS), siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Symantec Web Security Service (WSS)**.

    ![Vínculo a Symantec Web Security Service (WSS) en la lista de aplicaciones](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

Ahora que ha configurado su cuenta de WSS para usar Azure AD para la autenticación de SAML, en esta sección, probará la funcionalidad de inicio de sesión único.

Después de haber configurado el explorador web para canalizar el tráfico hacia WSS, cuando se abra el explorador web y se intente ir a un sitio, se le redirigirá a la página de inicio de sesión de Azure. Escriba las credenciales del usuario final de prueba que se ha aprovisionado en Azure AD (es decir, BrittaSimon) y la contraseña asociada. Una vez autenticado, podrá visitar el sitio web que eligió. Debe crear una regla de directiva en WSS para bloquear la exploración de un sitio determinado a BrittaSimon y, a continuación, debería ver la página de bloqueo de WSS al intentar ir a ese sitio como el usuario BrittaSimon.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_203.png

