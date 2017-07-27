---
title: "Solución de problemas de registro automático de equipos unidos a un dominio Azure AD para clientes de nivel inferior de Windows | Microsoft Docs"
description: "Solución de problemas de registro automático de equipos unidos a un dominio Azure AD para clientes de nivel inferior de Windows."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
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
ms.openlocfilehash: 49af838d750ba61694bdb28a83ee6e531627527d
ms.contentlocale: es-es
ms.lasthandoff: 03/10/2017

---
# <a name="troubleshooting-auto-registration-of-domain-joined-computers-to-azure-ad-for-windows-down-level-clients"></a>Solución de problemas de registro automático de equipos unidos a un dominio en Azure AD para clientes de nivel inferior de Windows 

Este tema solo es aplicable a los siguientes clientes: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Para Windows 10 o Windows Server 2016, consulte [Solución de problemas de registro automático de equipos unidos a un dominio Azure AD para Windows 10 y Windows Server 2016](active-directory-device-registration-troubleshoot-windows.md).

En este tema se supone que ha configurado el registro automático de dispositivos unidos a un dominio como se describe en [Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory](active-directory-device-registration-get-started.md).
 
En este tema se proporcionan instrucciones sobre cómo resolver problemas potenciales.  
Algunos aspectos que se deben tener en cuenta para obtener buenos resultados: 

- El registro de estos clientes en Azure AD se realiza por usuario/dispositivo. Por ejemplo: si jdoe y jharnett inician sesión en este dispositivo, se crea un registro independiente (Id. de dispositivo) para cada uno de estos usuarios en la pestaña de información de USUARIO.  

- El registro de estos clientes predeterminados se configura para probar el inicio de sesión o el bloqueo/desbloqueo, y se puede experimentar un retraso de 5 minutos que se desencadena con la tarea del Programador de tareas. 

- La reinstalación del sistema operativo o la anulación del registro y el nuevo registro de forma manual pueden crear un nuevo registro en Azure AD y generar varias entradas en la pestaña de información de USUARIO de Azure Portal. 


## <a name="step-1-retrieve-the-registration-status"></a>Paso 1: Recuperar el estado del registro 

**Para verificar el estado del registro:**  

1. Abra el símbolo del sistema como administrador. 

2. Escriba `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`

Este comando muestra un cuadro de diálogo que proporciona más detalles sobre el estado de la unión.

![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-registration-status"></a>Paso 2: Evaluar el estado del registro 

Si la unión no se realiza correctamente, el cuadro de diálogo proporciona detalles sobre el problema que se ha producido.

**Los problemas más comunes son:**

- Una configuración incorrecta de AD FS o Azure AD

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- No está registrado como un usuario de dominio

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)

- Se ha alcanzado una cuota

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- El servicio no responde 

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

También puede encontrar la información de estado en el registro de eventos en **Registros de aplicaciones y servicios\Microsoft-Workplace Join**.
  
**Las causas más comunes del error de un registro son:** 

- El equipo no está en la red interna de la organización o en una VPN sin conexión a una implementación local del controlador de dominio de AD.

- Ha iniciado sesión en el equipo con una cuenta del equipo local. 

- Problemas de configuración de servicio: 

  - El servidor de federación se ha configurado para admitir **WIAORMULTIAUTHN**. 

  - No hay ningún objeto de punto de conexión de servicio que haga referencia a su nombre de dominio comprobado en Azure AD en el bosque de AD al que pertenece el equipo.

  - Un usuario ha alcanzado el límite de dispositivos. Consulte Introducción al Registro de dispositivos de Azure Active Directory.

## <a name="next-steps"></a>Pasos siguientes

Para más información, vea [Preguntas más frecuentes sobre el registro automático de dispositivos](active-directory-device-registration-faq.md) 

