---
title: Consideraciones de seguridad para SQL Server en Azure | Microsoft Docs
description: "En este tema se proporcionan instrucciones generales para proteger SQL Server en ejecución en una máquina virtual de Azure."
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
ms.date: 06/02/2017
ms.author: jroth
ms.openlocfilehash: 4ad9156e481eac0bae32bca35a2b126363e5d8b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Consideraciones de seguridad para SQL Server en Máquinas virtuales de Azure

En este tema se incluyen instrucciones de seguridad generales que ayudan a establecer el acceso seguro a instancias de SQL Server en una máquina virtual de Azure.

Azure cumple diversos reglamentos y estándares del sector que permiten compilar una solución compatible con SQL Server que se ejecuta en una máquina virtual. Para obtener información acerca del cumplimiento normativo de Azure, consulte [Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Control de acceso a la máquina virtual de SQL

Cuando se crea la máquina virtual de SQL Server, tenga en cuenta cómo controlar con cuidado quién tiene acceso a la máquina y a SQL Server. En general, siempre debe seguir estos pasos:

- Restringir el acceso a SQL Server solo a las aplicaciones y los clientes que lo necesiten.
- Seguir las prácticas recomendadas para administrar las contraseñas y cuentas de usuario.

En las secciones siguientes se proporcionan sugerencias relacionadas con estos puntos.

## <a name="secure-connections"></a>Conexiones seguras

Cuando se crea una máquina virtual de SQL Server con una imagen de la galería, la opción **Conectividad de SQL Server** le permite elegir entre **Local (dentro de la máquina virtual)**, **Privada (dentro de la red virtual)** o **Pública (Internet)**.

![Conectividad de SQL Server](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Para obtener una mayor seguridad, elija la opción más restrictiva para su escenario. Por ejemplo, si está ejecutando una aplicación que tiene acceso a SQL Server en la misma máquina virtual, entonces **Local** es la opción más segura. Si está ejecutando una aplicación de Azure que requiere acceso a SQL Server, entonces **Privada** protege la comunicación con SQL Server solo dentro de la [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) especificada. Si necesita acceso **Público** (Internet) a la máquina virtual de SQL Server, asegúrese de seguir otras prácticas recomendadas en este tema para reducir el área expuesta a los ataques.

Las opciones seleccionadas en el portal usan reglas de seguridad de entrada en el [Grupo de seguridad de red](../../../virtual-network/virtual-networks-nsg.md) (NSG) de las máquinas virtuales para permitir o denegar el tráfico de red a la máquina virtual. Puede modificar o crear nuevas reglas NSG de entrada para permitir el tráfico al puerto de SQL Server (el predeterminado es 1433). También puede especificar direcciones IP concretas que tengan permiso para comunicarse a través de este puerto.

![Reglas del grupo de seguridad de red](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Además de las reglas NSG para restringir el tráfico de red, también puede usar el Firewall de Windows en la máquina virtual.

Si está usando puntos de conexión con el modelo de implementación clásica, quite de la máquina virtual todos los puntos de conexión que no use. Para obtener instrucciones sobre el uso de ACL con puntos de conexión, consulte [Administrar la ACL en un punto de conexión](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint). Esto no es necesario para las máquinas virtuales que usan Resource Manager.

Por último, considere la posibilidad de habilitar conexiones cifradas para la instancia del motor de base de datos de SQL Server en la máquina virtual de Azure. Configure la instancia de SQL Server con un certificado firmado. Para más información, consulte [Habilitar conexiones cifradas en el motor de base de datos](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) y [Sintaxis de cadena de conexión](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Usar un puerto no predeterminado

De forma predeterminada, SQL Server escucha en un puerto conocido, 1433. Para aumentar la seguridad, configure SQL Server para que escuche en un puerto no predeterminado, como 1401. Si se aprovisiona una imagen de la galería de SQL Server en Azure Portal, puede especificar este puerto en la hoja **Configuración de SQL Server**.

Para configurar esto después del aprovisionamiento, tiene dos opciones:

- Para máquinas virtuales de Resource Manager puede seleccionar **Configuración de SQL Server** en la hoja de información general de la máquina virtual. Esto proporciona una opción para cambiar el puerto.

  ![Cambiar el puerto TCP en el portal](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Para las máquinas virtuales clásicas o las máquinas virtuales de SQL Server que no se aprovisionaron con el portal, puede configurar manualmente el puerto mediante la conexión de forma remota a la máquina virtual. Para obtener los pasos de configuración, vea [Configure a Server to Listen on a Specific TCP Port](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port) (Configurar un servidor para que escuche en un puerto TCP específico). Si usa esta técnica manual, también debe agregar una regla de Firewall de Windows para permitir el tráfico entrante en ese puerto TCP.

> [!IMPORTANT]
> La especificación de un puerto no predeterminado es una buena idea si el puerto de SQL Server está abierto para las conexiones públicas a Internet.

Cuando SQL Server escucha en un puerto no predeterminado, debe especificar el puerto al conectarse. Por ejemplo, considere un escenario donde la dirección IP del servidor es 13.55.255.255 y SQL Server está escuchando en el puerto 1401. Para conectarse a SQL Server, debe especificar `13.55.255.255,1401` en la cadena de conexión.

## <a name="manage-accounts"></a>Administrar cuentas

No querrá que los atacantes adivinen fácilmente los nombres de las cuentas o las contraseñas. Use las sugerencias siguientes como ayuda para:

- Cree una cuenta de administrador local única que no se llame **Administrador**.

- Use contraseñas seguras complejas para todas sus cuentas. Para más información sobre cómo crear una contraseña segura, vea el artículo [Crear una contraseña segura](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password).

- De forma predeterminada, Azure selecciona la autenticación de Windows durante la instalación de la máquina virtual de SQL Server. Por lo tanto, el inicio de sesión de **SA** está deshabilitado y el programa de instalación asigna una contraseña. Se recomienda no usar ni habilitar el inicio de sesión de **SA**. Si debe tener un inicio de sesión de SQL, use una de las estrategias siguientes:

  - Cree una cuenta de SQL con un nombre único que sea miembro de **sysadmin**. Puede hacerlo desde el portal si habilita **Autenticación de SQL** durante el aprovisionamiento.

    > [!TIP] 
    > Si no habilita la autenticación de SQL durante el aprovisionamiento, debe cambiar manualmente el modo de autenticación a **Modo de autenticación de Windows y SQL Server**. Para obtener más información, consulte [Cambiar el modo de autenticación del servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Si tiene que usar el inicio de sesión de **SA**, habilite el inicio de sesión después del aprovisionamiento y asigne una nueva contraseña segura.

## <a name="follow-on-premises-best-practices"></a>Seguir las prácticas recomendadas locales

Además de los procedimientos descritos en este tema, se recomienda que revise e implemente las prácticas de seguridad local tradicionales, si procede. Para más información, vea [Consideraciones de seguridad para una instalación de SQL Server](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation).

## <a name="next-steps"></a>Pasos siguientes

Si también está interesado en los procedimientos recomendados de rendimiento, consulte [Procedimientos recomendados para SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-performance.md).

Para ver otros temas sobre la ejecución de SQL Server en máquinas virtuales de Azure, consulte [Información general sobre SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md).

