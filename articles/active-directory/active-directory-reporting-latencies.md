---
title: Latencias de informes de Azure Active Directory | Microsoft Docs
description: Cantidad de tiempo necesaria para que los eventos de informes aparezcan en su directorio de Azure Active Directory
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 346b14f8-d16d-4b07-8211-e6c5eec07062
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: dhanyahk;markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 9443a00232420d58dea52ed01f31a4ef964a1620
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---
# <a name="azure-active-directory-report-latencies"></a>Latencias de informes de Azure Active Directory
*Esta documentación forma parte de la [guía de informes de Azure Active Directory](active-directory-reporting-guide.md).*

| Informe | Mínima | Media | Máxima |
| --- | --- | --- | --- |
| **Informes de seguridad** | | | |
| Actividad de inicio de sesión irregular |2 horas |4 horas |8 horas |
| Inicios de sesión desde orígenes desconocidos |2 horas |4 horas |8 horas |
| Inicios de sesión tras varios errores |2 horas |4 horas |8 horas |
| Inicios de sesión desde varias ubicaciones geográficas |2 horas |4 horas |8 horas |
| Inicios de sesión desde direcciones IP con actividad sospechosa |2 horas |4 horas |8 horas |
| Inicios de sesión desde dispositivos posiblemente infectados |2 horas |4 horas |8 horas |
| Usuarios con actividad de inicio de sesión anómala |2 horas |4 horas |8 horas |
| Usuarios con credenciales perdidas |2 horas |4 horas |8 horas |
| Actividad de inicio de sesión de todos los usuarios |2 horas |4 horas |8 horas |
| **Informes de aplicación** | | | |
| Actividad de aprovisionamiento de cuentas |2 horas |4 horas |8 horas |
| Errores de aprovisionamiento de cuentas |2 horas |4 horas |8 horas |
| Uso de la aplicación |2 horas |4 horas |8 horas |
| Estado de la sustitución de contraseña |2 horas |4 horas |8 horas |
| **Informes de auditoría y actividad** | | | |
| Informe de auditoría |1 minuto |15 minutos |30 minutos |
| Actividad de restablecimiento de contraseña (Azure AD) |2 horas |4 horas |8 horas |
| Actividad de restablecimiento de contraseña (Identity Manager) |2 horas |4 horas |8 horas |
| Actividad de registro de restablecimiento de contraseña (Azure AD) |2 horas |4 horas |8 horas |
| Actividad de registro de restablecimiento de contraseña (Identity Manager) |2 horas |4 horas |8 horas |
| Actividad de los grupos de autoservicio (Azure AD) |2 horas |4 horas |8 horas |
| Actividad de los grupos de autoservicio (Identity Manager) |2 horas |4 horas |8 horas |
| **Informes de RMS** | | | |
| Usuarios de RMS más activos |2 horas |4 horas |8 horas |
| Uso de RMS |2 horas |4 horas |8 horas |
| Uso de dispositivos RMS |2 horas |4 horas |8 horas |
| Uso de aplicaciones habilitadas para RMS |2 horas |4 horas |8 horas |


