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
ms.date: 12/13/2017
ms.author: jroth
ms.openlocfilehash: 8b556b01aa47aeb3588138dfa61e517c00dc44dc
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Preguntas más frecuentes para SQL Server en máquinas virtuales Linux de Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

En este artículo se proporcionan respuestas a algunas de las preguntas más comunes sobre la ejecución de [SQL Server en Virtual Machines](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Este artículo se centra en los problemas específicos de SQL Server en máquinas virtuales Linux. Si ejecuta SQL Server en máquinas virtuales Windows, consulte las [preguntas más frecuentes sobre Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Imágenes

1. **¿Qué imágenes de la galería de máquinas virtuales de SQL Server están disponibles?**

   Azure mantiene imágenes de máquinas virtuales para todas las versiones principales admitidas de SQL Server en todas las ediciones de Linux y Windows. Para más información, consulte la lista completa de [imágenes de máquinas virtuales Linux](sql-server-linux-virtual-machines-overview.md#create) e [imágenes de máquinas virtuales Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **¿Están actualizadas las imágenes existentes de la galería de máquinas virtuales de SQL Server?**

   Cada dos meses, las imágenes de SQL Server de la galería de máquinas virtuales se actualizan con las últimas actualizaciones de Linux y Windows. En las imágenes de Linux, esto incluye las últimas actualizaciones del sistema. En el caso de las imágenes de Windows, esto incluye cualquier actualización que se marque como importante en Windows Update, incluidas las actualizaciones de seguridad importantes de SQL Server y los Service Pack. Las actualizaciones acumulativas de SQL Server se tratan de forma diferente para Linux y Windows. En Linux, las actualizaciones acumulativas de SQL Server también se incluyen en la actualización. Pero en este momento, las máquinas virtuales de Windows no se actualizan con las actualizaciones acumulativas de SQL Server o Windows Server.

1. **¿Qué paquetes relacionados de SQL Server se instalan también?**

   Para ver los paquetes de SQL Server que se instalan de manera predeterminada en máquinas virtuales Linux con SQL Server, consulte [Paquetes instalados](sql-server-linux-virtual-machines-overview.md#packages).

1. **¿Se pueden quitar las imágenes de máquinas virtuales de SQL Server de la galería?**

   Sí. Azure solo mantiene una imagen por edición y versión principal. Por ejemplo, cuando se lanza un nuevo Service Pack de SQL Server, Azure agrega una nueva imagen a la galería para dicho Service Pack. La imagen SQL Server para el Service Pack anterior se quita inmediatamente de Azure Portal. Sin embargo, todavía está disponible desde PowerShell para su aprovisionamiento durante los tres meses siguientes. Transcurridos tres meses, la imagen del Service Pack anterior dejará de estar disponible. Esta directiva de eliminación también se aplicaría si una versión de SQL Server no recibe soporte técnico cuando llega al final de su ciclo de vida.

## <a name="creation"></a>Creación

1. **¿Cómo puedo crear una máquina virtual Linux de Azure con SQL Server?**

   La solución más simple es crear una máquina virtual Linux que incluya SQL Server. Para ver un tutorial sobre cómo suscribirse a Azure y crear una máquina virtual de SQL desde el portal, consulte [Aprovisionamiento de una máquina virtual Linux con SQL Server en Azure Portal](provision-sql-server-linux-virtual-machine.md). También tiene la opción de instalar manualmente SQL Server en una máquina virtual con una edición de licencia libre (Developer o Express) o reutilizando una licencia local. Si trae su propia licencia, debe disponer de [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **¿Por qué no puedo aprovisionar una máquina virtual RHEL o SLES con SQL Server con una suscripción de Azure con un límite de gasto?**

   Las máquinas virtuales RHEL y SLES requieren una suscripción sin límite de gasto y con un método de pago comprobado (con frecuencia, una tarjeta de crédito) asociado con la suscripción. Si aprovisiona una máquina virtual RHEL o SLES sin quitar el límite de gasto, se deshabilitará la suscripción y se detendrán todas las máquinas virtuales o servicios. Si de todos modos ejecuta en este estado, para volver a habilitar la suscripción, [quite el límite de gasto](https://account.windowsazure.com/subscriptions). El crédito restante se restaurará para el ciclo de facturación actual, pero se le cobrará un recargo por la imagen de máquina virtual RHEL o SLES en la tarjeta de crédito si elige volver a iniciar la operación y sigue ejecutándola.

## <a name="licensing"></a>Licencias

1. **¿Cómo puedo instalar mi copia de SQL Server con licencia en una máquina virtual de Azure?**

   En primer lugar, cree una máquina virtual solo para Linux OS. Luego ejecute los [pasos de instalación de SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) para la distribución de Linux. Salvo que instale una de las ediciones de SQL Server de licencia abierta, debe tener también una licencia de SQL Server y [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **¿Hay imágenes de máquina virtual Linux de tipo "traiga su propia licencia" (BYOL) para SQL Server?**

   En este momento, no hay ninguna imagen de máquina virtual Linux BYOL para SQL Server. Sin embargo, puede instalar manualmente SQL Server en una máquina virtual solo Linux como se analizó en las preguntas anteriores.

1. **¿Puedo cambiar una máquina virtual para usar mi propia licencia de SQL Server si se ha creado desde una de las imágenes de la galería de pago por uso?**

   No. No puede cambiar de licencias de pago por minuto a usar su propia licencia. Debe crear una máquina virtual Linux nueva, instalar SQL Server y migrar los datos. Consulte la pregunta anterior para más detalles sobre cómo traer su propia licencia.

## <a name="administration"></a>Administración

1. **¿Puedo administrar una máquina virtual Linux de SQL Server con SQL Server Management Studio (SSMS)?**

   Sí, pero SSMS es actualmente una herramienta solo para Windows. Debe conectarse de forma remota desde una máquina Windows para usar SSMS con máquinas virtuales Linux de SQL Server. La nueva herramienta [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) puede realizar muchas tareas administrativas de forma local en Linux. Para obtener una vista previa de una herramienta de administración de base de datos multiplataforma, consulte [SQL Server Operations Studio (versión preliminar)](https://docs.microsoft.com/sql/sql-operations-studio/what-is).

## <a name="updating-and-patching"></a>Actualizaciones y revisiones

1. **¿Cómo actualizo a una nueva versión o edición de SQL Server en una máquina virtual de Azure?**

   Actualmente, no hay ninguna actualización para SQL Server que se ejecute en una máquina virtual de Azure. Cree una máquina virtual de Azure nueva con la versión o edición de SQL Server que desee y, luego, migre las bases de datos al servidor nuevo con las [técnicas de migración de datos estándar](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>General

1. **¿Las soluciones de alta disponibilidad de SQL Server son compatibles con las máquinas virtuales de Azure?**

   De momento, no. Tanto los grupos de disponibilidad AlwaysOn como los clústeres de conmutación por error requieren una solución de clústeres en Linux, como Pacemaker. La distribuciones de Linux compatibles con SQL Server no admiten sus complementos de alta disponibilidad en la nube.

## <a name="resources"></a>Recursos

**Máquinas virtuales Linux**:

* [Introducción a SQL Server en máquinas virtuales Linux](sql-server-linux-virtual-machines-overview.md)
* [Aprovisionamiento de una máquina virtual Linux con SQL Server](provision-sql-server-linux-virtual-machine.md)
* [Documentación de SQL Server en Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Máquinas virtuales Windows**:

* [Introducción a SQL Server en máquinas virtuales Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Aprovisionamiento de una máquina virtual Windows con SQL Server](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Preguntas más frecuentes (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)