---
title: "Protección de los datos y el servicio de SQL de Azure en Azure Security Center | Microsoft Docs"
description: "En este documento se explica cómo las recomendaciones de Azure Security Center ayudan a proteger el servicio SQL de Azure y los datos y a cumplir las directivas de seguridad."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 0c3a11e9a86767641533b16de1b96b4c59bfdf51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Protección del servicio SQL de Azure en Azure Security Center
El Centro de seguridad de Azure analiza el estado de seguridad de los recursos de Azure. Cuando Security Center identifica posibles vulnerabilidades de seguridad, crea recomendaciones que lo guiarán por el proceso de configuración de los controles necesarios.  Las recomendaciones se aplican a los tipos de recursos de Azure: máquinas virtuales, redes, SQL y datos y aplicaciones.

Este artículo aborda las recomendaciones sobre los datos y el servicio SQL de Azure. Las recomendaciones se centran en la habilitación de la auditoría de servidores y bases de datos de SQL de Azure, el cifrado de bases de datos SQL Database y la habilitación del cifrado de su cuenta de Azure Storage.  Use la tabla siguiente para entender mejor las recomendaciones disponibles sobre los datos y el servicio SQL y lo que harán cada una de ellas si se aplican.

## <a name="available-sql-service-and-data-recommendations"></a>Recomendaciones de datos y servicio SQL disponibles
| Recomendación | Descripción |
| --- | --- |
| [Habilitar la auditoría y la detección de amenazas en los servidores SQL Server](security-center-enable-auditing-on-sql-servers.md) |Recomienda activar la auditoría y la detección de amenazas para los servidores de Azure SQL (solo en el servicio Azure SQL; no incluye la instancia SQL que se ejecuta en sus máquinas virtuales). |
| [Habilitación de la auditoría y la detección de amenazas en bases de datos SQL](security-center-enable-auditing-on-sql-databases.md) |Recomienda activar la auditoría y la detección de amenazas para las bases de datos de Azure SQL (solo en el servicio Azure SQL; no incluye la instancia SQL que se ejecuta en sus máquinas virtuales). |
| [Habilitar Cifrado de datos transparente en bases de datos SQL](security-center-enable-transparent-data-encryption.md) |Recomienda habilitar el cifrado de bases de datos SQL (solo el servicio SQL de Azure). |

## <a name="see-also"></a>Otras referencias
Para obtener más información sobre las recomendaciones que se aplican a otros tipos de recursos de Azure, consulte los siguientes artículos:

* [Protección de las máquinas virtuales en Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Protección de las aplicaciones en Azure Security Center](security-center-application-recommendations.md)
* [Protección de las redes en Azure Security Center](security-center-network-recommendations.md)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
