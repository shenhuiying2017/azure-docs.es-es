---
title: "Preguntas más frecuentes sobre informes de Azure Active Directory | Microsoft Docs"
description: "Preguntas más frecuentes sobre informes de Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: accf292f70bf0eafdefc00c3feeaf8e346605401
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-reporting-faq"></a>Preguntas más frecuentes sobre informes de Azure Active Directory

Este artículo incluye respuestas a preguntas más frecuentes (P+F) sobre los informes de Azure Active Directory.  
Para más información, vea [Informes de Azure Active Directory](active-directory-reporting-azure-portal.md). 

**P: ¿Cuál es la retención de datos de los registros de actividad (auditoría e inicios de sesión) en Azure Portal?** 

**R:** Se proporcionan 7 días de retención de datos para los clientes con suscripción gratuita, pero si cambian a una licencia de Azure AD Premium 1 o Premium 2, pueden acceder a los datos durante un período máximo de 30 días. Para obtener más información sobre la retención, vea [Directivas de retención de informes de Azure Active Directory](active-directory-reporting-retention.md).

--- 

**P: ¿Cuánto tiempo debo esperar para poder ver los datos de actividad después de haber completado una tarea?**

**R:** Los registros de actividad de auditoría tienen una latencia que oscila entre 15 minutos y una hora. Los registros de actividad de inicio de sesión tienen una latencia comprendida entre 15 minutos para la mayoría de los registros y hasta 2 horas para una serie de registros.

---

**P: ¿Necesito ser un administrador global para consultar los registros de actividad en Azure Portal o para obtener datos a través de la API?**

**R**: No. Puede ser un **Lector de seguridad**, un **Administrador de seguridad** o un **Administrador global** para ver datos en Azure Portal o para acceder a ellos con la API.

---

**P: ¿Puedo obtener información del registro de actividad de Office 365 a través de Azure Portal?**

**R:** Aunque los registros de actividad de Office 365 y Azure AD comparten mucho de los recursos del directorio, si desea obtener una vista completa de los registros de actividad de Office 365, debe ir al Centro de administración de Office 365 para obtener la información del registro de actividad de Office 365.

---


**P: ¿Qué API utilizo para obtener información sobre los registros de actividad de Office 365?**

**R:** Use las API de Administración de Office 365 para acceder a los [registros de actividad de Office 365 a través de una API](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**P: ¿Cuántos registros puedo descargar de Azure Portal?**

**R:** Puede descargar hasta 120 000 registros de Azure Portal. Los registros se ordenan a partir de los *más recientes* y, de forma predeterminada, se obtienen los últimos 120 000 registros. 

---

**P: ¿Cuántos registros puedo consultar a través de las API de actividades?**

**R:** Puede consultar hasta un millón de registros (si no usa el operador TOP, que ordena los registros a partir del más reciente). Si usa el operador "top", puede consultar hasta 500 000 registros. Puede encontrar las consultas de ejemplo sobre cómo usar la API [aquí](active-directory-reporting-api-getting-started.md).

---

**P: ¿Cómo puedo obtener una licencia Premium?**

**R:** Vea [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md) para obtener una respuesta a esta pregunta.

---

**P: ¿Cuándo se deberían ver los datos de actividades después de obtener una licencia Premium?**

**R:** Si dispone ya de datos de actividades como una licencia gratuita, entonces puede ver los mismos datos. Si no tiene ningún dato, tardará uno o dos días.

---

**P: ¿Puedo ver los datos del último mes después de obtener una licencia de Azure AD Premium?**

**R:** Si ha cambiado recientemente a una versión Premium (incluida una versión de prueba), inicialmente, puede ver datos de hasta siete días. Cuando se acumulan datos, verá hasta 30 días.

---

**P: Hay un evento de riesgo en Identity Protection, pero no veo el inicio de sesión correspondiente en todos los inicios de sesión. ¿Es normal?**

**R:** Sí, Identity Protection evalúa el riesgo de todos los flujos de autenticación si es interactivo o no interactivo. Sin embargo, el informe únicamente de todos los inicios de sesión muestra los inicios de sesión interactivos.

---

**P: ¿Cómo puedo descargar el informe Usuarios marcados en riesgo en Azure Portal?**

**R:**: La opción para descargar el informe *Usuarios marcados en riesgo* se agregará próximamente.

---

**P: ¿Cómo puedo sé por qué un inicio de sesión o un usuario se marcó como en riesgo en Azure Portal?**

**R:**: Los clientes de la edición Premium pueden obtener más información sobre los eventos de riesgos subyacentes haciendo clic en el usuario en "Usuarios marcados en riesgo" o haciendo clic en "Inicios de sesión en riesgo". Los clientes de edición gratuita y básica pueden ver los inicios de sesión y usuarios en riesgo sin la información de eventos de riesgo subyacentes.

---

**P: ¿Cómo se calculan las direcciones IP en los inicios de sesión y en el informe de inicios de sesión de riesgo?**

**R:** Las direcciones IP se emiten de forma que no haya ninguna conexión definitiva entre una dirección IP y donde se encuentre físicamente el equipo con esa dirección. Esto se complica por factores tales como proveedores de dispositivos móviles y VPN que emiten direcciones IP desde grupos centrales, a menudo muy lejos de donde se usa realmente el dispositivo del cliente. Dado lo anterior, la conversión de la dirección IP en una ubicación física es un esfuerzo notable basado en seguimientos, datos de registro, búsquedas inversas y otra información. 

---
