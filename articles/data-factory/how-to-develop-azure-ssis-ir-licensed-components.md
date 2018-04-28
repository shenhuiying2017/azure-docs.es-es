---
title: Desarrollo de componentes de pago o con licencia para la instancia de Integration Runtime para la integración de SSIS en Azure | Microsoft Docs
description: En este artículo se describe cómo ISV desarrolla e instala componentes personalizados de pago o con licencia para la instancia de Integration Runtime para la integración de SSIS en Azure
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: e22ca4bd5b749e8752f800590938199e06abbd34
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="develop-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Desarrollo de componentes personalizados de pago o con licencia para la instancia de Integration Runtime para la integración de SSIS en Azure

## <a name="problem---the-azure-ssis-ir-requires-a-different-approach"></a>Problema: la instancia de Integration Runtime para la integración de SSIS en Azure requiere un enfoque diferente

La naturaleza de la instancia de Integration Runtime para la integración de SSIS en Azure presenta varios retos, que hacen que los métodos de licencia típicos que se usan para la instalación local de componentes personalizados sea insuficiente.

-   Los nodos de la instancia de Integration Runtime para la integración de SSIS en Azure son volátiles y se pueden asignar o liberar en cualquier momento. Por ejemplo, puede iniciar o detener nodos para administrar los costos o escalar vertical y horizontalmente a través de varios tamaños de nodo. Como resultado, ya no es viable enlazar una licencia de componentes de terceros a un nodo concreto mediante la información específica de la máquina, como dirección MAC o el identificador de CPU.

-   También puede escalar la instancia de Integration Runtime para la integración de SSIS en Azure vertical u horizontalmente para que el número de nodos se reduzca o aumente en cualquier momento.

## <a name="solution---windows-environment-variables-and-ssis-system-variables-for-license-binding-and-validation"></a>Solución: las variables de entorno de Windows y las variables del sistema SSIS para el enlace de licencias y la validación

Como resultado de las limitaciones de los métodos tradicionales de licencia que se describen en la sección anterior, la instancia de Integration Runtime para la integración de SSIS en Azure proporciona variables de entorno de Windows y variables del sistema SSIS para el enlace de la licencia y la validación de componentes de terceros. Los ISV pueden usar estas variables para obtener información duradera y única de la instancia de Integration Runtime para la integración de SSIS en Azure, como el identificador del clúster y el número de nodos del clúster. Con esta información, los ISV pueden enlazar la licencia de su componente a una instancia de Integration Runtime para la integración de SSIS en Azure *como clúster*, con un identificador que no cambia cuando se inician o detienen clientes, al escalar vertical y horizontalmente, al reducir y aumentar horizontalmente, ni al volver a configurar la instancia de Integration Runtime para la integración de SSIS en Azure de cualquier manera.

En el siguiente diagrama se muestra la instalación típica, la activación y el enlace de licencia, así como los flujos de validación de los componentes de terceros que utilizan estas variables nuevas:

![Instalación de componentes con licencia](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instrucciones
1. Los ISV pueden ofrecer sus componentes con licencia en varios SKU o niveles (por ejemplo, nodo único, hasta 5 nodos, hasta 10 nodos, etc.). El ISV proporciona la clave de producto correspondiente al cliente durante la compra. El ISV puede proporcionar también un contenedor de blobs de Azure Storage con un script de instalación de ISV y los archivos asociados. Los clientes puedan copiar estos archivos en su propio contenedor de almacenamiento y modificarlos con su propia clave de producto (por ejemplo, al ejecutar `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Los clientes pueden aprovisionar o volver a configurar la instancia de Integration Runtime para la integración de SSIS en Azure con el URI de SAS de su contenedor como parámetro. Para más información, consulte [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Instalación personalizada de la instancia de Integration Runtime para la integración de SSIS en Azure).

2. Cuando la instancia de Integration Runtime para la integración de SSIS en Azure se aprovisiona o se vuelve a configurar, el programa de instalación de ISV se ejecuta en todos los nodos para consultar las variables de entorno de Windows, `SSIS_CLUSTERID` y `SSIS_CLUSTERNODECOUNT`. A continuación, la instancia de Integration Runtime para la integración de SSIS en AzureIS envía el identificador del clúster y la clave de producto del producto con licencia para que el servidor de activación de ISV genere una clave de activación.

3. Después de recibir la clave de activación, el programa de instalación de ISV puede almacenar la clave localmente en cada nodo (por ejemplo, en el registro).

4. Cuando los clientes ejecutan un paquete que usa componentes con licencia de ISV en un nodo dela instancia de Integration Runtime para la integración de SSIS en Azure, el paquete lee la clave de activación almacenada localmente y la compara con el identificador de clúster del nodo. El paquete también puede notificar el número de nodos de clúster para el servidor de activación de ISV (opcional).

    Este es un ejemplo de código que valida la clave de activación y notifica el número de nodos de clúster:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="next-steps"></a>Pasos siguientes

-   [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Instalación personalizada de la instancia de Integration Runtime para la integración de SSIS en Azure)

-   [Enterprise Edition of the Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md) (Edición Enterprise para la instancia de Integration Runtime para la integración de SSIS en Azure)