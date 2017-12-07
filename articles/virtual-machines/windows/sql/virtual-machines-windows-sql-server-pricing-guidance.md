---
title: "Administración eficaz de costos de SQL Server en máquinas virtuales de Azure | Microsoft Docs"
description: "Se proporcionan procedimientos recomendados para elegir el modelo de fijación de precios adecuado de máquinas virtuales de SQL Server."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/17/2017
ms.author: jroth
ms.openlocfilehash: fa1611944d266001a54c4d78205c942a5226d97b
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Orientación de precios de SQL Server para máquinas virtuales de Azure

En este artículo se proporcionan orientaciones sobre precios de máquinas virtuales de SQL Server en Azure. Hay varias opciones que afectan al costo, y es importante elegir la imagen correcta que equilibra los costes con los requisitos empresariales.

## <a name="free-licensed-sql-server-editions"></a>Ediciones de SQL Server con licencia gratuita

Si desea desarrollar, probar o generar una prueba de concepto, entonces utilice la versión **SQL Server Developer Edition** con licencia gratuita. Esta edición tiene todas las opciones de SQL Server Enterprise Edition, por lo que puede usarla para crear cualquier aplicación que desee. Solo no se permite su ejecución en producción. El cargo de las máquinas virtuales de SQL Server Developer solo se basa en el costo aplicado a las máquinas virtuales, y no en la licencia de SQL Server.

Si desea ejecutar una carga de trabajo ligera en producción (< 4 núcleos, < 1 GB de memoria, < 10 GB/base de datos), entonces use la versión **SQL Server Express Edition** con licencia gratuita. En este caso, el cargo de una máquina virtual de SQL Express se basa exclusivamente en el costo de la máquina virtual, y no en la licencia de SQL.

Para estas cargas de trabajo de producción ligeras o de desarrollo y pruebas, también puede ahorrar dinero si elige un tamaño de máquina virtual menor apto para estas cargas de trabajo. DS1v2 puede ser una buena elección para estas cargas de trabajo.

Para crear una máquina virtual de Azure de SQL Server 2017 con alguna de estas imágenes, vea los siguientes vínculos:

