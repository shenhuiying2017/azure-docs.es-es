---
title: "Habilitación de Microsoft Windows Hello para empresas en la organización | Microsoft Docs"
description: "Instrucciones de implementación para habilitar Microsoft Passport en su organización."
services: active-directory
documentationcenter: 
keywords: "configurar Microsoft Passport, implementación de Windows Hello para empresas"
author: MarkusVi
manager: femila
tags: azure-classic-portal
ms.assetid: 7dbbe3c6-1cd7-429c-a9b2-115fcbc02416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: markvi
ms.openlocfilehash: 58943e1e29755c983e55c675dd4fe7b75ac47b34
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>Habilitación de Windows Hello para empresas en su organización
Tras [conectar dispositivos con Windows 10 unidos a un dominio mediante Azure Active Directory](active-directory-azureadjoin-devices-group-policy.md), lleve a cabo los siguientes pasos para habilitar Windows Hello para empresas en la organización.

1. Implementación de System Center Configuration Manager  
2. Establecer configuraciones de directiva
3. Configurar el perfil de certificado  

## <a name="deploy-system-center-configuration-manager"></a>Implementación de System Center Configuration Manager
Para implementar certificados de usuario según las claves de Windows Hello para empresas, necesita lo siguiente:

* **Rama actual de System Center Configuration Manager**: debe instalar la versión 1606 o superior. Para más información, consulte [Documentation for System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) (Documentación de System Center Configuration Manager) y [System Center Configuration Manager Team Blog](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx) (Blog del equipo de System Center Configuration Manager).
* **Infraestructura de clave pública (PKI)**: para habilitar Windows Hello para empresas mediante certificados de usuario, tiene que haber implantado una PKI. Si no tiene una o no desea usar certificados de usuario, puede implementar un nuevo controlador de dominio que tenga instalada Windows Server 2016 versión 10551 (o superior). Siga los pasos para [instalar un controlador de dominio de réplica en un dominio existente](https://technet.microsoft.com/library/jj574134.aspx) o [instalar un nuevo bosque de Active Directory, si está creando un nuevo entorno](https://technet.microsoft.com/library/jj574166). (Las archivos ISO están disponibles para su descarga en [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true)).

## <a name="configure-policy-settings"></a>Establecer configuraciones de directiva
Para establecer configuraciones de directiva de Microsoft Windows Hello para empresas, tiene dos opciones:

* Directiva de grupo en Active Directory 
* System Center Configuration Manager 

El uso de la directiva de grupo en Active Directory es el método recomendado para establecer la configuración de la directiva de Microsoft Windows Hello para empresas. 

El uso de System Center Configuration Manager es el método preferido cuando también se utiliza para implementar certificados. Este escenario:

* Garantiza la compatibilidad con los escenarios de implementación más recientes.
* Se requiere en Windows 10 versión 1607 o superior del lado cliente.

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Configuración de Windows Hello para empresas mediante directiva de grupo en Active Directory
**Pasos**:

1. Abra el Administrador del servidor y vaya a **Herramientas** > **Administración de directivas de grupo**.
2. En Administración de directivas de grupo, vaya al nodo de dominio que corresponde al dominio en el que desea habilitar Azure AD Join.
3. Haga clic con el botón derecho en **Objetos de directiva de grupo** y seleccione **Nuevo**. Asigne un nombre a su objeto de directiva de grupo; por ejemplo, Habilitar Windows Hello para empresas. Haga clic en **Aceptar**.
4. Haga clic con el botón derecho en el nuevo objeto de directiva de grupo y luego seleccione **Editar**.
5. Vaya a **Configuración del equipo** > **Directivas** > **Plantillas administrativas** > **Componentes de Windows** > **Windows Hello para empresas**.
6. Haga clic con el botón derecho en **Hablitar Windows Hello para empresas** y, después, seleccione **Editar**.
7. Seleccione el botón de opción **Habilitado** y, luego, haga clic en **Aplicar**. Haga clic en **Aceptar**.
8. Ahora puede vincular el objeto de directiva de grupo a la ubicación que elija. Para habilitar esta directiva para todos los dispositivos de Windows 10 unidos a un dominio en su organización, vincule la Directiva de grupo al dominio. Por ejemplo:
   * Una unidad organizativa (OU) específica en Active Directory donde se encontrarán los equipos unidos a un dominio de Windows 10.
   * Un grupo de seguridad específico que contiene equipos unidos a un dominio de Windows 10 que se registrarán automáticamente en Azure AD.

### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>Configuración de Windows Hello para empresas mediante System Center Configuration Manager
**Pasos**:

1. Abra **System Center Configuration Manager** y vaya a **Activos y compatibilidad > Configuración de cumplimiento > Acceso a los recursos de la compañía > Perfiles de Windows Hello para la empresa**.
   
    ![Configuración de Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/01.png)
2. En la barra de herramientas de la parte superior, haga clic en **Crear perfil de Windows Hello para la empresa**.
   
    ![Configuración de Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/02.png)
3. En el cuadro de diálogo **General** , realice los pasos siguientes:
   
    ![Configuración de Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/03.png)
   
    a. En el cuadro de texto **Nombre**, escriba un nombre para el perfil, como **Mi perfil de WHpE**.
   
    b. Haga clic en **Siguiente**.
4. En el cuadro de diálogo **Plataformas admitidas**, seleccione las plataformas que se aprovisionarán con este perfil de Windows Hello para empresas y, a continuación, haga clic en **Siguiente**.
   
    ![Configuración de Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/04.png)
5. En el cuadro de diálogo **Configuración** , siga estos pasos:
   
    ![Configuración de Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/05.png)
   
    a. Como valor de **Configurar Windows Hello para la empresa**, seleccione **Habilitado**.
   
    b. Como valor de **Usar un Módulo de plataforma segura (TPM)**, seleccione **Obligatorio**. 
   
    c. Como valor de **Método de autenticación**, seleccione **Basada en certificados**.
   
    d. Haga clic en **Siguiente**.
6. En el cuadro de diálogo **Resumen**, haga clic en **Siguiente**.
7. En el cuadro de diálogo **Finalización**, haga clic en **Cerrar**.
8. En la barra de herramientas de la parte superior, haga clic en **Implementar**.
   
    ![Configuración de Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/06.png)

## <a name="configure-the-certificate-profile"></a>Configurar el perfil de certificado
Si utiliza la autenticación basada en certificados para la autenticación local, debe configurar e implementar un perfil de certificado. Esta tarea requiere configurar un rol de sitio de punto de registro de certificados y un servidor NDES en System Center Configuration Manager. Para obtener más información, consulte [Requisitos previos de perfiles de certificado en Configuration Manager](https://technet.microsoft.com/library/dn261205.aspx).

1. Abra **System Center Configuration Manager** y vaya a **Activos y compatibilidad > Configuración de cumplimiento > Acceso a los recursos de la compañía > Perfiles de certificado**.
2. Seleccione una plantilla que tenga el uso mejorado de clave de inicio de sesión de la tarjeta inteligente.

En la página **Inscripción de SCEP** del perfil de certificado, tiene que seleccionar **Instalar en Passport for Work o generar un error** como valor de **Proveedor de almacenamiento de claves**.

## <a name="next-steps"></a>Pasos siguientes
* [Windows 10 para la empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

