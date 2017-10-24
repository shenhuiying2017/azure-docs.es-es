---
title: "Preguntas más frecuentes sobre SQL Server en máquinas virtuales Linux de Azure | Microsoft Docs"
description: "En este artículo se brindan respuestas a las preguntas más frecuentes sobre cómo ejecutar SQL Server en máquinas virtuales Linux de Azure."
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 10/05/2017
ms.author: jroth
ms.openlocfilehash: a001ae116e33e0b7be4431b0bc4c8bb319f4e801
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Preguntas más frecuentes para SQL Server en máquinas virtuales Linux de Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

En este tema se proporcionan respuestas a algunas de las preguntas más comunes sobre la ejecución de [SQL Server en máquinas virtuales Linux de Azure](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Este tema se centra en los problemas específicos de SQL Server en máquinas virtuales Linux. Si ejecuta SQL Server en máquinas virtuales Windows, consulte las [preguntas más frecuentes sobre Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Preguntas frecuentes

1. **¿Cómo puedo crear una máquina virtual Linux de Azure con SQL Server?**

   La solución más simple es crear una máquina virtual Linux que incluya SQL Server. Para ver un tutorial sobre cómo suscribirse a Azure y crear una máquina virtual de SQL desde el portal, consulte [Aprovisionamiento de una máquina virtual Linux con SQL Server en Azure Portal](provision-sql-server-linux-virtual-machine.md). También tiene la opción de instalar manualmente SQL Server en una máquina virtual con una edición de licencia libre (Developer o Express) o reutilizando una licencia local. Si trae su propia licencia, debe disponer de [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **¿Cómo actualizo a una nueva versión o edición de SQL Server en una máquina virtual de Azure?**

   Actualmente, no hay ninguna actualización para SQL Server que se ejecute en una máquina virtual de Azure. Cree una máquina virtual de Azure nueva con la versión o edición de SQL Server que desee y, luego, migre las bases de datos al servidor nuevo con las [técnicas de migración de datos estándar](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

1. **¿Cómo puedo instalar mi copia de SQL Server con licencia en una máquina virtual de Azure?**

   En primer lugar, cree una máquina virtual solo para Linux OS. Luego ejecute los [pasos de instalación de SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) para la distribución de Linux. Salvo que instale una de las ediciones de SQL Server de licencia abierta, debe tener también una licencia de SQL Server y [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **¿Hay imágenes de máquina virtual Linux de tipo "traiga su propia licencia" (BYOL) para SQL Server?**

   En este momento, no hay ninguna imagen de máquina virtual Linux BYOL para SQL Server. Sin embargo, puede instalar manualmente SQL Server en una máquina virtual solo Linux como se analizó en las preguntas anteriores.

1. **¿Puedo cambiar una máquina virtual para usar mi propia licencia de SQL Server si se ha creado desde una de las imágenes de la galería de pago por uso?**

   No. No puede cambiar de licencias de pago por minuto a usar su propia licencia. Debe crear una máquina virtual Linux nueva, instalar SQL Server y migrar los datos. Consulte la pregunta anterior para más detalles sobre cómo traer su propia licencia.

1. **¿Qué paquetes relacionados de SQL Server se instalan también?**

   Para ver los paquetes de SQL Server que se instalan de manera predeterminada en máquinas virtuales Linux con SQL Server, consulte [Paquetes instalados](sql-server-linux-virtual-machines-overview.md#packages).

1. **¿Las soluciones de alta disponibilidad de SQL Server son compatibles con las máquinas virtuales de Azure?**

   De momento, no. Tanto los grupos de disponibilidad AlwaysOn como los clústeres de conmutación por error requieren una solución de clústeres en Linux, como Pacemaker. La distribuciones de Linux compatibles con SQL Server no admiten sus complementos de alta disponibilidad en la nube.

1. **¿Por qué no puedo aprovisionar una máquina virtual RHEL o SLES con SQL Server con una suscripción de Azure con un límite de gasto?**

   Las máquinas virtuales RHEL y SLES requieren una suscripción sin límite de gasto y con un método de pago comprobado (con frecuencia, una tarjeta de crédito) asociado con la suscripción. Si aprovisiona una máquina virtual RHEL o SLES sin quitar el límite de gasto, se deshabilitará la suscripción y se detendrán todas las máquinas virtuales o servicios. Si de todos modos ejecuta en este estado, para volver a habilitar la suscripción, [quite el límite de gasto](https://account.windowsazure.com/subscriptions). El crédito restante se restaurará para el ciclo de facturación actual, pero se le cobrará un recargo por la imagen de máquina virtual RHEL o SLES en la tarjeta de crédito si elige volver a iniciar la operación y sigue ejecutándola.

## <a name="resources"></a>Recursos

**Máquinas virtuales Linux**:

* [Introducción a SQL Server en máquinas virtuales Linux](sql-server-linux-virtual-machines-overview.md)
* [Aprovisionamiento de una máquina virtual Linux con SQL Server](provision-sql-server-linux-virtual-machine.md)
* [Documentación de SQL Server en Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Máquinas virtuales Windows**:

* [Introducción a SQL Server en máquinas virtuales Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Aprovisionamiento de una máquina virtual Windows con SQL Server](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Preguntas más frecuentes (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)