| Plataforma | Imágenes con licencia gratuita |
|---|---|
| Windows Server 2016 | [Máquina virtual de Azure en SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[Máquina virtual de Azure en SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [Máquina virtual de Azure en SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[Máquina virtual de Azure en SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Máquina virtual de Azure en SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[Máquina virtual de Azure en SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [Máquina virtual de Azure en SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[Máquina virtual de Azure en SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Ediciones de pago de SQL Server

Si tiene una carga de trabajo de producción no ligera, use una de las siguientes ediciones de SQL Server:

| Edición de SQL Server | Carga de trabajo |
|-----|-----|
| Web | Sitios web pequeños |
| Estándar | Cargas de trabajo de pequeñas a medianas |
| Enterprise | Cargas de trabajo grandes o críticas|

Tiene dos opciones para pagar licencias de SQL Server para estas ediciones: *pago por uso* o *traiga su propia licencia (BYOL)*.

### <a name="pay-per-usage"></a>Pago por uso

**Pago por uso de la licencia de SQL Server**: el costo por minuto de ejecución de la máquina virtual de Azure incluye el costo de la licencia de SQL Server. Puede ver el precio de las diferentes ediciones de SQL Server (Web, Standard y Enterprise) en la [página de precios de máquinas virtuales de Azure](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard). El costo es el mismo para todas las versiones de SQL Server (2012 SP3 a 2017). Al igual que con las licencias de SQL Server en general, el costo de las licencias por minuto depende del número de núcleos de la máquina virtual.

Se recomienda pagar las licencias por uso de SQL Server en los siguientes casos:

- Cargas de trabajo temporales o periódicas. Por ejemplo, una aplicación que necesita admitir un evento durante un par de meses cada año, o bien análisis de negocio todos los lunes.
- Cargas de trabajo con una duración o escala desconocidas. Por ejemplo, una aplicación que puede no ser necesaria en unos meses, o que puede requerir más o menos capacidad de proceso, según la demanda.

Para crear una máquina virtual de Azure de SQL Server 2017 con alguna de estas imágenes de pago por uso, vea los siguientes vínculos:

| Plataforma | Imágenes con licencia |
|---|---|
| Windows Server 2016 | [Máquina virtual de Azure en SQL Server 2017 Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[Máquina virtual de Azure en SQL Server 2017 Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[Máquina virtual de Azure en SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [Máquina virtual de Azure en SQL Server 2017 Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[Máquina virtual de Azure en SQL Server 2017 Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[Máquina virtual de Azure en SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Máquina virtual de Azure en SQL Server 2017 Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[Máquina virtual de Azure en SQL Server 2017 Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[Máquina virtual de Azure en SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [Máquina virtual de Azure en SQL Server 2017 Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[Máquina virtual de Azure en SQL Server 2017 Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[Máquina virtual de Azure en SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Cuando se crea una máquina virtual de SQL Server en el portal, la ventana **Elegir un tamaño** muestra un costo estimado. Es importante tener en cuenta que esta estimación incluye solo los costos de proceso derivados de la ejecución de la VM junto con los costos de licencia de Windows para las VM de Windows. No incluye los costos de licencia adicionales de SQL Server para las ediciones Web, Standard y Enterprise. Tampoco incluye los costos de licencia adicionales para los sistemas operativos de Linux de terceros para VM de Linux. Para obtener la estimación más precisa de precios, seleccione el sistema operativo y la edición de SQL Server en la página de precios para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) y [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).
>
> ![Hoja Elegir un tamaño](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)

### <a name="bring-your-own-license-byol"></a>Traiga su propia licencia (BYOL)

**Traiga su propia licencia de SQL Server a través de License Mobility**, que también se conoce como **BYOL**, consiste en usar una licencia por volumen existente de SQL Server con Software Assurance en una máquina virtual de Azure. Si se trata de una máquina virtual con SQL Server con licencia BYOL, solo se carga el costo de la ejecución de la máquina virtual, no el relativo a la licencia de SQL Server, puesto que ya ha adquirido licencias y Software Assurance a través de un programa de licencias por volumen.

> [!NOTE]
> Actualmente, las imágenes BYOL solo están disponibles para máquinas virtuales de Windows. Sin embargo, puede instalar manualmente SQL Server en una máquina virtual con solo Linux. Consulte las instrucciones en [Preguntas más frecuentes para SQL Server en máquinas virtuales Linux de Azure](../../linux/sql/sql-server-linux-faq.md).

Se recomienda traer su propia licencia de SQL a través de License Mobility para:

- Cargas de trabajo continuas. Por ejemplo, una aplicación que necesita admitir operaciones empresariales de forma ininterrumpida.
- Cargas de trabajo con una duración y escala desconocidas. Por ejemplo, una aplicación que sea necesaria durante todo el año y cuya demanda ya se ha previsto.

Para usar BYOL con una máquina virtual con SQL Server, debe tener una licencia para SQL Server Standard o Enterprise y [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), que es una opción necesaria en algunos programas de [licencias por volumen](https://www.microsoft.com/en-us/download/details.aspx?id=10585) y una adquisición opcional en otros.  Los niveles de precios proporcionados a través de programas de licencias por volumen varían según el tipo de contrato y la cantidad y el compromiso con SQL Server. No obstante, por norma general, el modelo traiga su propia licencia para cargas de trabajo de producción continuas tiene las siguientes ventajas:

| Ventaja de BYOL | Descripción |
|-----|-----|
| **Ahorros en costos** | Traer su propia licencia de SQL Server resulta más rentable que pagar por uso si una carga de trabajo ejecuta continuamente SQL Server Standard o Enterprise durante *más de diez meses*. |
| **Ahorros a largo plazo** | Como promedio, resulta un *30 % más barato por año* comprar o renovar una licencia de SQL Server durante los tres primeros años. Además, después de tres años, ya no es necesario volver a renovar la licencia, y solo se paga por Software Assurance. En ese momento, resulta un *200 % más barato*. |
| **Réplica secundaria pasiva gratuita** | Otra ventaja que aporta traer su propia licencia es la [licencia gratuita para una réplica secundaria pasiva](https://azure.microsoft.com/pricing/licensing-faq/) por SQL Server para fines de alta disponibilidad. De esta forma, se reduce a la mitad el costo de una implementación de SQL Server de alta disponibilidad (por ejemplo, el uso de grupos de disponibilidad Always On). Los derechos para ejecutar la réplica secundaria pasiva se proporcionan con la ventaja de Software Assurance en servidores de conmutación por error. |

Para crear una máquina virtual de Azure de SQL Server 2016 con alguna de estas imágenes del modelo traiga su propia licencia, vea las máquinas virtuales con el prefijo "{BYOL}":

- [Máquina virtual de Azure en SQL Server 2016 Enterprise](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016)
- [Máquina virtual de Azure en SQL Server 2016 Standard](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016)

> [!IMPORTANT]
> Infórmenos dentro de diez días de cuántas licencias de SQL Server usará en Azure. Los vínculos a las imágenes anteriores tienen instrucciones sobre cómo hacerlo.

> [!NOTE]
> No es posible cambiar el modelo de licencias de una máquina virtual con SQL Server de pago por minuto para usar su propia licencia. En este caso, debe crear una nueva máquina virtual BYOL y migrar las bases de datos a ella. 

## <a name="avoid-unnecessary-costs"></a>Evitación de costos innecesarios

Para evitar costos innecesarios, elija un tamaño de máquina virtual óptimo y considere la posibilidad de realizar apagados intermitentes para cargas de trabajo continuas.

### <a id="machinesize"></a> Selección del tamaño adecuado de VM

El costo de las licencias de SQL Server está directamente relacionado con el número de núcleos. Elija un tamaño de memoria virtual que coincida con sus necesidades previstas de CPU, memoria, almacenamiento y ancho de banda de E/S. Para obtener una lista completa de opciones de tamaño de máquinas, vea [Tamaños de las máquinas virtuales Windows en Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) y [Tamaños de las máquinas virtuales Linux en Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Hay nuevos tamaños de máquina que funcionan bien con determinados tipos de cargas de trabajo de SQL Server. Estos tamaños de máquinas mantienen altos niveles de memoria, almacenamiento y ancho de banda de E/S, pero tienen una cantidad inferior de núcleos virtuales. Por ejemplo, considere el siguiente ejemplo:

| Tamaño de VM | vCPU | Memoria | Discos máx. | Rendimiento de E/S máx. | Costos de licencia de SQL | Costos totales (proceso + licencias) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51 200 IOPS o 768 MB/s | | |
| **Standard_DS14 4v2** | 4 | 112 GB | 32 | 51 200 IOPS o 768 MB/s | 75 % menos | 57 % menos |

> [!IMPORTANT]
> Se trata de un ejemplo de un momento en el tiempo. Para conocer las especificaciones más recientes, vea los artículos de tamaños de máquina y la página de precios de Azure para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) y [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

En el ejemplo anterior, puede ver que las especificaciones de **Standard_DS14v2** y **Standard_DS14-4v2** son idénticas, a excepción de vCPU. El sufijo **-4v2** al final del tamaño de máquina **Standard_DS14-4v2** indica el número de vCPU activos. Dado que los costos de licencia de SQL Server están asociados al número de núcleos, esto reduce considerablemente el costo derivado de VM en escenarios donde no se necesitan vCPU adicionales. Esto es solo un ejemplo, y hay muchos tamaños de máquina con vCPU restringidas que se identifican con este patrón de sufijo. Para obtener más información, consulte la entrada de blog [Announcing new Azure VM sizes for more cost-effective database work](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/) (Anuncio de nuevos tamaños de VM para un trabajo de base de datos rentable).

### <a name="shut-down-your-vm-when-possible"></a>Apagado de la máquina virtual siempre que sea posible

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

Más información acerca de las máquinas virtuales de SQL Server para [SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md) y [SQL Server en máquinas virtuales de Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md).
