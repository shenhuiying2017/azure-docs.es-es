<properties
	pageTitle="Preguntas más frecuentes sobre SQL Server en Máquinas virtuales de Azure | Microsoft Azure"
	description="Este artículo brinda respuestas a las preguntas más frecuentes sobre cómo ejecutar SQL Server en Máquinas virtuales de Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/15/2016"
	ms.author="jroth"/>

# Preguntas más frecuentes sobre SQL Server en Máquinas virtuales de Azure

En este tema se proporcionan respuestas a algunas de las preguntas más comunes sobre la ejecución de [SQL Server en Máquinas virtuales de Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

## Preguntas frecuentes

1. **¿Cómo puedo crear una máquina virtual de Azure con SQL Server?**

	Existen dos formas de hacerlo. La solución más simple es crear una máquina virtual que incluya SQL Server. Para ver un tutorial sobre cómo suscribirse a Azure y crear una máquina virtual de SQL desde el portal, consulte [Aprovisionamiento de una máquina virtual de SQL Server en el Portal de Azure](virtual-machines-windows-portal-sql-server-provision.md). También tiene la opción de instalar manualmente SQL Server en una máquina virtual y reutilizar una licencia local con [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **¿Cuál es la diferencia entre las máquinas virtuales de SQL y el servicio Base de datos SQL?**

	De manera conceptual, ejecutar SQL Server en una máquina virtual de Azure no es diferente a ejecutar SQL Server en un centro de datos remoto. En cambio, [Base de datos SQL](../sql-database/sql-database-technical-overview.md) ofrece base de datos como servicio. Con Base de datos SQL, no se tiene acceso a las máquinas que hospedan las bases de datos. Para ver una comparación completa, consulte [Selección de una opción de SQL Server en la nube: Base de datos (PaaS) SQL de Azure o SQL Server en máquinas virtuales de Azure (IaaS)](../sql-database/media/data-management-azure-sql-database-and-sql-server-iaas.md).

1. **¿Cómo puedo migrar mi base de datos local de SQL Server a la nube?**

	En primer lugar, cree una máquina virtual de Azure con una instancia de SQL Server. Luego, migre las bases de datos locales a esa instancia. Para conocer las estrategias de migración de datos, consulte [Migración de una Base de datos SQL Server a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md).

2. **¿Puedo cambiar las características instaladas o instalar una segunda instancia de SQL Server en la misma máquina virtual?**

	Sí. Los medios de instalación de SQL Server están ubicados en una carpeta en la unidad **C**. Ejecute **Setup.exe** desde esa ubicación para agregar instancias de SQL Server nuevas o para cambiar otras características instaladas de SQL Server en la máquina.

3. **¿Cómo actualizo a una nueva versión o edición de SQL Server en una máquina virtual de Azure?**

	Actualmente, no hay ninguna actualización para SQL Server que se ejecute en una máquina virtual de Azure. Cree una máquina virtual de Azure nueva con la versión o edición de SQL Server que desee y, luego, migre las bases de datos al servidor nuevo con las [técnicas de migración de datos](virtual-machines-windows-migrate-sql.md) estándar.

4. **¿Cómo puedo instalar mi copia de SQL Server con licencia en una máquina virtual de Azure?**

	Copie los medios de instalación de SQL Server en la máquina virtual de Windows Server y, luego, instale SQL Server en la máquina virtual. En lo que respecta a la licencia, debe tener [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/).

5. **¿Se deben pagar los costos de SQL de una máquina virtual si solo se usará para el modo de espera/conmutación por error?**

	Si crea la máquina virtual de SQL mediante la galería, debe tener una licencia independiente para la máquina virtual de SQL en modo de espera y los precios son los mismos. Si instala manualmente SQL Server en una máquina virtual con [Movilidad de licencias](https://azure.microsoft.com/pricing/license-mobility/), tiene la opción de tener una instancia SQL pasiva gratis para la conmutación por error. Revise las restricciones y los requisitos.

6. **¿Cómo se aplican las actualizaciones y los Service Packs en una máquina virtual de SQL Server?**

	Las máquinas virtuales brindan control sobre la máquina virtual, incluido el momento en que se aplican las actualizaciones y la manera de hacerlas. En el caso del sistema operativo, puede aplicar manualmente las actualizaciones de Windows, o bien puede habilitar un servicio de programación llamado [Aplicación de revisión automatizada](virtual-machines-windows-classic-sql-automated-patching.md). Aplicación de revisión automatizada instala las actualizaciones marcadas como importantes, incluidas las actualizaciones de SQL Server que tienen esa categoría. Otras actualizaciones opcionales a SQL Server se deben instalar manualmente.

7. **¿Es posible configurar configuraciones que no aparecen en la galería de máquinas virtuales (por ejemplo, Windows 2008 R2 + SQL Server 2012)?**

	No. En el caso de las imágenes de la galería de máquinas virtuales que incluyen SQL Server, debe seleccionar una de las imágenes que se proporcionan.

9. **¿Cómo instalo herramientas de datos de SQL en mi máquina virtual de Azure?**

	Descargue e instale las herramientas de datos de SQL desde [Microsoft SQL Server Data Tools - Business Intelligence para Visual Studio 2013 ](https://www.microsoft.com/es-ES/download/details.aspx?id=42313).

## Recursos

Si desea obtener información general sobre las Máquinas virtuales de Azure, vea el vídeo [Azure VM is the best platform for SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016) (VM de Azure es la mejor plataforma para SQL Server 2016). También puede consultar una buena introducción en el tema [Información general sobre SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md).

Otros recursos son los siguientes:

- [Aprovisionamiento de una máquina virtual de SQL Server en el Portal de Azure](virtual-machines-windows-portal-sql-server-provision.md)
- [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md)
- [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-high-availability-dr.md)
- [Prácticas recomendadas para mejorar el rendimiento para SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-performance.md)
- [Estrategias de desarrollo y patrones de aplicación de SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

<!---HONumber=AcomDC_0427_2016-->