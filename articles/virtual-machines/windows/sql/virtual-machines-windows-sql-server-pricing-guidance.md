---
title: "Administración eficaz de costos de SQL Server en máquinas virtuales de Azure | Microsoft Docs"
description: "Se proporcionan procedimientos recomendados para elegir el modelo de fijación de precios adecuado de máquinas virtuales de SQL Server."
services: virtual-machines-windows
documentationcenter: na
author: luisherring
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/18/2017
ms.author: jroth
ms.openlocfilehash: 29a92f0c70bffedeb75c50b7fc3b687ee5ee227d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Orientación de precios de SQL Server para máquinas virtuales de Azure

En este tema se proporciona una guía sobre precios de máquinas virtuales de SQL Server en Azure. Hay varias opciones que afectan al costo, y es importante elegir la imagen correcta que equilibra los costes con los requisitos empresariales.

## <a name="free-licensed-sql-server-editions"></a>Ediciones de SQL Server con licencia gratuita

Si desea desarrollar, probar o generar una prueba de concepto, entonces utilice la versión **SQL Server Developer Edition** con licencia gratuita. Esta edición tiene todas las opciones de SQL Server Enterprise Edition, por lo que puede usarla para crear cualquier aplicación que desee. No obstante, no se puede ejecutar en producción. El cargo de las máquinas virtuales de SQL Server Developer solo se basará en el costo aplicado a las máquinas virtuales, y no en relación con la licencia de SQL Server.

Si desea ejecutar una carga de trabajo ligera en producción (< 4 núcleos, < 1 GB de memoria, < 10 GB/base de datos), entonces use la versión **SQL Server Express Edition** con licencia gratuita. En este caso, el cargo de una máquina virtual de SQL Express se basará exclusivamente en el costo de la máquina virtual, y no en relación con la licencia de SQL.

Para estas cargas de trabajo de producción ligeras o de desarrollo y pruebas, también puede ahorrar dinero si elige un tamaño de máquina virtual menor apto para estas cargas de trabajo. DS1v2 puede ser una buena elección para estas cargas de trabajo.

Para crear una máquina virtual de Azure de SQL Server 2016 con alguna de estas imágenes, vea los siguientes vínculos:

