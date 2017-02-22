---
title: Consideraciones de seguridad para SQL Server en Azure | Microsoft Docs
description: "Este tema hace referencia a los recursos creados con el modelo de implementación clásica y proporciona instrucciones generales para la seguridad de SQL Server que se ejecuta en una máquina virtual de Azure."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/15/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 7402249aa87ffe985ae13f28a701e22af3afd450
ms.openlocfilehash: 12df603e22c8c735bf5c3f892575e79725f7313e


---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Consideraciones de seguridad para SQL Server en Máquinas virtuales de Azure
Este tema incluye las direcciones de seguridad generales que ayudan a establecer el acceso seguro a instancias de SQL Server en una máquina virtual de Azure. Sin embargo, para garantizar una mejor protección para las instancias de base de datos de SQL Server en Azure, es recomendable que implemente los procedimientos de seguridad locales tradicionales además de los procedimientos recomendados de seguridad de Azure.

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

Para obtener más información sobre los procedimientos de seguridad de SQL Server, consulte [Procedimientos recomendados de seguridad en SQL Server 2008 R2: tareas operativas y administrativas](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

Azure cumple diversos reglamentos y estándares del sector que permiten compilar una solución compatible con SQL Server que se ejecuta en una máquina virtual. Para obtener información acerca del cumplimiento normativo de Azure, consulte [Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/).

La siguiente es una lista de recomendaciones de seguridad para tener en cuenta a la hora de configurar y conectarse a la instancia de SQL Server en una VM de Azure.

## <a name="considerations-for-managing-accounts"></a>Consideraciones para administrar cuentas:
* Cree una cuenta de administrador local única que no se llame **Administrador**.
* Use contraseñas seguras complejas para todas sus cuentas. Para más información sobre cómo crear una contraseña segura, consulte el artículo [Sugerencias para crear una contraseña segura](http://windows.microsoft.com/en-us/windows-vista/Tips-for-creating-a-strong-password) .
* De forma predeterminada, Azure selecciona la autenticación de Windows durante la instalación de la máquina virtual de SQL Server. Por lo tanto, el inicio de sesión de **SA** está deshabilitado y el programa de instalación asigna una contraseña. Se recomienda no usar ni habilitar el inicio de sesión de **SA** . Las siguientes son estrategias alternativas si se desea un inicio de sesión de SQL:
  
  * Crear una cuenta SQL que sea miembro de sysadmin.
  * Si tiene que usar un inicio de sesión de **SA** , habilite el inicio de sesión, cámbiele el nombre y asígnele una nueva contraseña.
  * Ambas opciones mencionadas requieren cambiar el modo de autenticación al **modo de autenticación de Windows y SQL Server**. Para obtener más información, consulte [Cambiar el modo de autenticación del servidor](https://msdn.microsoft.com/library/ms188670.aspx).

## <a name="considerations-for-securing-connections-to-azure-virtual-machine"></a>Consideraciones para proteger las conexiones con la máquina virtual de Azure:
* Considere la posibilidad de usar [Red virtual de Azure](../../../virtual-network/virtual-networks-overview.md) para administrar las máquinas virtuales en lugar de los puertos públicos RDP.
* Use un [grupo de seguridad de red](../../../virtual-network/virtual-networks-nsg.md) (NSG) para permitir o denegar el tráfico de red a la máquina virtual. Si desea usar un grupo de seguridad de red y ya tiene un extremo del ACL, quite primero el extremo del ACL. Para obtener información acerca de cómo hacerlo, consulte [Administración de listas de control de acceso (ACL) para extremos mediante  PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Si está utilizando puntos de conexión, quite de la máquina virtual todos los puntos de conexión que no use. Para obtener instrucciones sobre el uso de ACL con puntos de conexión, consulte [Administrar la ACL en un punto de conexión](../../virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).
* Habilite una opción de conexión cifrada para una instancia de motor de base de datos de SQL Server en máquinas virtuales de Azure. Configure la instancia de SQL Server con un certificado firmado. Para más información, consulte [Habilitar conexiones cifradas en el motor de base de datos](https://msdn.microsoft.com/library/ms191192.aspx) y [Sintaxis de cadena de conexión](https://msdn.microsoft.com/library/ms254500.aspx).
* Si el acceso a las máquinas virtuales debe realizarse solo desde una red específica, use Firewall de Windows para restringir el acceso a ciertas direcciones IP o subredes de red.

## <a name="next-steps"></a>Pasos siguientes
Si también está interesado en los procedimientos recomendados de rendimiento, consulte [Procedimientos recomendados para SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-performance.md).

Para ver otros temas sobre la ejecución de SQL Server en máquinas virtuales de Azure, consulte [Información general sobre SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md).




<!--HONumber=Jan17_HO2-->


