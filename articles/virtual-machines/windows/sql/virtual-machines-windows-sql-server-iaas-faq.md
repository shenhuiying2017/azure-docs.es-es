---
title: "Preguntas más frecuentes sobre SQL Server en Azure Virtual Machines | Microsoft Docs"
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
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/07/2017
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 42b3e222f24e9b5c1bb26f7e1e36b1e11c2f48b8
ms.openlocfilehash: 7a8d1451fc1b5ace2996faec22090b1f872bf547


---
# <a name="sql-server-on-azure-virtual-machines-faq"></a>Preguntas más frecuentes sobre SQL Server en Máquinas virtuales de Azure
En este tema se proporcionan respuestas a algunas de las preguntas más comunes sobre la ejecución de [SQL Server en Máquinas virtuales de Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Preguntas frecuentes
1. **¿Cómo puedo crear una máquina virtual de Azure con SQL Server?**
   
    La solución más simple es crear una máquina virtual que incluya SQL Server. Para ver un tutorial sobre cómo suscribirse a Azure y crear una máquina virtual de SQL desde el portal, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Puede seleccionar una imagen de máquina virtual que use licencias de SQL Server de pago por minuto o puede utilizar una imagen que le permita traer su propia licencia de SQL Server. También tiene la opción de instalar manualmente SQL Server en una máquina virtual y reutilizar una licencia local. Si trae su propia licencia, debe disponer de [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. **¿Cuál es la diferencia entre las máquinas virtuales de SQL y el servicio Base de datos SQL?**
   
    De manera conceptual, ejecutar SQL Server en una máquina virtual de Azure no es diferente a ejecutar SQL Server en un centro de datos remoto. En cambio, [Base de datos SQL](../../../sql-database/sql-database-technical-overview.md) ofrece base de datos como servicio. Con Base de datos SQL, no se tiene acceso a las máquinas que hospedan las bases de datos. Para ver una comparación completa, consulte [Selección de una opción de SQL Server en la nube: Base de datos (PaaS) SQL de Azure o SQL Server en máquinas virtuales de Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).
3. **¿Cómo puedo migrar mi base de datos local de SQL Server a la nube?**
   
    En primer lugar, cree una máquina virtual de Azure con una instancia de SQL Server. Luego, migre las bases de datos locales a esa instancia. Para conocer las estrategias de migración de datos, consulte [Migración de una Base de datos SQL Server a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md).
4. **¿Puedo cambiar las características instaladas o instalar una segunda instancia de SQL Server en la misma máquina virtual?**
   
    Sí. Los medios de instalación de SQL Server están ubicados en una carpeta en la unidad **C** . Ejecute **Setup.exe** desde esa ubicación para agregar instancias de SQL Server nuevas o para cambiar otras características instaladas de SQL Server en la máquina.
5. **¿Cómo actualizo a una nueva versión o edición de SQL Server en una máquina virtual de Azure?**
   
    Actualmente, no hay ninguna actualización para SQL Server que se ejecute en una máquina virtual de Azure. Cree una máquina virtual de Azure nueva con la versión o edición de SQL Server que desee y, luego, migre las bases de datos al servidor nuevo con las [técnicas de migración de datos](virtual-machines-windows-migrate-sql.md)estándar.
6. **¿Cómo puedo instalar mi copia de SQL Server con licencia en una máquina virtual de Azure?**
   
    Existen dos formas de hacerlo. Puede aprovisionar una de las [imágenes de máquina virtual que admita licencias](virtual-machines-windows-sql-server-iaas-overview.md#BYOL). También existe la posibilidad de copiar los medios de instalación de SQL Server en la máquina virtual de Windows Server y, luego, instalar SQL Server en la máquina virtual. En lo que respecta a la licencia, debe tener [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/).
7. **¿Puedo cambiar una máquina virtual para usar mi propia licencia de SQL Server si se ha creado desde una de las imágenes de la galería de pago por uso?**

    No. No puede cambiar de licencias de pago por minuto a usar su propia licencia. Cree una máquina virtual de Azure nueva usando una de las [imágenes BYOL](virtual-machines-windows-sql-server-iaas-overview.md#BYOL) y, luego, migre las bases de datos al nuevo servidor con las [técnicas de migración de datos](virtual-machines-windows-migrate-sql.md) estándar.

7. **¿Son compatibles las instancias del clúster de conmutación por error (FCI) de SQL Server con Azure Virtual Machines?**

   Sí. También puede [crear un clúster de conmutación por error de Windows Server \(WSFC\) en Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) y usar Espacios de almacenamiento directo (S2D) para el almacenamiento del clúster. También puede usar soluciones de agrupación en clústeres o almacenamiento de terceros como se describe en [Alta disponibilidad y recuperación ante desastres para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

7. **¿Tengo que pagar para obtener una licencia de SQL Server en una máquina virtual de Azure si solo se usa para conmutación por error o en modo en espera?**
   
    No tiene que pagar por obtener una licencia de SQL Server, como réplica secundaria pasiva en una implementación de alta disponibilidad, si dispone de Software Assurance y usa la Movilidad de Licencias como se describe en [P+F sobre licencias de máquinas virtuales](http://azure.microsoft.com/pricing/licensing-faq/).
    
8. **¿Cómo se aplican las actualizaciones y los Service Packs en una máquina virtual de SQL Server?**
   
    Las máquinas virtuales brindan control sobre la máquina virtual, incluido el momento en que se aplican las actualizaciones y la manera de hacerlas. En el caso del sistema operativo, puede aplicar manualmente las actualizaciones de Windows, o bien puede habilitar un servicio de programación llamado [Aplicación de revisiones automatizada](virtual-machines-windows-sql-automated-patching.md). Aplicación de revisión automatizada instala las actualizaciones marcadas como importantes, incluidas las actualizaciones de SQL Server que tienen esa categoría. Otras actualizaciones opcionales a SQL Server se deben instalar manualmente.
9. **¿Es posible configurar configuraciones que no aparecen en la galería de máquinas virtuales (por ejemplo, Windows 2008 R2 + SQL Server 2012)?**
   
    No. En el caso de las imágenes de la galería de máquinas virtuales que incluyen SQL Server, debe seleccionar una de las imágenes que se proporcionan.
10. **¿Cómo instalo herramientas de datos de SQL en mi máquina virtual de Azure?**
    
     Descargue e instale las herramientas de datos de SQL desde [Microsoft SQL Server Data Tools - Inteligencia empresarial para Visual Studio 2013 ](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Recursos
Si desea obtener información general sobre Máquinas virtuales de Azure, vea el vídeo [Azure VM is the best platform for SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016)(VM de Azure es la mejor plataforma para SQL Server 2016). También puede consultar una buena introducción en el tema [Información general sobre SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

Otros recursos son los siguientes:

* [Aprovisionamiento de una máquina virtual de SQL Server en Azure Portal](virtual-machines-windows-portal-sql-server-provision.md)
* [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidad y recuperación ante desastres para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Procedimientos recomendados para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Estrategias de desarrollo y patrones de aplicación de SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)




<!--HONumber=Feb17_HO2-->