- [Máquina virtual de Azure en SQL Server 2016 Developer](https://ms.portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP1DeveloperWindowsServer2016-ARM)
- [Máquina virtual de Azure en SQL Server 2016 Express](https://ms.portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP1ExpressWindowsServer2016-ARM)

## <a name="paid-sql-server-editions"></a>Ediciones de pago de SQL Server

Si tiene una carga de trabajo de producción no ligera, use una de las siguientes ediciones de SQL Server:

| Edición de SQL Server | Carga de trabajo |
|-----|-----|
| Web | Sitios web pequeños |
| Estándar | Cargas de trabajo de pequeñas a medianas |
| Enterprise | Cargas de trabajo grandes o críticas|

Tiene dos opciones para pagar licencias de SQL Server para estas ediciones: *pago por uso* o *traiga su propia licencia*.

### <a name="pay-per-usage"></a>Pago por uso

**Pago por uso de la licencia de SQL Server**: el costo por minuto de ejecución de la máquina virtual de Azure incluye el costo de la licencia de SQL Server. Puede ver el precio de las diferentes ediciones de SQL Server (Web, Standard y Enterprise) en la [página de precios de máquinas virtuales de Azure](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard). El costo es el mismo para todas las versiones de SQL Server (2008 R2 a 2016). Al igual que con las licencias de SQL Server en general, el costo de las licencias por minuto depende del número de núcleos de la máquina virtual.

Se recomienda pagar las licencias por uso de SQL Server en los siguientes casos:

- Cargas de trabajo temporales o periódicas. Por ejemplo, una aplicación que necesita admitir un evento durante un par de meses cada año, o bien análisis de negocio todos los lunes.
- Cargas de trabajo con una duración o escala desconocidas. Por ejemplo, una aplicación que puede no ser necesaria en unos meses, o que puede requerir más o menos capacidad de proceso, según la demanda.

Para crear una máquina virtual de Azure de SQL Server 2016 con alguna de estas imágenes de pago por uso, vea los siguientes vínculos:

- [Máquina virtual de Azure en SQL Server 2016 Web](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016)
- [Máquina virtual de Azure en SQL Server 2016 Standard](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016)
- [Máquina virtual de Azure en SQL Server 2016 Enterprise](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016)

> [!IMPORTANT]
> Al crear una máquina virtual con SQL Server en Azure Portal, el costo mensual estimado que se muestra en la hoja **Elegir un tamaño** no incluye los costos de licencias de SQL Server. Este es el costo solo de la máquina virtual.
>
> ![Hoja Elegir un tamaño](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Para las ediciones SQL Server Express y Developer con licencia gratuita, este es el costo total estimado. En cambio, para las ediciones Web, Standard y Enterprise, puede consultar los costes de licencias de SQL adicionales en la [página de precios de máquinas virtuales Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). En la página de precios, seleccione la edición de destino de SQL Server.

### <a name="bring-your-own-license-byol"></a>Traiga su propia licencia (BYOL)

**Traiga su propia licencia de SQL Server a través de License Mobility**, que también se conoce como **BYOL**, consiste en usar una licencia por volumen existente de SQL Server con Software Assurance en una máquina virtual de Azure. Si se trata de una máquina virtual con SQL Server con licencia BYOL, solo se cargará el costo de la ejecución de la máquina virtual, no en relación con la licencia de SQL Server, puesto que ya ha adquirido licencias y Software Assurance a través de un programa de licencias por volumen.

Se recomienda traer su propia licencia de SQL a través de License Mobility para:

- Cargas de trabajo continuas. Por ejemplo, una aplicación que necesita admitir operaciones empresariales de forma ininterrumpida.
- Cargas de trabajo con una duración y escala desconocidas. Por ejemplo, una aplicación que será necesaria durante todo el año y cuya demanda ya se ha previsto.

Para usar BYOL con una máquina virtual con SQL Server, debe tener una licencia para SQL Server Standard o Enterprise y [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), que es una opción necesaria en algunos programas de [licencias por volumen](https://www.microsoft.com/en-us/download/details.aspx?id=10585) y una adquisición opcional en otros.  Los niveles de precios proporcionados a través de programas de licencias por volumen varían según el tipo de contrato y la cantidad y el compromiso con SQL Server. No obstante, por norma general, el modelo traiga su propia licencia para cargas de trabajo de producción continuas tiene las siguientes ventajas:

| Ventaja de BYOL | Descripción |
|-----|-----|
| **Ahorros en costos** | Traer su propia licencia de SQL Server resulta más rentable que pagar por uso si una carga de trabajo va a ejecutar continuamente SQL Server Standard o Enterprise durante *más de diez meses*. |
| **Ahorros a largo plazo** | Como promedio, resulta un *30 % más barato por año* comprar o renovar una licencia de SQL Server durante los tres primeros años. Además, después de tres años, ya no es necesario volver a renovar la licencia, y solo se paga por Software Assurance. En ese momento, resulta un *200 % más barato*. |
| **Réplica secundaria pasiva gratuita** | Otra ventaja que aporta traer su propia licencia es la [licencia gratuita para una réplica secundaria pasiva](https://azure.microsoft.com/pricing/licensing-faq/) por SQL Server para fines de alta disponibilidad. De esta forma, se reduce a la mitad el costo de una implementación de SQL Server de alta disponibilidad (por ejemplo, el uso de grupos de disponibilidad Always On). Los derechos para ejecutar la réplica secundaria pasiva se proporcionan con la ventaja de Software Assurance en servidores de conmutación por error. |

Para crear una máquina virtual de Azure de SQL Server 2016 con alguna de estas imágenes del modelo traiga su propia licencia, vea las máquinas virtuales con el prefijo "{BYOL}":

- [Máquina virtual de Azure en SQL Server 2016 Enterprise](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016)
- [Máquina virtual de Azure en SQL Server 2016 Standard](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016)

> [!NOTE]
> Infórmenos dentro de diez días de cuántas licencias de SQL Server usará en Azure. Los vínculos a las imágenes anteriores tienen instrucciones sobre cómo hacerlo.

## <a name="avoid-unecessary-costs"></a>Evitar costos innecesarios

Si usa cargas de trabajo que no se ejecutan continuamente, considere la posibilidad de apagar la máquina virtual durante los períodos inactivos. Pague solo por lo que usa.

Por ejemplo, si solo tiene intención de probar SQL Server en una máquina virtual de Azure, no querría incurrir en cargos por dejarla en ejecución durante semanas de forma accidental. Una solución consiste en utilizar la [característica de parada automática](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Parada automática de máquinas virtual de SQL](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

La parada automática forma parte de un conjunto mayor de características similares proporcionadas por [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Para otros flujos de trabajo, considere la posibilidad de parar y reiniciar automáticamente las máquinas virtuales con una solución de scripting, como [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Apagar la máquina virtual y desasignarla es la única manera de evitar cargos. Incurre en cargos por uso si solo detiene o usa opciones de energía para parar la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

Para consultar una guía de precios general de Azure, vea [Prevención de costos inesperados con la administración de costos y facturación de Azure](../../../billing/billing-getting-started.md).

Para conocer los últimos precios de Virtual Machines, incluido SQL Server, vea la [página de precios de máquinas virtuales de Azure](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard).

Revise otros temas de la máquina virtual de SQL Server en [Información general de SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
