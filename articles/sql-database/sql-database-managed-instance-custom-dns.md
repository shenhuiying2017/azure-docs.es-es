---
title: DNS personalizado de Instancia administrada de Azure SQL Database | Microsoft Docs
description: En este tema se describen las opciones de configuración para un DNS personalizado con Instancia administrada de Azure SQL Database.
services: sql-database
author: srdjan-bozovic
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 3175b99c0e41cedf313115043b09608496adfdca
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configuración de un DNS personalizado para Instancia administrada de Azure SQL Database

Instancia administrada de Azure SQL Database (versión preliminar) debe implementarse dentro de una [red virtual](../virtual-network/virtual-networks-overview.md) de Azure. Hay algunos escenarios y servidores vinculados a otras instancias de SQL en el entorno híbrido o en la nube, que requieren que los nombres de host privados se resuelvan en Instancia administrada. En este caso, debe configurar un DNS personalizado dentro de Azure. Puesto que Instancia administrada utiliza el mismo DNS para su funcionamiento interno, la configuración de DNS de la red virtual debe ser compatible con Instancia administrada. 

Para realizar una configuración personalizada de DNS compatible con Instancia administrada, debe completar los pasos siguientes: 
- Configuración de DNS personalizado para reenvío de solicitudes a Azure DNS 
- Configuración de DNS personalizado como principal y de Azure DNS como secundario para la red virtual 
- Registro de DNS personalizado como principal y de Azure DNS como secundario

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>Configuración de DNS personalizado para reenvío de solicitudes a Azure DNS 

Para configurar el reenvío de DNS en Windows Server 2016, siga estos pasos: 

1. En el **Administrador del servidor**, haga clic en **Herramientas** y, a continuación, en **DNS**. 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. Haga doble clic en **Reenviadores**.

   ![Reenviadores](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. Haga clic en **Editar**. 

   ![Lista de reenviadores](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Escriba la dirección IP de las resoluciones recursivas de Azure, como 168.63.129.16.

   ![Dirección IP de resoluciones recursivas](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Configuración de DNS personalizado como principal y de Azure DNS como secundario 
 
La configuración de DNS en una red virtual de Azure requiere que especifique las direcciones IP, por lo que debe configurar la máquina virtual de Azure que hospeda el servidor DNS con una dirección IP estática mediante los pasos siguientes: 

1. En Azure Portal, abra la interfaz de red de máquina virtual de DNS personalizado.

   ![Interfaz de red](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. En la sección Configuraciones de IP, seleccione la configuración de IP. 

   ![Configuración de IP](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. Establezca la dirección IP privada como Estática. Anote la dirección IP (10.0.1.5 en esta captura de pantalla). 

   ![estática](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Registro de DNS personalizado como principal y de Azure DNS como secundario 

1. En Azure Portal, busque la opción DNS personalizado para la red virtual.

   ![Opción DNS personalizado](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Cambie a Personalizado y escriba la dirección IP del servidor DNS personalizado, así como la dirección IP de resoluciones recursivas de Azure, como 168.63.129.16. 

   ![Opción DNS personalizado](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Si no se configura la resolución recursiva de Azure en la lista de DNS, se genera un error en el estado de Instancia administrada. La recuperación a partir de un estado así puede requerir la creación de una nueva instancia en una red virtual con las directivas de red compatibles, la creación de los datos a nivel de instancia y la restauración de las bases de datos. Consulte [Configuración de VNET](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Pasos siguientes

- Para información general, consulte [¿Qué es Instancia administrada de SQL Database (versión preliminar)?](sql-database-managed-instance.md)
- Para un tutorial que muestra cómo crear una instancia administrada, consulte el artículo de [creación de instancias administradas](sql-database-managed-instance-create-tutorial-portal.md).
- Para información sobre cómo configurar una red virtual para una instancia administrada, consulte el artículo sobre la [configuración de la red virtual para las instancias administradas](sql-database-managed-instance-vnet-configuration.md).
