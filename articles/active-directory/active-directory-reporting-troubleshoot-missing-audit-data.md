---
title: "Solución de problemas: faltan datos en el registro de actividad de Azure Active Directory | Microsoft Docs"
description: Enumera los distintos informes disponibles para Azure Active Directory
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 47617f8f727027de113a0f503308c8accc58859e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="i-cant-find-some-actions-that-i-performed-in-the-azure-active-directory-activity-log"></a>No encuentro algunas acciones que realicé en el registro de actividad de Azure Active Directory


## <a name="symptoms"></a>Síntomas

Realice algunas acciones en Azure Portal y esperaba ver los registros de auditoría de dichas acciones en la hoja `Activity logs > Audit Logs`, pero no los encuentro.

 ![Informes](./media/active-directory-reporting-troubleshoot-missing-audit-data/01.png)
 

## <a name="cause"></a>Causa

Las acciones no aparecen inmediatamente en el registro de auditoría de la actividad. Puede tardar entre 15 minutos y una hora para ver los registros de auditoría en el portal a partir del momento en que se realiza la operación.

## <a name="resolution"></a>Resolución

Espere entre 15 minutos y una hora para ver si las acciones aparecen en el registro. Si sigue sin verlas, genere una incidencia de soporte técnico y examinaremos el problema.


## <a name="next-steps"></a>Pasos siguientes
Consulte [Preguntas más frecuentes sobre informes de Azure Active Directory](active-directory-reporting-faq.md).

