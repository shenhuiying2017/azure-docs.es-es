---
title: "Configuración del acceso condicional local mediante el registro de dispositivos de Azure Active Directory | Microsoft Docs"
description: "Guía paso a paso para habilitar el acceso condicional a aplicaciones locales mediante Servicios de federación de Active Directory (AD FS) en Windows Server 2012 R2."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 1a6f1c6566468188daa71939db8345280b7a529f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>Configuración del acceso condicional local mediante el registro de dispositivos de Azure Active Directory
Cuando se requiera que los usuarios unan sus dispositivos personales al área de trabajo en el servicio de registro de dispositivos de Azure Active Directory (Azure AD), los dispositivos deben estar marcados como conocidos para la organización. A continuación aparece una guía paso a paso para habilitar el acceso condicional a aplicaciones locales mediante Servicios de federación de Active Directory (AD FS) en Windows Server 2012 R2.

> [!NOTE]
> Se requiere una licencia de Azure AD Premium o de Office 365 al usar dispositivos que están registrados en directivas de acceso condicional del servicio de registros de dispositivos de Azure Active Directory. Estas incluyen directivas que AD FS aplica en recursos locales.
> 
> Para más información sobre los escenarios de acceso condicional para recursos locales, consulte [Unión al área de trabajo desde cualquier dispositivo para SSO y autenticación de segundo factor sin problemas entre las aplicaciones de la empresa](https://technet.microsoft.com/library/dn280945.aspx).

Estas funcionalidades están disponibles para los clientes que compren una licencia de Azure Active Directory Premium.

## <a name="supported-devices"></a>Dispositivos compatibles
* Dispositivos Windows 7 unidos a un dominio
* Dispositivos Windows 8.1 personales y unidos a un dominio
* iOS 6 y versiones posteriores para el explorador Safari
* Android 4.0 o versiones posteriores, teléfonos Samsung GS3 o versiones posteriores, tabletas Samsung Galaxy Note 2 o versiones posteriores

## <a name="scenario-prerequisites"></a>Requisitos previos de escenario
* Suscripción a Office 365 o a Azure Active Directory Premium
* Un inquilino de Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 o versiones posteriores)
* Esquema actualizado en Windows Server 2012 R2
* Licencia de Azure Active Directory Premium
* Servicios de federación con Windows Server 2012 R2, configurados para SSO en Azure AD
* Proxy de aplicación web de Windows Server 2012 R2 
* Microsoft Azure Active Directory Connect (Azure AD Connect) [(descargue Azure AD Connect)](http://www.microsoft.com/en-us/download/details.aspx?id=47594)
* Dominio comprobado

## <a name="known-issues-in-this-release"></a>Problemas conocidos en esta versión
* Las directivas de acceso condicional basado en dispositivos requieren la escritura diferida de objetos de dispositivo en Active Directory de Azure Active Directory. Los objetos de dispositivo pueden tardar hasta tres horas en realizar la escritura diferida en Active Directory.
* Los dispositivos iOS 7 siempre solicitan al usuario que seleccione un certificado de cliente durante la autenticación de certificados de cliente.
* Algunas versiones de iOS 8 anteriores a iOS 8.3 no funcionan.

## <a name="scenario-assumptions"></a>Escenarios hipotéticos
En este escenario se asume que tiene un entorno híbrido que consta de un inquilino de Azure AD y una versión local de Active Directory. Estos inquilinos deben conectarse mediante Azure AD Connect, con un dominio comprobado, y con AD FS para SSO. Use la lista de comprobación siguiente para configurar el entorno según los requisitos.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>Lista de comprobación: requisitos previos para un escenario de acceso condicional
Conecte su inquilino de Azure AD a la instancia local de Active Directory.

## <a name="configure-azure-active-directory-device-registration-service"></a>Configuración del servicio de registro de dispositivos de Azure Active Directory
Use esta guía para implementar y configurar el servicio de registro de dispositivos de Azure Active Directory para su organización.

En esta guía se asume que ya configuró Windows Server Active Directory y se suscribió a Microsoft Azure Active Directory. Consulte los requisitos previos que se describieron anteriormente.

Para implementar el servicio de registro de dispositivos de Azure Active Directory con su inquilino de Azure Active Directory, complete por orden las tareas de la lista de comprobación siguiente. Cuando un vínculo de referencia lleve a un tema conceptual, vuelva a esta lista de comprobación para que pueda continuar con las tareas restantes. Algunas tareas incluyen un paso de validación del escenario que puede ayudarle a confirmar si el paso se completó correctamente.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Paso 1: habilitación del registro de dispositivos de Azure Active Directory
Siga los pasos que aparecen en la lista de comprobación para habilitar y configurar el servicio de registro de dispositivos de Azure Active Directory.

| Tarea | Referencia | 
| --- | --- |
| Habilite el registro de dispositivos en el inquilino de Azure Active Directory para permitir que los dispositivos se unan al área de trabajo. De manera predeterminada, Azure Multi-Factor Authentication no está habilitado para el servicio. Sin embargo, se recomienda que lo uso cuando registre un dispositivo. Antes de habilitar Multi-Factor Authentication en el servicio de registro de Active Directory, asegúrese de que AD FS está configurado para un proveedor de Multi-Factor Authentication. |[Habilitación del registro de dispositivos de Azure Active Directory](active-directory-device-registration-get-started.md)| 
|Los dispositivos detectan el servicio de registro de dispositivos de Azure Active Directory buscando registros DNS conocidos. Configure el DNS de la empresa para que los dispositivos puedan detectar el servicio de registro de dispositivos de Azure Active Directory. |[Configuración de la detección del registro de dispositivos de Azure Active Directory](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Parte 2: implementación de Servicios de federación de Active Directory Windows Server 2012 R2 y configuración de una relación de federación con Azure AD.

| Tarea | Referencia |
| --- | --- |
| Implemente Active Directory Domain Services con las extensiones de esquema de Windows Server 2012 R2. No es preciso actualizar los controladores de dominio a Windows Server 2012 R2. El único requisito es la actualización del esquema. |[Actualización del esquema de Active Directory Domain Services](#upgrade-your-active-directory-domain-services-schema) |
| Los dispositivos detectan el servicio de registro de dispositivos de Azure Active Directory buscando registros DNS conocidos. Configure el DNS de la empresa para que los dispositivos puedan detectar el servicio de registro de dispositivos de Azure Active Directory. |[Preparación de los dispositivos para la compatibilidad con Active Directory](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>Parte 3: habilitación de reescritura de dispositivos en Azure AD
| Tarea | Referencia |
| --- | --- |
| Complete la parte 2 de "Habilitación de escritura diferida de dispositivos en Azure AD Connect". Cuando lo haga, vuelva a esta guía. |[Habilitación de escritura diferida de dispositivos en Azure AD Connect](#upgrade-your-active-directory-domain-services-schema) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[Opcional] Parte 4: habilitación de Multi-Factor Authentication
Se recomienda encarecidamente configurar una de las distintas opciones de Multi-Factor Authentication. Si desea exigir Multi-Factor Authentication, consulte [Elección de la solución de seguridad Multi-Factor Authentication más adecuada](../multi-factor-authentication/multi-factor-authentication-get-started.md). Incluye una descripción de cada solución y vínculos para ayudarle a configurar la solución que haya elegido.

## <a name="part-5-verification"></a>Parte 5: verificación
La implementación se completó y puede probar algunos escenarios. Siga los vínculos siguientes para probar con el servicio y familiarizarse con sus características.

| Tarea | Referencia |
| --- | --- |
| Conecte varios dispositivos al área de trabajo con el servicio de registro de dispositivos de Azure Active Directory. Puede conectar dispositivos iOS, Windows y Android. |[Unión de dispositivos al área de trabajo mediante el servicio de registro de dispositivos de Azure Active Directory](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Vea y habilite o deshabilite los dispositivos registrados desde el portal de administrador. En esta tarea, usará el portal de administrador para ver algunos dispositivos registrados. |[Introducción al servicio de registro de dispositivos de Azure Active Directory](active-directory-device-registration-get-started.md) |
| Compruebe que los objetos de dispositivo se reescriben desde Azure Active Directory a Windows Server Active Directory. |[Comprobación de que los dispositivos registrados se escriben de manera diferida en Active Directory](#verify-registered-devices-are-written-back-to-active-directory) |
| Ahora que los usuarios pueden registrar sus dispositivos, puede crear en AD FS directivas de acceso a la aplicación que solo permitan dispositivos registrados. En esta tarea, creará una regla de acceso a la aplicación y un mensaje de acceso denegado personalizado. |[Creación de una directiva de acceso a aplicaciones y un mensaje personalizado de acceso denegado](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Integración de Azure Active Directory con una versión local de Active Directory
Este paso le ayuda a integrar el inquilino de Azure AD con la instancia local de Active Directory mediante Azure AD Connect. Aunque los pasos están disponibles en el Portal de Azure clásico, anote las instrucciones especiales que se enumeran en esta sección.

1. Inicie sesión en el Portal de Azure clásico con una cuenta que sea un administrador global en Azure AD.
2. En el panel izquierdo, seleccione **Active Directory**.
3. En la pestaña **Directorio** , seleccione su directorio.
4. Seleccione la pestaña **Integración de directorios** .
5. En la sección acerca de **implementación y administración**, siga los pasos 1 a 3 para integrar Azure Active Directory en su directorio local.
   
   1. Adición de dominios.
   2. Para instalar y ejecutar Azure AD Connect, use las instrucciones que aparecen en [Instalación personalizada de Azure AD Connect](connect/active-directory-aadconnect-get-started-custom.md).
   3. Comprobación y administración de la sincronización de directorios. Las instrucciones de inicio de sesión único están disponibles en este paso.
   
   Además, configure la federación con AD FS tal como se describe en [Instalación personalizada de Azure AD Connect](connect/active-directory-aadconnect-get-started-custom.md).

## <a name="upgrade-your-active-directory-domain-services-schema"></a>Actualización del esquema de Servicios de dominio de Active Directory
> [!NOTE]
> Una vez que actualice el esquema de Active Directory, no es posible revertir el proceso. Se recomienda realizar la actualización primero en un entorno de prueba.
> 

1. Inicie sesión en el controlador de dominio con una cuenta que tenga derechos de administrador de organización y de administrador de esquema.
2. Copie el directorio **[media]\support\adprep** y los subdirectorios en uno de los controladores de dominio de Active Directory (donde **[media]** es la ruta de acceso a los medios de instalación de Windows Server 2012 R2).
4. En un símbolo del sistema, vaya al directorio **adprep** y ejecute **adprep.exe /forestprep**. Siga las instrucciones en pantalla para completar la actualización del esquema.

## <a name="prepare-your-active-directory-to-support-devices"></a>Preparación de Active Directory para que admita los dispositivos
> [!NOTE]
> Se trata de una operación que se realiza una sola vez que debe ejecutar para preparar el bosque de Active Directory para admitir los dispositivos. Para completar este procedimiento, debe iniciar sesión con permisos de administrador de organización y el bosque de Active Directory debe tener el esquema de Windows Server 2012 R2.
> 


### <a name="prepare-your-active-directory-forest"></a>Preparación del bosque de Active Directory
1. En el servidor de federación, abra una ventana Comandos de Windows PowerShell y escriba **Initialize-ADDeviceRegistration**. 
2. Cuando le pidan el valor de **ServiceAccountName**, escriba el nombre de la cuenta de servicio que seleccionó como cuenta de servicio de AD FS. Si es una cuenta de gMSA, escríbala con el formato **dominio\nombreDeCuenta$**. En el caso de una cuenta de dominio, use el formato **dominio\nombreDeCuenta**.

### <a name="enable-device-authentication-in-ad-fs"></a>Habilitación de la autenticación del dispositivo en AD FS
1. En el servidor de federación, abra la consola de administración de AD FS y vaya a **AD FS** > **Directivas de autenticación**.
2. En el panel **Acciones**, seleccione **Editar autenticación principal global**.
3. Active **Habilitar autenticación de dispositivo** y, luego, seleccione **Aceptar**.
4. De manera predeterminada, AD FS quitará periódicamente los dispositivos no usados de Active Directory. Deshabilite esta tarea cuando use el servicio de registro de dispositivos de Azure Active Directory para que los dispositivos se puedan administrar en Azure.

### <a name="disable-unused-device-cleanup"></a>Deshabilitación de la limpieza de dispositivos no usados
En el servidor de federación, abra una ventana Comandos de Windows PowerShell y, luego, escriba **Set-AdfsDeviceRegistration -MaximumInactiveDays 0**.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Preparación de Azure AD Connect para la reescritura de dispositivos
Complete la parte 1: preparación de Azure AD Connect.

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Unión de dispositivos al área de trabajo mediante el servicio de registro de dispositivos de Azure Active Directory

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>Unión de un dispositivo iOS mediante el registro de dispositivos de Azure Active Directory
El registro de dispositivos de Azure Active Directory usa el proceso de inscripción de perfil móvil para dispositivos iOS. Este proceso comienza cuando el usuario se conecta a la dirección URL de inscripción de perfil con Safari. El formato de la dirección URL es el siguiente:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

En este caso, `yourdomainname` es el nombre de dominio que se configuró con Azure Active Directory. Por ejemplo, si el nombre de dominio es contoso.com, la dirección URL es la siguiente:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Existen varias formas de comunicar la URL a los usuarios. Por ejemplo, un método que se recomienda es publicar esta dirección URL en un mensaje de acceso denegado a la aplicación personalizado en AD FS. Esta información se trata en la siguiente sección [Creación de una directiva de acceso a aplicaciones y un mensaje personalizado de acceso denegado](#create-an-application-access-policy-and-custom-access-denied-message).

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Unión de un dispositivo Windows 8.1 mediante el registro de dispositivos de Azure Active Directory
1. En el dispositivo Windows 8.1, seleccione **Configuración de PC** > **Red** > **Área de trabajo**.
2. Escriba el nombre de usuario en formato UPN, por ejemplo, **dan@contoso.com**.
3. Seleccione **Combinar**.
4. Cuando se lo pidan, inicie sesión con sus credenciales. El dispositivo ahora está unido.

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Unión de un dispositivo Windows 7 mediante el registro de dispositivos de Azure Active Directory
Para registrar los dispositivos Windows 7 unidos a un dominio debe implementar el paquete de software de registro del dispositivo. El paquete de software se llama Workplace Join for Windows 7 y está disponible para su descarga en el [sitio web de Microsoft Connect](https://connect.microsoft.com/site1164). 

Las instrucciones sobre cómo usar el paquete están disponibles en [Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>Comprobación de que los dispositivos registrados se escriben de manera diferida en Active Directory
Puede ver y comprobar que los objetos de dispositivo se escribieron de manera diferida en Active Directory con LDP.exe o con el Editor ADSI. Ambos están disponibles con las herramientas del administrador de Active Directory.

De manera predeterminada, los objetos de dispositivo que se escriben de manera diferida desde Azure Active Directory se colocan en el mismo dominio que la granja de AD FS.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Creación de una directiva de acceso a aplicaciones y un mensaje personalizado de acceso denegado
Considere el siguiente escenario: se crea una aplicación de confianza para usuario autenticado en AD FS y se configura una regla de autorización de emisión que solo permite dispositivos registrados. Ahora solo los dispositivos que están registrados pueden tener acceso a la aplicación. 

Para que a los usuarios les resulte más fácil obtener acceso a la aplicación, configure un mensaje personalizado de acceso denegado que incluya instrucciones sobre cómo deben unir el dispositivo. Ahora los usuarios disponen de una manera directa de registrar sus dispositivos para que puedan tener acceso a una aplicación.

Los pasos siguientes indican cómo implementar este escenario.

> [!NOTE]
> En esta sección se supone que ya configuró una relación de confianza para usuario autenticado para la aplicación en AD FS.
> 

1. Abra la herramienta MMC de AD FS y, luego, seleccione **AD FS** > **Relaciones de confianza** > **Relación de confianza para usuario autenticado**.
2. Busque la aplicación a la que se aplica la nueva regla de acceso. Haga clic con el botón derecho en la aplicación y, luego, seleccione **Editar reglas de notificación**.
3. Seleccione la pestaña **Reglas de autorización de emisión** y, luego, **Agregar regla**.
4. En la lista desplegable de plantillas **Regla de notificaciones**, seleccione **Permitir o denegar usuarios según notificación entrante**. Luego, seleccione **Siguiente**.
5. En el campo **Nombre de regla de notificaciones**, escriba **Permitir acceso desde dispositivos registrados**.
6. En la lista desplegable **Tipo de notificación entrante**, seleccione **Es usuario registrado**.
7. En el campo **Valor de notificación entrante**, escriba **true**.
8. Seleccione el botón de radio **Permitir acceso a usuarios con esta notificación entrante** .
9. Seleccione **Finalizar** y, luego, **Aplicar**.
10. Quite todas las reglas que sean más permisivas que la regla que creó. Por ejemplo, quite la regla predeterminada **Permitir acceso a todos los usuarios**.

Ahora, la aplicación está configurada para permitir el acceso solo cuando el usuario procede de un dispositivo registrado y unido al área de trabajo. Para ver directivas de acceso más avanzadas, consulte [Administrar el riesgo con la autenticación multifactor adicional para aplicaciones con información confidencial](https://technet.microsoft.com/library/dn280949.aspx).

A continuación, se configura un mensaje de error personalizado para la aplicación. El mensaje de error notifica a los usuarios que deben unir el dispositivo al área de trabajo antes de que puedan tener acceso a la aplicación. Puede usar PowerShell y HTML personalizado para crear un mensaje personalizado de acceso denegado a la aplicación.

En el servidor de federación, abra una ventana Comandos de PowerShell y, luego, escriba el comando siguiente. Reemplazar partes del comando con elementos específicos de su sistema:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Para poder acceder a esta aplicación, debe registrar el dispositivo.

**Si usa un dispositivo iOS, seleccione este vínculo para conectarlo**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Una el dispositivo iOS al área de trabajo.

Si usa un dispositivo Windows 8.1, puede conectarlo si selecciona **Configuración de PC**> **Red** > **Área de trabajo**.

En los comandos anteriores, el **nombre de relación de confianza para usuario autenticado** es el nombre del objeto Relación de confianza para usuario autenticado de la aplicación en AD FS.
Y **yourdomain.com** es el nombre de dominio que configuró con Azure Active Directory (por ejemplo, contoso.com).
Asegúrese de quitar los saltos de línea (si existen) del contenido HTML que se pasa al cmdlet **Set-AdfsRelyingPartyWebContent**.

Ahora, cuando los usuarios accedan a la aplicación desde un dispositivo que no esté registrado con el servicio de registro de dispositivos de Azure Active Directory, verán una página similar a la captura de pantalla siguiente.

![Captura de pantalla de un error cuando los usuarios no han registrado su dispositivo con Azure AD](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)


