---
title: "Preguntas más frecuentes sobre SQL Server en Microsoft Azure Virtual Machines | Microsoft Docs"
description: "Este artículo brinda respuestas a las preguntas más frecuentes sobre cómo ejecutar SQL Server en Máquinas virtuales de Azure."
services: virtual-machines-windows
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/04/2017
ms.author: v-shysun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 88c54293b4a9b470febeda3c0e9a1755ad3b0638
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-windows-azure-virtual-machines"></a>Preguntas más frecuentes para SQL Server en Microsoft Azure Virtual Machines

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

En este tema se proporcionan respuestas a algunas de las preguntas más comunes sobre la ejecución de [SQL Server en Microsoft Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Este tema se centra en los problemas específicos de SQL Server en máquinas virtuales Windows. Si ejecuta SQL Server en máquinas virtuales Linux, consulte las [preguntas más frecuentes sobre Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Preguntas frecuentes

1. **¿Cómo puedo crear una máquina virtual de Azure con SQL Server?**

   La solución más simple es crear una máquina virtual que incluya SQL Server. Para ver un tutorial sobre cómo suscribirse a Azure y crear una máquina virtual de SQL desde el portal, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Puede seleccionar una imagen de máquina virtual que use licencias de SQL Server de pago por minuto o puede utilizar una imagen que le permita traer su propia licencia de SQL Server. También tiene la opción de instalar manualmente SQL Server en una máquina virtual con una edición de licencia libre (Developer o Express) o reutilizando una licencia local. Si trae su propia licencia, debe disponer de [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/). Para más información, vea [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Orientación de precios de máquinas virtuales de SQL Server Azure).

1. **¿Cuál es la diferencia entre las máquinas virtuales de SQL y el servicio SQL Database?**

   De manera conceptual, ejecutar SQL Server en una máquina virtual de Azure no es diferente a ejecutar SQL Server en un centro de datos remoto. En cambio, [SQL Database](../../../sql-database/sql-database-technical-overview.md) ofrece base de datos como servicio. Con SQL Database, no se tiene acceso a las máquinas que hospedan las bases de datos. Para ver una comparación completa, consulte [Selección de una opción de SQL Server en la nube: Azure SQL (PaaS) Database o SQL Server en máquinas virtuales de Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **¿Cómo puedo migrar mi base de datos local de SQL Server a la nube?**

   En primer lugar, cree una máquina virtual de Azure con una instancia de SQL Server. Luego, migre las bases de datos locales a esa instancia. Para conocer las estrategias de migración de datos, consulte [Migración de una Base de datos SQL Server a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md).

1. **¿Puedo instalar una segunda instancia de SQL Server en la misma máquina virtual? ¿Puedo cambiar las características instaladas de la instancia predeterminada?**

   Sí. Los medios de instalación de SQL Server están ubicados en una carpeta en la unidad **C** . Ejecute **Setup.exe** desde esa ubicación para agregar instancias de SQL Server nuevas o para cambiar otras características instaladas de SQL Server en la máquina. Tenga en cuenta que algunas características, como Copia de seguridad automatizada, Aplicación de revisión automatizada e Integración de Azure Key Vault, solo funcionan con la instancia predeterminada.

1. **¿Puedo desinstalar la instancia predeterminada de SQL Server?**

   Sí. Pero hay algunas consideraciones. Como se mencionó en la respuesta anterior, las características que dependen de la [Extensión del Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md) solo funcionan en la instancia predeterminada. Si desinstala la instancia predeterminada, la extensión continúa buscándola y puede generar errores de registro de eventos. Estos errores son de los dos orígenes siguientes: **Administración de credenciales de Microsoft SQL Server** y **Agente de IaaS de Microsoft SQL Server**. Es posible que uno de los errores sea similar al siguiente:

      Se ha producido un error relacionado con la red o específico de la instancia al establecer una conexión en SQL Server. No se encontró el servidor o no era accesible.

   Si decide desinstalar la instancia predeterminada, desinstale también la [Extensión del Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

1. **¿Cómo actualizo a una nueva versión o edición de SQL Server en una máquina virtual de Azure?**

   Actualmente, no hay ninguna actualización para SQL Server que se ejecute en una máquina virtual de Azure. Cree una máquina virtual de Azure nueva con la versión o edición de SQL Server que desee y, luego, migre las bases de datos al servidor nuevo con las [técnicas de migración de datos](virtual-machines-windows-migrate-sql.md)estándar.

1. **¿Cómo puedo instalar mi copia de SQL Server con licencia en una máquina virtual de Azure?**

   Existen dos formas de hacerlo. Puede aprovisionar una de las [imágenes de máquina virtual que admita licencias](virtual-machines-windows-sql-server-iaas-overview.md#BYOL). También existe la posibilidad de copiar los medios de instalación de SQL Server en la máquina virtual de Windows Server y, luego, instalar SQL Server en la máquina virtual. En lo que respecta a la licencia, debe tener [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/). Para más información, vea [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Orientación de precios de máquinas virtuales de SQL Server Azure).

1. **¿Puedo cambiar una máquina virtual para usar mi propia licencia de SQL Server si se ha creado desde una de las imágenes de la galería de pago por uso?**

   No. No puede cambiar de licencias de pago por minuto a usar su propia licencia. Cree una máquina virtual de Azure nueva usando una de las [imágenes BYOL](virtual-machines-windows-sql-server-iaas-overview.md#BYOL) y, luego, migre las bases de datos al nuevo servidor con las [técnicas de migración de datos](virtual-machines-windows-migrate-sql.md) estándar.

1. **¿Son compatibles las instancias del clúster de conmutación por error (FCI) de SQL Server con Azure Virtual Machines?**

   Sí. También puede [crear un clúster de conmutación por error de Windows en Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) y usar Espacios de almacenamiento directo (S2D) para el almacenamiento del clúster. También puede usar soluciones de agrupación en clústeres o almacenamiento de terceros como se describe en [Alta disponibilidad y recuperación ante desastres para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

1. **¿Tengo que pagar para obtener una licencia de SQL Server en una máquina virtual de Azure si solo se usa para conmutación por error o en modo en espera?**

   No tiene que pagar por obtener una licencia de SQL Server, como réplica secundaria pasiva en una implementación de alta disponibilidad, si dispone de Software Assurance y usa la Movilidad de Licencias como se describe en [P+F sobre licencias de máquinas virtuales](http://azure.microsoft.com/pricing/licensing-faq/). En caso contrario, deberá pagar para adquirir la licencia.

1. **¿Cómo se aplican las actualizaciones y los Service Packs en una máquina virtual de SQL Server?**

   Las máquinas virtuales brindan control sobre la máquina virtual, incluido el momento en que se aplican las actualizaciones y la manera de hacerlas. En el caso del sistema operativo, puede aplicar manualmente las actualizaciones de Windows, o bien puede habilitar un servicio de programación llamado [Aplicación de revisiones automatizada](virtual-machines-windows-sql-automated-patching.md). Aplicación de revisión automatizada instala las actualizaciones marcadas como importantes, incluidas las actualizaciones de SQL Server que tienen esa categoría. Otras actualizaciones opcionales a SQL Server se deben instalar manualmente.

1. **¿Es posible configurar configuraciones que no aparecen en la galería de máquinas virtuales (por ejemplo, Windows 2008 R2 + SQL Server 2012)?**

   No. En el caso de las imágenes de la galería de máquinas virtuales que incluyen SQL Server, debe seleccionar una de las imágenes que se proporcionan.

1. **¿Cómo instalo herramientas de datos de SQL en mi máquina virtual de Azure?**

    Descargue e instale las herramientas de datos de SQL desde [Microsoft SQL Server Data Tools - Business Intelligence para Visual Studio 2013](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Recursos

**Máquinas virtuales Windows**:

* [Introducción a SQL Server en máquinas virtuales Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Aprovisionamiento de una máquina virtual Windows con SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidad y recuperación ante desastres para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Procedimientos recomendados para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Estrategias de desarrollo y patrones de aplicación de SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Máquinas virtuales Linux**:

* [Introducción a SQL Server en máquinas virtuales Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Aprovisionamiento de una máquina virtual Linux con SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Preguntas más frecuentes (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentación de SQL Server en Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)