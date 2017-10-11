---
title: "Tutorial: Integración de Azure Active Directory con Qlik Sense Enterprise | Microsoft Doc"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Qlik Sense Enterprise."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: 4964634cd5aaf0dbb98c766f5e12700c4d118750
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Tutorial: Integración de Azure Active Directory con Qlik Sense Enterprise

En este tutorial, aprenderá a integrar Qlik Sense Enterprise con Azure Active Directory (Azure AD).

La integración de Qlik Sense Enterprise con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Qlik Sense Enterprise.
- Puede permitir que los usuarios inicien sesión automáticamente en Qlik Sense Enterprise (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Qlik Sense Enterprise, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Qlik Sense Enterprise

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, aquí puede obtener una versión de evaluación de un mes: [Oferta de prueba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Qlik Sense Enterprise desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Adición de Qlik Sense Enterprise desde la galería
Para configurar la integración de Qlik Sense Enterprise en Azure AD, deberá agregar esta solución desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Qlik Sense Enterprise desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Qlik Sense Enterprise**, seleccione **Qlik Sense Enterprise** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Qlik Sense Enterprise en la lista de resultados](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Qlik Sense Enterprise utilizando un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Qlik Sense Enterprise para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Qlik Sense Enterprise.

Para establecer la relación de vínculo, en Qlik Sense Enterprise, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Qlik Sense Enterprise, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Qlik Sense Enterprise](#create-a-qlik-sense-enterprise-test-user)**: para tener un homólogo de Britta Simon en Qlik Sense Enterprise que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Qlik Sense Enterprise.

**Para configurar el inicio de sesión único de Azure AD con Qlik Sense Enterprise, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Qlik Sense Enterprise** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. En la sección **Dominio y direcciones URL de Qlik Sense Enterprise**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Qlik Sense Enterprise](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<Qlik Sense Fully Qualifed Hostname>:443//samlauthn/`.
    
    > [!NOTE]
    > No se olvide de escribir la barra diagonal al final de este URI, ya que es obligatorio.
    
    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón:
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y dirección URL de inicio de sesión reales, que se explican más adelante en el tutorial o póngase en contacto con el [equipo de soporte técnico de Qlik Sense Enterprise](https://www.qlik.com/us/services/support) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Prepare el archivo XML de metadatos de federación para que pueda cargarse en el servidor de Qlik Sense.
   
    > [!NOTE]
    > Antes de cargar los metadatos de IdP en el servidor de Qlik Sense, hay que editar el archivo para quitar información y, de este modo, garantizar que la conexión entre Azure AD y el servidor de Qlik se realice sin problemas.
    
    ![QlikSense][qs24]
   
    a. Abra el archivo FederationMetaData.xml que ha descargado de Azure Portal en un editor de texto.
   
    b. Busque el valor **RoleDescriptor**.  Hay cuatro entradas (dos pares de etiquetas de elementos de apertura y cierre).
   
    c. Elimine del archivo las etiquetas de RoleDescriptor y toda la información intermedia.
   
    d. Guarde el archivo y téngalo a mano, ya que vamos a utilizarlo más adelante en este documento.

7. Acceda a Qlik Sense Qlik Management Console (QMC) como un usuario que pueda crear configuraciones de proxy virtual.

8. En la consola QMC, haga clic en el elemento de menú **Virtual Proxies** (servidores proxy virtuales).
   
    ![QlikSense][qs6] 

9. Haga clic en el botón **Create new** (Crear nuevo) situado en la parte inferior de la pantalla.
   
    ![QlikSense][qs7]

10. Aparece la pantalla de edición Virtual Proxy (Proxy virtual).  En la parte derecha de la pantalla hay un menú con el que puede hacer que se muestren las opciones de configuración.
   
    ![QlikSense][qs9]

11. Active la opción de menú Identificación y escriba la información de identificación de la configuración de proxy virtual de Azure.
    
    ![QlikSense][qs8]  
    
    a. El campo **Description** (Descripción) es un nombre descriptivo de la configuración del proxy virtual.  Escriba un valor para este campo.
    
    b. El campo **Prefix** (Prefijo) identifica el punto de conexión del proxy virtual para conectarse a Qlik Sense con el inicio de sesión único de Azure AD.  Escriba un nombre de prefijo único para este proxy virtual.
    
    c. **Session inactivity timeout (minutes)** (Tiempo de espera de inactividad de la sesión [minutos]) es el tiempo de espera de las conexiones que se establecen a través de este proxy virtual.
    
    d. **Session cookie header name** (Nombre de encabezado de cookie de sesión) es el nombre de la cookie que almacena el identificador de la sesión de Qlik Sense que recibe un usuario tras autenticarse correctamente.  Este nombre debe ser único.

12. Haga clic en la opción de menú Autenticación para que se muestre.  Aparecerá la pantalla Autenticación.
    
    ![QlikSense][qs10]
    
    a. El menú desplegable **Anonymous access mode** (Modo de acceso anónimo) determina si los usuarios anónimos pueden acceder a Qlik Sense a través del proxy virtual.  La opción predeterminada es No anonymous user (Ningún usuario anónimo).
    
    b. El menú desplegable **Authentication method** (Método de autenticación) determina el esquema de autenticación que utilizará el proxy virtual.  Seleccione SAML en la lista desplegable.  Como resultado, aparecerán más opciones.
    
    c. En el campo **SAML host URI**(identificador URI de host SAML), especifique el nombre de host que tendrán que escribir los usuarios para acceder a Qlik Sense a través de este proxy virtual de SAML.  El nombre de host es el URI del servidor de Qlik Sense.
    
    d. En el campo **SAML entity ID** (Id. de entidad SAML), escriba el mismo valor que especificó en el campo de URI de host SAML.
    
    e. **SAML IdP metadata** (Metadatos de IdP de SAML) es el archivo que se editó anteriormente en la sección de **modificación de los metadatos de federación desde la configuración de Azure AD**.  **Antes de cargar los metadatos de IdP, hay que editar el archivo** para quitar información y, de este modo, garantizar que la conexión entre Azure AD y el servidor de Qlik se realice sin problemas.  **Consulte las instrucciones anteriores si aún no ha modificado el archivo.**  Si ya lo ha hecho, haga clic en el botón Examinar y seleccione el archivo de metadatos editado para cargarlo en la configuración de proxy virtual.
    
    f. Escriba el nombre de atributo o la referencia de esquema del atributo SAML que representa el **UserID** (identificador de usuario) que Azure AD enviará al servidor de Qlik Sense.  La información de referencia del esquema está disponible en las pantallas de la aplicación de Azure que aparecen tras el proceso de configuración.  Para usar el atributo name, escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    g. Escriba el valor del **directorio de usuarios** que se asociará a los usuarios cuando se autentiquen en el servidor de Qlik Sense a través de Azure AD.  Los valores codificados deben estar entre **corchetes**.  Para utilizar un atributo enviado en la aserción SAML de Azure AD, escriba el nombre del atributo en este cuadro de texto **sin** corchetes.
    
    h. El **algoritmo de firma SAML** establece la firma de certificados del proveedor de servicios (en este caso, el servidor de Qlik Sense) en la configuración de proxy virtual.  Si el servidor de Qlik Sense utiliza un certificado de confianza generado mediante el proveedor de servicios criptográficos AES y RSA mejorado de Microsoft, cambie el algoritmo de firma SAML a **SHA-256**.
    
    i. La sección de asignación de atributos SAML permite enviar otros atributos, como grupos, a Qlik Sense para utilizarse en reglas de seguridad.

13. Haga clic en la opción de menú **LOAD BALANCING** (Equilibrio de carga) para que se muestre.  Aparecerá la pantalla Equilibrio de carga.
    
    ![QlikSense][qs11]

14. Haga clic en el botón **Add new server node** (Agregar nuevo nodo de servidor), seleccione los nodos de motor a los que Qlik Sense enviará sesiones para equilibrar la carga y, luego, haga clic en el botón **Add** (Agregar).
    
    ![QlikSense][qs12]

15. Haga clic en la opción de menú Opciones avanzadas para que se muestre. Aparece la pantalla Opciones avanzadas.
    
    ![QlikSense][qs13]
    
    La lista blanca de Host identifica los nombres de host que se aceptan al conectarse al servidor de Qlik Sense.  **Escriba el nombre de host que especificarán los usuarios al conectarse al servidor de Qlik sentido.** El nombre de host es el mismo valor que el URI de host SAML, pero sin "https://".

16. Haga clic en el botón **Apply** (Aplicar).
    
    ![QlikSense][qs14]

17. Haga clic en Aceptar para aceptar el mensaje de advertencia que indica que se reiniciará el proxy vinculado al proxy virtual.
    
    ![QlikSense][qs15]

18. En la parte derecha de la pantalla, aparece el menú de elementos Asociado.  Haga clic en la opción de menú **Proxies** (Servidores proxy).
    
    ![QlikSense][qs16]

19. Aparecerá la pantalla Servidores proxy.  Haga clic en el botón **Link** (Vincular) de la parte inferior para vincular un proxy al proxy virtual.
    
    ![QlikSense][qs17]

20. Seleccione el nodo de proxy que admitirá esta conexión de proxy virtual y haga clic en el botón **Link** (Vincular).  Cuando termine el proceso de vinculación, el proxy aparecerá debajo de los servidores proxy asociados.
    
    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. Cuando pasen entre unos cinco y diez segundos, se mostrará el mensaje Actualizar QMC.  Haga clic en el botón **Refresh QMC** (Actualizar QMC).
    
    ![QlikSense][qs20]

22. Cuando se actualice la consola QMC, haga clic en el elemento de menú **Virtual proxies** (Servidores proxy virtuales). La nueva entrada SAML virtual proxy (Proxy virtual de SAML) se muestra en la tabla que aparece en la pantalla.  Haga clic en la entrada de proxy virtual.
    
    ![QlikSense][qs51]

23. En la parte inferior de la pantalla, se activará el botón Download SP metadata (Descargar metadatos de SP).  Haga clic en el botón **Download SP metadata** (Descargar metadatos de SP) para guardar los metadatos en un archivo.
    
    ![QlikSense][qs52]

24. Abra el archivo de metadatos de SP.  Observe las entradas **entityID** y **AssertionConsumerService**.  Estos valores son equivalentes a los de **Identificador** y **Dirección URL de inicio de sesión** de la configuración de la aplicación de Azure AD. Pegue estos valores en la sección **Dominio y direcciones URL de Qlik Sense Enterprise** en la configuración de la aplicación de Azure AD; si no coinciden, se deben reemplazar en el Asistente para configuración de aplicaciones de Azure AD.
    
    ![QlikSense][qs53]

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

   ![Botón Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

   ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

   ![Botón Agregar](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

   ![Cuadro de diálogo Usuario](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. En el cuadro **Nombre**, escriba **BrittaSimon**.

   b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

   c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

   d. Haga clic en **Crear**.
 
### <a name="create-a-qlik-sense-enterprise-test-user"></a>Creación de un usuario de prueba de Qlik Sense Enterprise

En esta sección, creará un usuario llamado "Britta Simon" en Qlik Sense Enterprise. Colabore con el [equipo de soporte técnico de Qlik Sense Enterprise](https://www.qlik.com/us/services/support) para agregar los usuarios en la plataforma Qlik Sense Enterprise. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Qlik Sense Enterprise.

![Asignación del rol de usuario][200] 

**Para asignar el usuario Britta Simon a Qlik Sense Enterprise, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Qlik Sense Enterprise**.

    ![Vínculo a Qlik Sense Enterprise en la lista de aplicaciones](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Qlik Sense Enterprise del panel de acceso, debería iniciar sesión automáticamente en la aplicación Qlik Sense Enterprise. 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

