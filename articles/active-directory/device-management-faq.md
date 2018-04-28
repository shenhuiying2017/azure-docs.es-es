---
title: Preguntas más frecuentes sobre la administración de dispositivos de Azure Active Directory | Microsoft Docs
description: Preguntas más frecuentes sobre la administración de dispositivos de Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 4358b57284721642957d56ad8cfeea2b0f53fd89
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="azure-active-directory-device-management-faq"></a>Preguntas más frecuentes sobre la administración de dispositivos de Azure Active Directory



**P: ¿Cómo puedo registrar un dispositivo macOS?**

**R:** Para registrar el dispositivo Mac OS:

1.  [Cree una directiva de cumplimiento](https://docs.microsoft.com/intune/compliance-policy-create-mac-os).
2.  [Defina una directiva de acceso condicional para dispositivos de Mac OS](active-directory-conditional-access-azure-portal.md). 

**Comentarios:**

- Los usuarios que se incluyen en la directiva de acceso condicional necesitan un [versión admitida de Office para Mac OS](active-directory-conditional-access-technical-reference.md#client-apps-condition) para acceder a los recursos. 

- Durante el primer intento de acceso, se pide a los usuarios que inscriban el dispositivo mediante el portal de empresa.

---

**P: He registrado el dispositivo hace poco. ¿Por qué no puedo ver el dispositivo en la información del usuario en Azure Portal?**

**R:** los dispositivos Windows 10 que están unidos a Azure AD híbrido no aparecen bajo los dispositivos del USUARIO.
Debe usar PowerShell para ver todos los dispositivos. 

Los únicos que aparecen entre los dispositivos del USUARIO son los siguientes:

- Todos los dispositivos personales que no están unidos a Azure AD híbrido. 
- Todos los dispositivos que no tienen Windows 10 o Windows Server 2016
- Todos los dispositivos que no son de Windows 

---

**P: ¿Por qué no puedo ver en Azure Portal todos los dispositivos registrados en Azure Active Directory?** 

**R:** Ahora, los encontrará en Azure AD Directory -> menú Todos los dispositivos. También puede usar Azure PowerShell para buscar todos los dispositivos. Para más información, vea el cmdlet [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0).

--- 

**P: ¿Cómo puedo saber cuál es el estado de registro del dispositivo del cliente?**

**R:** En los dispositivos Windows 10 y Windows Server 2016 o versiones posteriores, ejecute dsregcmd.exe /status.

En las versiones anteriores del sistema operativo, ejecute "%programFiles%\Microsoft Workplace Join\autoworkplace.exe"

---

**P: ¿Por qué aún aparece registrado un dispositivo que ya he eliminado de Azure Portal o con Windows PowerShell?**

**R:** Se debe al diseño. El dispositivo no puede acceder a los recursos en la nube. Si desea volver a registrar el dispositivo, deberá realizar algunas acciones manualmente. 

Para anular el estado de unión de los dispositivos Windows 10 y Windows Server 2016 unidos a un dominio de AD local:

1.  Abra el símbolo del sistema como administrador.

2.  Escriba `dsregcmd.exe /debug /leave`

3.  Cierre la sesión y luego iníciela para desencadenar la tarea programada que registra de nuevo el dispositivo con Azure AD. 

En las versiones anteriores del sistema operativo Windows que están unidas a un dominio de AD local:

1.  Abra el símbolo del sistema como administrador.
2.  Escriba `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Escriba `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

**P: ¿Por qué veo entradas duplicadas del dispositivo en Azure Portal?**

**R:**

-   En Windows 10 y Windows Server 2016, si se realizan varios intentos para desunir y volver a unir el mismo dispositivo, pueden aparecer entradas duplicadas. 

-   Si ha usado Agregar cuenta profesional o educativa, cada usuario de Windows que usa Agregar cuenta profesional o educativa creará un nuevo registro de dispositivo con el mismo nombre de dispositivo.

-   En las versiones anteriores del sistema operativo Windows que estén unidas a un dominio de AD local con el registro automático, se creará un nuevo registro del dispositivo con el mismo nombre de dispositivo para cada usuario del dominio registrado en el dispositivo. 

-   En el caso de las máquinas unidas a Azure AD que se han borrado, se han vuelto a instalar y se han vuelto a unir con el mismo nombre, aparecerá otro registro con el mismo nombre de dispositivo.

---

**P: ¿Por qué puede un usuario tener acceso a recursos desde un dispositivo que he deshabilitado en Azure Portal?**

**R:** Una revocación puede tardar hasta una hora en aplicarse.

>[!Note] 
>En el caso de los dispositivos inscritos, se recomienda borrar el dispositivo para asegurarse de que los usuarios no pueden acceder a los recursos. Para más información, vea [Inscribir dispositivos para administrarlos en Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 


---

**P: ¿Por qué mis usuarios ven un mensaje que indica que no pueden acceder desde aquí?**

**R:** Si ha configurado determinadas reglas de acceso condicional para requerir un estado de dispositivo específico y el dispositivo no cumple los criterios, a los usuarios se les bloquea y ven este mensaje. Evalúe las reglas de la directiva de acceso condicional y asegúrese de que el dispositivo es capaz de cumplir los criterios para evitar ese mensaje.

---


**P: Puedo ver el registro del dispositivo en la información del USUARIO en Azure Portal y puedo ver el estado como registrado en el cliente. ¿He establecido la configuración correctamente para utilizar el acceso condicional?**

**R:** El estado de unión del dispositivo, especificado en deviceID, debe coincidir con el de Azure AD y satisfacer los criterios de evaluación del acceso condicional. Para más información, vea [Introducción al Registro de dispositivos de Azure Active Directory](active-directory-device-registration.md).

---

**P: ¿Por qué me aparece el mensaje “El nombre de usuario o la contraseña no son correctos” para un dispositivo que ya he unido a Azure AD?**

**R:** Las razones comunes para este escenario son:

- Las credenciales de usuario ya no son válidas.

- El equipo no puede comunicarse con Azure Active Directory. Compruebe si existen errores de conectividad de red.

- No se han cumplido los requisitos previos para Azure AD Join. Asegúrese de que haya seguido los pasos para la [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-overview.md).  

- Los inicios de sesión federados requieren que el servidor de federación admita un punto de conexión activo de WS-Trust. 

---

**P: ¿Por qué me aparece un cuadro de diálogo que indica que se ha producido un error cuando intento unir mi equipo a Azure AD?**

**R:** Es el resultado de configurar la inscripción de Azure Active Directory con Intune. Asegúrese de que el usuario que intenta realizar la unión a Azure AD tiene asignada la licencia de Intune correcta. Para más información, vea [Configurar la administración de dispositivos Windows](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**P: ¿Por qué se produce un error al intentar unir mi equipo a pesar de que no he obtenido información sobre el error?**

**R:** Puede deberse a que el usuario ha iniciado sesión en el dispositivo con la cuenta de administrador integrada. Cree una cuenta local distinta antes de usar Azure Active Directory Join para completar la instalación. 

---

**P: ¿Dónde puedo encontrar instrucciones para la configuración de registro automático de dispositivos?**

**R:** Para ver instrucciones detalladas, consulte [Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

---

**P: ¿Dónde puedo encontrar información para solucionar problemas con el registro automático de dispositivos?**

**R:** Para consultar información sobre solución de problemas, vea:

- [Solución de problemas de registro automático de equipos unidos a un dominio en Azure AD: Windows 10 y Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md)

- [Solución de problemas de registro automático de equipos unidos a un dominio en Azure AD para clientes de nivel inferior de Windows](device-management-troubleshoot-hybrid-join-windows-legacy.md)
 
---

