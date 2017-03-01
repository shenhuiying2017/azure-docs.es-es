---
title: "Tutorial: Integración de Azure Active Directory con TeamSeer | Microsoft Docs"
description: "Aprenda cómo usar TeamSeer con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 49352a5e8255468bbc54c02e0cd9242d49002dbd
ms.openlocfilehash: 75bd875a4ca7bbfd4d5a1cd9ac6bde302430136a
ms.lasthandoff: 12/08/2016


---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Tutorial: integración de Azure Active Directory con TeamSeer
El objetivo de este tutorial es mostrar la integración de Azure y TeamSeer.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de TeamSeer

Después de completar este tutorial, los usuarios de Azure AD que asignó a TeamSeer podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de TeamSeer (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para TeamSeer
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-teamseer-tutorial/IC789618.png "Escenario")

## <a name="enabling-the-application-integration-for-teamseer"></a>Habilitación de la integración de aplicaciones para TeamSeer
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en TeamSeer.

### <a name="to-enable-the-application-integration-for-teamseer-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para TeamSeer:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Aplicaciones](./media/active-directory-saas-teamseer-tutorial/IC700994.png "Aplicaciones")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicaciones](./media/active-directory-saas-teamseer-tutorial/IC749321.png "Agregar aplicaciones")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-teamseer-tutorial/IC749322.png "Agregar una aplicación de la galería")

6. En el **cuadro de búsqueda**, escriba **TeamSeer**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-teamseer-tutorial/IC789619.png "Galería de aplicaciones")

7. En el panel de resultados, seleccione **TeamSeer** y, luego, haga clic en **Completa** para agregar la aplicación.
   
    ![TeamSeer](./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en TeamSeer con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base&64;.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **TeamSeer**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configurar inicio de sesión único")

2. En la página **¿Cómo desea que los usuarios inicien sesión en TeamSeer?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configurar inicio de sesión único")

3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de TeamSeer**, escriba su dirección URL con el siguiente patrón *http://www.teamseer.com/companyid* y luego haga clic en **Siguiente**.
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-teamseer-tutorial/IC789629.png "Configurar dirección URL de la aplicación")

4. En la página **Configuración de inicio de sesión único en TeamSeer**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configurar inicio de sesión único")

5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de TeamSeer como administrador.

6. Vaya a **Administrador de RR. HH.**.
   
    ![Administración de RR. HH.](./media/active-directory-saas-teamseer-tutorial/IC789634.png "Administración de RR. HH.")

7. Haga clic en **Configuración**.
   
    ![Instalación](./media/active-directory-saas-teamseer-tutorial/IC789635.png "Instalación")

8. Haga clic en **Configurar detalles del proveedor SAML**.
   
    ![Configuración de SAML](./media/active-directory-saas-teamseer-tutorial/IC789636.png "Configuración de SAML")

9. En la sección de detalles del proveedor SAML, lleve a cabo estos pasos:
   
    ![Configuración de SAML](./media/active-directory-saas-teamseer-tutorial/IC789637.png "Configuración de SAML")
   
    a. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en TeamSeer**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **URL**.
   
    b. Cree un archivo **codificado en base&64;** a partir del certificado descargado.  
      
    > [!TIP]
    > Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    c. Abra el certificado codificado en base&64; en el Bloc de notas, copie su contenido en el portapapeles y luego péguelo en el cuadro de texto **Certificado público de Id** .

10. Para completar la configuración del proveedor SAML, lleve a cabo estos pasos:
    
    ![Configuración de SAML](./media/active-directory-saas-teamseer-tutorial/IC789638.png "Configuración de SAML")
    
    a. En las **Direcciones de correo electrónico de prueba**, escriba la dirección de correo electrónico del usuario de prueba.
    
    b. En el cuadro de texto **Emisor** , escriba la dirección URL de emisor del proveedor de servicios.
    
    c. Haga clic en **Save**.

11. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configurar inicio de sesión único")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en TeamSeer, deben aprovisionarse en ShiftPlanning.  
En el caso de TeamSeer, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en su sitio de la compañía de **TeamSeer** como administrador.

2. Lleve a cabo los siguiente pasos:
   
    ![Administración de RR. HH.](./media/active-directory-saas-teamseer-tutorial/IC789640.png "Administración de RR. HH.")
   
    a. Vaya a **Administrador de RR. HH. \> Usuarios**.
   
    b. Haga clic en **Ejecutar el Asistente para nuevos usuarios**.

3. En la sección **Detalles del usuario** , lleve a cabo estos pasos:
   
   ![Detalles del usuario](./media/active-directory-saas-teamseer-tutorial/IC789641.png "Detalles del usuario")
   
    a. Escriba el **Nombre**, **Apellido**, **Nombre de usuario (dirección de correo electrónico)** de una cuenta válida de AAD que desee aprovisionar en los cuadros de texto relacionados.
  
    b. Haga clic en **Siguiente**.

4. Siga las instrucciones en pantalla para agregar un nuevo usuario y haga clic en **Finalizar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de TeamSeer ofrecida por TeamSeer para aprovisionar cuentas de usuario de Azure AD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-teamseer-perform-the-following-steps"></a>Para asignar usuarios a TeamSeer, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.

2. En la página de integración de la aplicación **TeamSeer**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-teamseer-tutorial/IC789642.png "Asignar usuarios")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-teamseer-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


