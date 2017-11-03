---
title: "Integración del centro de datos de Azure Stack: Seguridad"
description: Aprenda a integrar la seguridad de Azure Stack con la seguridad de su centro de datos.
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/17/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: eb7c651362838d44d6558e080e6130b4a8041d1e
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2017
---
# <a name="azure-stack-datacenter-integration---security"></a>Integración del centro de datos de Azure Stack: Seguridad

*Se aplica a: sistemas integrados de Azure Stack*

Azure Stack se ha diseñado y compilado con una atención especial a la seguridad. Azure Stack es un sistema bloqueado, por lo que no se admite la instalación de agentes de seguridad de software.

Este artículo le ayudará a integrar las características de seguridad de Azure Stack con las soluciones de seguridad ya implementadas en su centro de datos.

## <a name="security-logs"></a>Registros de seguridad

Azure Stack recopila eventos de seguridad y del sistema operativo para roles de infraestructura y nodos de unidades de escalado cada dos minutos. Los registros se almacenan en los contenedores de blobs de la cuenta de almacenamiento.

Hay una cuenta de almacenamiento por cada rol de infraestructura y una cuenta de almacenamiento general para todos los eventos habituales del sistema operativo.

Se puede llamar al proveedor de recursos de mantenimiento a través del protocolo de REST para recuperar la dirección URL en el contenedor de blobs. Las soluciones de seguridad de terceros pueden usar la API y las cuentas de almacenamiento para recuperar eventos para su procesamiento.

### <a name="use-azure-storage-explorer-to-view-events"></a>Uso del Explorador de Azure Storage para ver los eventos

Puede recuperar los eventos recopilados por Azure Stack mediante una herramienta denominada Explorador de Azure Storage. Puede descargar el Explorador de Azure Storage de [http://storageexplorer.com](http://storageexplorer.com).

El siguiente procedimiento es un ejemplo que puede usar para configurar el Explorador de Azure Storage para Azure Stack:

1. Inicie sesión en el portal de administración de Azure Stack como operador.
2. Examine las **cuentas de almacenamiento** y busque **frphealthaccount**. La cuenta **frphealthaccount** es la cuenta de almacenamiento general que se utiliza para almacenar todos los eventos del sistema operativo.

   ![Cuentas de almacenamiento](media/azure-stack-integrate-security/storage-accounts.png)

3. Seleccione **frphealthaccount** y, a continuación, haga clic en **Claves de acceso**.

   ![Claves de acceso](media/azure-stack-integrate-security/access-keys.png)

4. Copie la clave de acceso en el Portapapeles.
5. Abra el Explorador de Azure Storage.
6. En el menú **Editar**, seleccione **Target Azure Stack** (Azure Stack de destino).
7. Seleccione **Agregar cuenta**y, a continuación, seleccione **Use a storage account name and key** (Usar un nombre y una clave de la cuenta de almacenamiento).

   ![Conexión con el almacenamiento](media/azure-stack-integrate-security/connect-storage.png)

8. Haga clic en **Siguiente**.
9. En la página **Asociar almacenamiento externo**:

   a. Escriba el nombre de la cuenta **frphealthaccount**.

   b. Pegue la clave de acceso de la cuenta de almacenamiento.

   c. En **Dominio de puntos de conexión de Storage**, seleccione **Otro**y especifique el punto de conexión de almacenamiento **[Región].[NombreDeDominio]**.

   d. Seleccione la casilla **Usar HTTP**.

   ![Asociar almacenamiento externo](media/azure-stack-integrate-security/attach-storage.png)

10. Haga clic en **Siguiente**, revise el resumen y seleccione **Finalizar** en el asistente.
11. Ahora puede examinar los contenedores de blobs individuales y descargar los eventos.

   ![Examinar blobs](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>Uso de lenguajes de programación para acceder a los eventos

Puede usar varios lenguajes de programación para acceder a una cuenta de almacenamiento. Para elegir un ejemplo que coincida con el lenguaje, utilice la siguiente documentación:

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Auditoría de acceso de dispositivos

Todos los dispositivos físicos de Azure Stack admiten el uso de TACACS o RADIUS. Esto incluye el acceso al controlador de administración de placa base (BMC) y a los conmutadores de red.

Las soluciones de Azure Stack no se suministran con RADIUS o TACACS integrado. Sin embargo, las soluciones se han validado para que admitan el uso de las soluciones de RADIUS o TACACS ya existentes disponibles en el mercado.

Solo para RADIUS, se validó MSCHAPv2. Esta representa la implementación más segura mediante RADIUS.
Consulte con su proveedor de hardware de OEM para poder habilitar TACACS o RADIUS en los dispositivos incluidos en su solución de Azure Stack.

## <a name="syslog"></a>syslog

Todos los dispositivos físicos de Azure Stack pueden enviar mensajes de Syslog. Las soluciones de Azure Stack se suministran sin un servidor de Syslog. No obstante, se han validado las soluciones para que admitan el envío de mensajes a las soluciones de Syslog existentes que hay disponibles en el mercado.

La dirección de destino de Syslog es un parámetro opcional que se recopila para la implementación, pero se puede agregar también después de esta.

## <a name="next-steps"></a>Pasos siguientes

[Integración del centro de datos de Azure Stack: publicar puntos de conexión](azure-stack-integrate-endpoints.md)
