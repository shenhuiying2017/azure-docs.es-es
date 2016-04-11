<properties
	pageTitle="Configuración de la integración de Almacén de claves de Azure para SQL Server en máquinas virtuales de Azure (Administrador de recursos)"
	description="Aprenda a automatizar la configuración de cifrado de SQL Server para su uso con Almacén de claves de Azure. Este tema explica cómo usar la integración de Almacén de claves de Azure con máquinas virtuales de SQL Server creadas con el Administrador de recursos."
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
	ms.date="03/24/2016"
	ms.author="jroth"/>

# Configuración de la integración de Almacén de claves de Azure para SQL Server en máquinas virtuales de Azure (Administrador de recursos)

> [AZURE.SELECTOR]
- [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
- [Clásico](virtual-machines-windows-classic-ps-sql-keyvault.md)

## Información general
SQL Server tiene varias características de cifrado, como el [cifrado de datos transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), el [cifrado de nivel de columna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) y la [copia de seguridad cifrada](https://msdn.microsoft.com/library/dn449489.aspx). Estas formas de cifrado requieren administrar y almacenar las claves criptográficas que se usan para el cifrado. El servicio de Almacén de claves de Azure (AKV) está diseñado para mejorar la seguridad y la administración de estas claves en una ubicación segura y con gran disponibilidad. El [conector de SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permite que SQL Server use estas claves de Almacén de claves de Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

Si se ejecuta SQL Server con equipos locales, hay una serie de [pasos a seguir para tener acceso a Almacén de claves de Azure desde el equipo de SQL Server local](https://msdn.microsoft.com/library/dn198405.aspx). Pero para SQL Server en las máquinas virtuales de Azure, puede ahorrar tiempo usando la característica *Integración de Almacén de claves de Azure*.

Cuando se habilita esta característica, automáticamente se instala el conector de SQL Server, se configura el proveedor EKM para obtener acceso a Almacén de claves de Azure y se crea la credencial para que pueda tener acceso a su almacén. Si examinamos los pasos descritos en la documentación local que se mencionó anteriormente, puede ver que esta característica automatiza los pasos 2 y 3. Lo único que aún tiene que hacer manualmente es crear el Almacén de claves y las claves. Desde allí, se automatiza toda la configuración de la máquina virtual de SQL. Cuando esta característica haya completado el programa de instalación, puede ejecutar instrucciones de T-SQL para empezar a cifrar sus bases de datos o copias de seguridad como lo haría normalmente.

[AZURE.INCLUDE [Preparación de la integración de AKV](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## Habilitación de la integración de AKV
Si está aprovisionando una nueva máquina virtual de SQL Server con Resource Manager, el Portal de Azure proporciona un paso para habilitar la integración del Almacén de claves de Azure.

![Integración con el Almacén de claves de Azure de SQL ARM](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Para obtener un tutorial detallado del aprovisionamiento, consulte [Aprovisionamiento de una máquina virtual de SQL Server en el Portal de Azure](virtual-machines-windows-portal-sql-server-provision.md).

Si necesita habilitar la integración de AKV en una máquina virtual existente, puede usar una plantilla. Para obtener más información, consulte la [plantilla de inicio rápido de Azure para la integración del Almacén de claves de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-keyvault-setup).


[AZURE.INCLUDE [Siguientes pasos de integración de AKV](../../includes/virtual-machines-sql-server-akv-next-steps.md)]

<!---HONumber=AcomDC_0330_2016-->