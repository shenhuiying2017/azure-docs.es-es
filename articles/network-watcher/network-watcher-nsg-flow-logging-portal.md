---
title: 'Registrar el flujo del tráfico de red desde y hacia una VM (tutorial): Azure Portal | Microsoft Docs'
description: Obtenga información acerca de cómo registrar el flujo de tráfico de red desde y hacia una VM mediante la funcionalidad de registro de flujos de NSG de Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to log the network traffic to and from a VM so I can analyze it for anomalies.
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: f010bebcf1130b3061c60987ffbd4e706a030773
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776557"
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>Tutorial: registrar el tráfico de red hacia y desde una máquina virtual mediante Azure Portal

Un grupo de seguridad de red (NSG) le permite filtrar el tráfico entrante y el tráfico saliente en una máquina virtual (VM). Puede registrar el tráfico de red que fluye a través de un NSG gracias a la capacidad de registro de flujos de NSG de Network Watcher. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un grupo de seguridad de red con una máquina virtual.
> * Habilitar Network Watcher y registrar un proveedor de Microsoft.Insights.
> * Habilitar un registro de flujo de tráfico para un NSG, mediante la capacidad de registro de flujos de NSG de Network Watcher.
> * Descargar los datos registrados.
> * Ver los datos registrados.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="create-a-vm"></a>Crear una VM

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Compute** y, a continuación, seleccione **Windows Server 2016 Datacenter** o **Ubuntu Server 17.10 VM**.
3. Escriba o seleccione la siguiente información, acepte los valores predeterminados para el resto de la configuración y luego seleccione **Aceptar**:

    |Configuración|Valor|
    |---|---|
    |NOMBRE|myVm|
    |Nombre de usuario| Escriba un nombre de usuario de su elección.|
    |Password| Escriba una contraseña de su elección. La contraseña debe tener al menos 12 caracteres de largo y cumplir con los [requisitos de complejidad definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |La suscripción| Seleccione su suscripción.|
    |Grupos de recursos| Haga clic en **Crear nuevo** y escriba **myResourceGroup**.|
    |Ubicación| Seleccione **Este de EE. UU**.|

4. Seleccione un tamaño para la máquina virtual y luego **Seleccionar**.
5. En **Configuración**, acepte todos los valores predeterminados y seleccione **Aceptar**.
6. En **Crear** de la página **Resumen**, seleccione **Crear** para iniciar la implementación de la máquina virtual. La maquina virtual tarda unos minutos en implementarse. Espere a que la VM finalice la implementación antes de continuar con los pasos restantes.

La máquina virtual tarda en crearse unos minutos. No continúe con los pasos restantes hasta que la VM haya finalizado el proceso de creación. Además de crear la máquina virtual, el portal también crea un grupo de seguridad de red denominado **myVm nsg** y lo asocia a la interfaz de red de la máquina virtual.

## <a name="enable-network-watcher"></a>Habilitación de Network Watcher

Si ya dispone de un monitor de red habilitado en la región este de EE. UU., vaya al [Registro del proveedor de Insights](#register-insights-provider).

1. En el portal, seleccione **Todos los servicios**. En el **cuadro Filtrar**, escriba *Network Watcher*. Cuando aparezca la opción **Network Watcher** en los resultados, selecciónela.
2. Seleccione **Regiones** para expandirla y, a continuación, seleccione **...**  a la derecha de la opción **Este de EE. UU.**, tal y como se muestra en la siguiente imagen:

    ![Habilitación de Network Watcher](./media/network-watcher-nsg-flow-logging-portal/enable-network-watcher.png)

3. Seleccione **Habilitar Network Watcher**.

## <a name="register-insights-provider"></a>Registro del proveedor de Insights

Para iniciar sesión en el flujo de NSG, es necesario recurrir al proveedor **Microsoft.Insights**. Para registrar el proveedor, realice los pasos siguientes:

1. En la esquina superior izquierda del portal, seleccione **Todos los servicios**. En el cuadro Filtro, escriba *Suscripciones*. Cuando aparezca la opción **Suscripciones** en los resultados de la búsqueda, selecciónela.
2. En la lista de suscripciones, seleccione la suscripción con la que quiera habilitar el proveedor.
3. Seleccione **Proveedores de recursos** en **CONFIGURACIÓN**.
4. Confirme que el **ESTADO** del proveedor **microsoft.insights** está **registrado**, tal y como se muestra en la figura siguiente. Si el estado está **sin registrar**, seleccione **Registrar** a la derecha del proveedor.

    ![Registrar el proveedor](./media/network-watcher-nsg-flow-logging-portal/register-provider.png)

## <a name="enable-nsg-flow-log"></a>Habilitar el registro de flujos de NSG

1. Los datos del registro de flujos de NSG se escriben en una cuenta de Azure Storage. Para crear una cuenta de Azure Storage, seleccione **+ Create a resource** (+ Crear un recurso) en la esquina superior izquierda del portal.
2. Seleccione **Storage** y, a continuación, seleccione **Storage account - blob, file, table, queue** (Cuenta de almacenamiento: blob, archivo, tabla, cola).
3. Escriba o seleccione la siguiente información, acepte los valores predeterminados restantes y haga clic en **Crear**:

    | Configuración        | Valor                                                        |
    | ---            | ---   |
    | NOMBRE           | Debe tener entre 3 y 24 caracteres de longitud y solo puede contener letras minúsculas y números; asimismo, debe ser único para todas las cuentas de Azure Storage.                                                               |
    | Ubicación       | Seleccione **Este de EE. UU**.                                           |
    | Grupos de recursos | Seleccione **Usar existente** y, luego, seleccione **myResourceGroup** |

    La cuenta de almacenamiento tardará unos minutos en crearse. No continúe con los pasos restantes hasta que haya creado la cuenta de almacenamiento. Si va a usar una cuenta de almacenamiento existente en vez de crear una, asegúrese de seleccionar la cuenta de almacenamiento que tenga la opción **Todas las redes** (valor predeterminado) seleccionada en **Firewalls y redes virtuales**, en la **CONFIGURACIÓN** de la cuenta de almacenamiento.
4. En la esquina superior izquierda del portal, seleccione **Todos los servicios**. En el cuadro **Filtrar**, escriba *Network Watcher*. Cuando aparezca la opción **Network Watcher** en los resultados de búsqueda, selecciónela.
5. En **REGISTROS**, seleccione **Registro de flujos de NSG**, tal y como se muestra en la siguiente imagen:

    ![Grupos de seguridad de red](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. En la lista de NSG, seleccione el NSG denominado **myVm-nsg**.
7. En **Configuración de los registros de flujo**, seleccione **Activada**.
8. Seleccione la cuenta de almacenamiento que creó anteriormente en el paso 3:
9. Establezca el valor de **Retención (días)** en 5 y, a continuación, seleccione **Guardar**.

## <a name="download-flow-log"></a>Descargar el registro de flujos

1. En el portal de Network Watcher, seleccione **Registro de flujos de NSG** en **REGISTROS**.
2. Seleccione **You can download flow logs from configured storage accounts** (Puede descargar los registros de flujo desde cuentas de almacenamiento configuradas), tal como se muestra en la siguiente imagen.

  ![Descarga de registros de flujo](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. Seleccione la cuenta de almacenamiento que configuró en el paso 2 de la opción [Habilitar los registros de flujo de NSG](#enable-nsg-flow-log).
4. Seleccione **Contenedores** en **SERVICIO DE BLOB** y, a continuación, seleccione el contenedor **insights-logs-networksecuritygroupflowevent**, tal como se muestra en la siguiente imagen:

    ![Seleccionar el contenedor](./media/network-watcher-nsg-flow-logging-portal/select-container.png)
5. Explore la jerarquía de carpetas hasta llegar a un archivo PT1H.json, tal como se muestra en la siguiente imagen:

    ![Archivo de registro](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

    Los archivos de registro se escriben en una jerarquía de carpetas que tiene la siguiente convención de nomenclatura: https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

6. Seleccione **...** a la derecha del archivo PT1H.json de y **Descargar**.

## <a name="view-flow-log"></a>Ver el registro de flujos

El siguiente formato JSON es un ejemplo de lo que verá en el archivo PT1H.json para cada flujo en el que se registran los datos:

```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [{
            "rule": "UserRule_default-allow-rdp",
            "flows": [{
                "mac": "000D3A170C69",
                "flowTuples": ["1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"]
            }]
        }]
    }
}
```

El valor de **mac** en la salida anterior es la dirección MAC de la interfaz de red que se creó cuando creó la máquina virtual. La información separada por comas de **flowTuples** tiene el siguiente aspecto:

| Datos de ejemplo | Qué representan los datos   | Explicación                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1525186745   | Marca de tiempo             | La marca de tiempo de cuando se produjo el flujo en formato UNIX EPOCH. En el ejemplo anterior, la fecha se convierte en el 1 de mayo de 2018 a las 2:59:05 P.M. GMT.                                                                                    |
| 192.168.1.4  | Dirección IP de origen      | La dirección IP de origen en la que se ha originado el flujo.
| 10.0.0.4     | Dirección IP de destino | La dirección IP de destino a la que se destina el flujo. 10.0.0.4 es la dirección IP privada de la VM que creó en el paso [Crear una máquina virtual](#create-a-vm).                                                                                 |
| 55960        | Puerto de origen            | El puerto de origen en el que se ha originado el flujo.                                           |
| 3389         | Puerto de destino       | El puerto de destino al que se destina el flujo. Puesto que el tráfico se destinó al puerto 3389, la regla denominada **UserRule_default-allow-rdp**, en el archivo de registro, es la que procesa el flujo.                                                |
| T            | Protocolo               | Si el protocolo del flujo es TCP (T) o UDP (U).                                  |
| I            | Dirección              | Si el tráfico es entrante (I) o saliente (O).                                     |
| Una             | .                 | Si el tráfico está permitido (A) o si está denegado (D).                                           |

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a habilitar el registro de flujos de NSG para un grupo de seguridad de red o NSG. Asimismo, también aprendió a descargar y ver los datos registrados en un archivo. Los datos sin procesar del archivo json pueden ser difíciles de interpretar. Para visualizar los datos, puede usar el [análisis del tráfico](traffic-analytics.md) de Network Watcher, Microsoft [PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md) y otras herramientas.