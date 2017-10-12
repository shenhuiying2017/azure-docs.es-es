---
title: "Protección de datos personales en Microsoft Azure | Microsoft Docs"
description: "Primer artículo de una serie de artículos que le ayudarán a usar Azure para proteger datos personales"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 4dbdb2dc11bdc515fb3856dd45203868122c7726
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-in-microsoft-azure"></a>Protección de datos personales en Microsoft Azure

En este artículo se presentan una serie de artículos que le ayudarán a usar servicios y tecnologías de seguridad de Azure para proteger datos personales. Se trata de un requisito clave para muchas iniciativas de gobierno y cumplimiento corporativas y del sector. El escenario, la declaración del problema y los objetivos de la empresa se incluyen aquí.

## <a name="scenario-and-problem-statement"></a>Escenario y declaración del problema

Una gran empresa de cruceros, con sede en Estados Unidos, se encuentra en proceso de expansión de sus operaciones para ofrecer itinerarios en los mares Mediterráneo, Adriático y Báltico, así como en las Islas Británicas. Para apoyar esos esfuerzos, ha adquirido varias líneas de cruceros más pequeñas establecidas en Italia, Alemania, Dinamarca y Reino Unido.

La empresa usa Microsoft Azure para almacenar datos corporativos en la nube. Estos pueden incluir información sobre clientes o empleados como, por ejemplo:

- direcciones
- números de teléfono
- números de identificación fiscal
- información de tarjeta de crédito

La empresa debe proteger la privacidad de los datos de los clientes y los empleados y, al mismo tiempo, hacer que aquellos departamentos que lo necesiten puedan tener acceso a esos datos (por ejemplo, los departamentos de nóminas y reservas).

## <a name="company-goals"></a>Objetivos de la empresa 

- Los orígenes de datos que contienen datos personales están cifrados si residen en el almacenamiento en nube.

- Los datos personales que se transfieren de una ubicación a otra están cifrados mientras están en tránsito. Esto es cierto si los datos circulan a través de la red virtual o a través de Internet entre el centro de datos corporativo y la nube de Azure.

- Sólidas tecnologías de control de acceso y administración de identidades protegen la confidencialidad e integridad de los datos personales frente al acceso no autorizado.

- Los datos personales están protegidos contra su exposición a través de una infracción de seguridad de los mismos mediante la supervisión de vulnerabilidades y amenazas.

- El estado de seguridad de los servicios de Azure que almacenan o transmiten datos personales se evalúa a fin de identificar oportunidades para proteger mejor los datos personales.

## <a name="data-protection-guidance"></a>Guía de protección de datos

Los artículos siguientes contienen una guía paso a paso que le ayudará a alcanzar los objetivos de protección de datos personales indicados anteriormente:

- [Tecnologías de cifrado de Azure](protect-personal-data-at-rest.md)

- [Tecnologías de cifrado de Azure](protect-personal-data-in-transit-encryption.md)

- [Azure identity and access technologies (Tecnologías de identidad y acceso de Azure)](protect-personal-data-identity-access-controls.md)

- [Azure network security technologies (Tecnologías de Azure Network Security)](protect-personal-data-network-security.md)

- [Azure Security Center](protect-personal-data-azure-security-center.md)



## <a name="next-steps"></a>Pasos siguientes

- [Sitio de información de seguridad de Azure](https://aka.ms/AzureSecInfo)

- [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx)

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

- [Blog del equipo de seguridad de Azure](https://www.azuresecurityorg)

- [Azure.com Blog - Security (Blog de Azure.com: seguridad)](https://azure.microsoft.com/blog/topics/security/)
