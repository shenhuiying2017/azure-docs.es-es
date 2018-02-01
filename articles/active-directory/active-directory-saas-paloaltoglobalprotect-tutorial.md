---
title: "Tutorial: Integración de Azure Active Directory con Palo Alto Networks - GlobalProtect | Microsoft Docs"
description: "Obtenga más información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Palo Alto Networks - GlobalProtect."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: c851c056095ef8e7fb40d7d2ead76457495b2118
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---globalprotect"></a>Tutorial: Integración de Azure Active Directory con Palo Alto Networks - GlobalProtect

Con este tutorial obtendrá más información sobre cómo integrar Palo Alto Networks - GlobalProtect con Azure Active Directory (Azure AD).

La integración de Palo Alto Networks - GlobalProtect con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Palo Alto Networks - GlobalProtect.
- Puede permitir que los usuarios inicien sesión automáticamente en Palo Alto Networks - GlobalProtect (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Palo Alto Networks - GlobalProtect, se necesita lo siguiente:

- Una suscripción de Azure AD
- Una suscripción con inicio de sesión único de Palo Alto Networks - GlobalProtect

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregar Palo Alto Networks - GlobalProtect desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Agregar Palo Alto Networks - GlobalProtect desde la galería
Para configurar la integración de Palo Alto Networks - GlobalProtect en Azure AD, es preciso agregar Palo Alto Networks - GlobalProtect desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Palo Alto Networks - GlobalProtect desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Palo Alto Networks - GlobalProtect**, seleccione **Palo Alto Networks - GlobalProtect** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Palo Alto Networks - GlobalProtect en la lista de resultados](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Palo Alto Networks - GlobalProtect con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Palo Alto Networks - GlobalProtect para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Palo Alto Networks - GlobalProtect.

Para establecer la relación de vínculo, en Palo Alto Networks - GlobalProtect, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Palo Alto Networks - GlobalProtect, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Palo Alto Networks - GlobalProtect](#create-a-palo-alto-networks---globalprotect-test-user)**: para tener un homólogo de Britta Simon en Palo Alto Networks - GlobalProtect que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Palo Alto Networks - GlobalProtect.

**Para configurar el inicio de sesión único de Azure AD con Palo Alto Networks - GlobalProtect, siga estos pasos:**

1. En la página de integración de la aplicación **Palo Alto Networks - GlobalProtect** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_samlbase.png)

3. En la sección **Dominio y direcciones URL de Palo Alto Networks - GlobalProtect**, siga estos pasos:

    ![Información de inicio de sesión único con el dominio y las direcciones URL de Palo Alto Networks - GlobalProtect](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<Customer Firewall URL>`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico del cliente de Palo Alto Networks - GlobalProtect](https://support.paloaltonetworks.com/support) para obtener estos valores. 
 
4. La aplicación Palo Alto Networks - GlobalProtect espera las aserciones de SAML en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_attribute.png)
    
5. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo | Valor de atributo |
    | --- | --- |    
    | Nombre de usuario | user.userprincipalname |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_attribute_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_attribute_05.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila. Hemos asignado el valor con user.userprincipalname como ejemplo, pero puede realizar la asignación con el valor que necesite. 
    
    d. Haga clic en **Aceptar**.


6. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_certificate.png) 

7. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_400.png)

8. Abra la interfaz de usuario de administración de firewalls de Palo Alto Networks como administrador en otra ventana del explorador.

9. Haga clic en **Dispositivo**.

    ![Configuración del inicio de sesión único de Palo Alto](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

10. Seleccione **Proveedor de identidades SAML** en la barra de navegación izquierda y haga clic en "Importar" para importar el archivo de metadatos.

    ![Configuración del inicio de sesión único de Palo Alto](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

11. Realice las siguientes acciones en la ventana Importar:

    ![Configuración del inicio de sesión único de Palo Alto](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. En el cuadro de texto **Profile Name** (Nombre de perfil), proporcione un nombre, por ejemplo, Azure AD GlobalProtect.
    
    b. En **Metadatos del proveedor de identidades**, haga clic en **Examinar** y seleccione el archivo metadata.xml que ha descargado de Azure Portal.
    
    c. Haga clic en **Aceptar**

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-paloaltoglobalprotect-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-paloaltoglobalprotect-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-paloaltoglobalprotect-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-paloaltoglobalprotect-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-palo-alto-networks---globalprotect-test-user"></a>Creación de un usuario de prueba de Palo Alto Networks - GlobalProtect

Palo Alto Networks - GlobalProtect admite el aprovisionamiento de usuarios Just-In-Time, de manera que, si aún no existe, se creará automáticamente un usuario en el sistema después de una autenticación correcta. No es necesario que realice ninguna acción aquí. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para usar el inicio de sesión único de Azure al concederle acceso a Palo Alto Networks - GlobalProtect.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Palo Alto Networks - GlobalProtect, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Palo Alto Networks - GlobalProtect**.

    ![Vínculo a Palo Alto Networks - GlobalProtect en la lista de aplicaciones](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Palo Alto Networks - GlobalProtect en el Panel de acceso, se iniciará sesión automáticamente en la aplicación Palo Alto Networks - GlobalProtect.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_203.png

