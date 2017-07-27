---
title: "Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory | Microsoft Docs"
description: "Configure sus dispositivos Windows unidos a un dominio para registrarse de forma automática y silenciosa en Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 9934902811354ffa4047d70d995a6dd44be0229b
ms.contentlocale: es-es
ms.lasthandoff: 03/10/2017

---
# <a name="get-started-with-azure-active-directory-device-registration"></a>Introducción al Registro de dispositivos de Azure Active Directory

El Registro de dispositivos de Azure Active Directory es la base de los escenarios de acceso condicional basado en dispositivos. Cuando se registra un dispositivo, el Registro de dispositivos de Azure Active Directory le proporciona una identidad que se utiliza para autenticar el dispositivo cuando el usuario inicia sesión. El dispositivo autenticado y los atributos del dispositivo pueden utilizarse para aplicar directivas de acceso condicional para las aplicaciones que se hospedan en la nube y locales.

Cuando se combina con una solución de administración de dispositivos móviles (MDM) como Microsoft Intune, los atributos del dispositivo en Azure Active Directory se actualizan con información adicional sobre este. Esto le permite crear reglas de acceso condicional que obligan a que el acceso desde dispositivos cumpla con las normas de seguridad y cumplimiento. Para obtener más información sobre la inscripción de dispositivos de Microsoft Intune, consulte el artículo sobre cómo inscribir dispositivos para su administración en Intune.
Entre los escenarios que posibilita el Registro de dispositivos de Azure Active Directory se encuentra la compatibilidad con dispositivos Windows, iOS y Android. Los distintos escenarios que usan el Registro de dispositivos de Azure AD pueden tener requisitos y compatibilidad con la plataforma más específicos. 

Estos escenarios son los siguientes:

- **Acceso condicional para aplicaciones de Office 365 con Microsoft Intune**: los administradores de TI pueden aprovisionar directivas de dispositivos de acceso condicional para proteger los recursos corporativos, al tiempo que permiten que los trabajadores de la información con dispositivos compatibles tengan acceso a los servicios. Para más información, consulte las directivas de dispositivo de acceso condicional para los servicios de Office 365.

- **Acceso condicional a las aplicaciones hospedadas localmente**: puede usar dispositivos registrados con directivas de acceso en las aplicaciones configuradas para usar AD FS con Windows Server 2012 R2. Para obtener más información sobre cómo configurar el acceso condicional localmente, consulte [Configuración del acceso condicional local mediante el registro de dispositivos de Azure Active Directory](active-directory-device-registration-on-premises-setup.md).

## <a name="setting-up-azure-active-directory-device-registration"></a>Configuración del Registro de dispositivos de Azure Active Directory

Para configurar el Registro de dispositivos, tiene varias opciones:

- Los dispositivos se pueden registrar cuando se unen al dominio de Azure AD. Para obtener más información sobre este tema, puede leer sobre la unión a Azure AD y la configuración necesaria para que los usuarios se unan a un dominio de este tipo.

- Los dispositivos pueden registrarse cuando los usuarios agregan cuentas profesionales o educativas a Windows en un dispositivo personal, o bien cuando los dispositivos móviles se conectan a unos recursos de trabajo que precisan registrarse. Para asegurarse de esto, debe habilitar el Registro de dispositivos de Azure AD en Azure Portal. 

- Los dispositivos se pueden registrar con el registro automático de dispositivos en máquinas tradicionales unidas un dominio. Para asegurarse de esto, debe primero configurar Azure AD Connect antes de continuar con el registro automático de dispositivos.

Para ver las instrucciones más recientes, consulte [Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).  
También puede ver y habilitar o deshabilitar dispositivos registrados mediante el Portal de administrador de Azure Active Directory.

## <a name="enable-the-azure-active-directory-device-registration-service"></a>Habilitación del servicio Registro de dispositivos de Azure Active Directory

**Para habilitar el servicio Registro de dispositivos de Azure Active Directory, siga estos pasos**:

1.  Inicie sesión como administrador en Microsoft Azure Portal.

2.  En el panel izquierdo, seleccione **Active Directory**.

3.  En la pestaña Directorio, seleccione su directorio.

4.  Haga clic en **Configurar**.

5.  Desplácese a **Dispositivos**.

6.  Seleccione TODO en LOS USUARIOS PUEDEN REGISTRAR SUS DISPOSITIVOS CON AZURE AD.

7.  Seleccione el número máximo de dispositivos que quiere autorizar por usuario.

La inscripción en Microsoft Intune o Administración de dispositivos móviles para Office 365 requiere la unión al área de trabajo. Si configuró alguno de estos servicios, se seleccionará **TODOS** y se deshabilitará el botón **NINGUNO**. Asegúrese de que se están configurados correctamente y tiene la licencia adecuada.

De forma predeterminada, la autenticación en dos fases no está habilitada para el servicio. Aunque se recomienda usar la autenticación en dos fases al registrar un dispositivo.

- Antes de requerir la autenticación en dos fases para este servicio, debe configurar un proveedor de autenticación en dos fases en Azure Active Directory y configurar las cuentas de usuario para Multi-Factor Authentication. Vea Adición de Multi-Factor Authentication a Azure Active Directory.

- Si utiliza AD FS con Windows Server 2012 R2, debe configurar un módulo de autenticación en dos fases en AD FS. Para ello consulte Utilización de Multi-Factor Authentication con Servicios de federación de Active Directory.

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Visualización y administración de objetos de dispositivo en Azure Active Directory

En el Portal de administrador de Azure, puede ver, bloquear y desbloquear dispositivos. Un dispositivo bloqueado dejará de tener acceso a las aplicaciones configuradas para permitir solo dispositivos registrados.

**Para ver y administrar objetos de dispositivo en Azure Active Directory, siga estos pasos:**
 
1.  Inicie sesión en Microsoft Azure Portal como administrador.

2.  En el panel izquierdo, seleccione **Active Directory**.

3.  Seleccione el directorio.

4.  Seleccione **Usuarios**. 

5.  Haga clic en el usuario para el que desea ver los dispositivos.

6.  Seleccione **Dispositivos**.

7.  Seleccione **Dispositivos registrados**.

Ahora, puede ver, bloquear o desbloquear dispositivos registrados de usuarios.
Los dispositivos Windows 10 que están unidos a un dominio local y se registran automáticamente no aparecen en la pestaña Usuarios. Use el comando Get-MsolDevice de PowerShell para buscar todos los dispositivos empresariales. 


## <a name="next-steps"></a>Pasos siguientes

Para configurar el registro de dispositivos automatizado, siga los pasos de [Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).



