---
title: Introducción a la versión preliminar de Azure Database Migration Service | Microsoft Docs
description: Una introducción a Azure Database Migration Service, que proporciona migraciones completas desde muchos orígenes de base de datos hasta las plataformas de datos de Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 04/25/2018
ms.openlocfilehash: b28ea5606e4fae849a2906b0d81a9ed07f265ebf
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-the-azure-database-migration-service-preview"></a>¿Qué es la versión preliminar de Azure Database Migration Service?
Azure Database Migration Service es un servicio totalmente administrado diseñado para permitir migraciones completas desde varios orígenes de base de datos hasta las plataformas de datos de Azure con un tiempo de inactividad mínimo. El servicio está actualmente en versión preliminar pública y el trabajo de desarrollo está centrado en los siguientes aspectos:

- Confiabilidad y rendimiento
- Adición iterativa de pares de origen-destino
- Inversión continuada en migraciones libres de problemas

## <a name="use-familiar-tools"></a>Uso de herramientas conocidas
Azure Database Migration Service integra una parte de la funcionalidad de nuestras herramientas y servicios existentes. Así, ofrece a los clientes una solución completa y de alta disponibilidad. El servicio utiliza [Data Migration Assistant](http://aka.ms/dma) para generar informes de evaluación que proporcionen recomendaciones que le guíen a través de los cambios necesarios antes de realizar una migración. Es decisión suya aplicar los remedios necesarios. Cuando esté listo para comenzar el proceso de migración, Azure Database Migration Service realizará todos los pasos asociados. Ya puede iniciar sus proyectos de migración y olvidarse de ellos con la tranquilidad de saber que el proceso utiliza los procedimientos recomendados que determina Microsoft.

## <a name="regional-availability-during-public-preview"></a>Disponibilidad regional durante la versión preliminar pública
La versión preliminar pública de Azure Database Migration Service está disponible actualmente en las siguientes regiones:
- Este de EE. UU
- Central EE. UU:
- Centro-Sur de EE. UU
- Oeste de EE. UU
- Centro de Canadá
- Sur de Brasil
- Europa occidental
- Europa del Norte
- Sudeste asiático
- Oeste de la India

## <a name="next-steps"></a>Pasos siguientes
- [Creación de una instancia de Azure Database Migration Service mediante Azure Portal](quickstart-create-data-migration-service-portal.md).
- [Migración de SQL Server a Azure SQL Database](tutorial-sql-server-to-azure-sql.md).
- [Información general sobre los requisitos previos para usar Azure Database Migration Service](pre-reqs.md).
- [Preguntas más frecuentes sobre el uso de Azure Database Migration Service](faq.md).
