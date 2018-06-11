---
title: Uso de perfiles de la versión de la API con Python en Azure Stack | Microsoft Docs
description: Obtenga información sobre el empleo de perfiles de la versión de la API con Python en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: d17ba9ed4548a986d6846d934aee197609ec80ca
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2018
ms.locfileid: "34806843"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Usar perfiles de la versión de la API con Python en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

## <a name="python-and-api-version-profiles"></a>Python y Perfiles de la versión de la API

El SDK de Python es compatible con los perfiles de la versión de la API para su uso con diferentes plataformas en la nube, como Azure Stack y Azure global. Puede utilizar los perfiles de la API para crear soluciones para una nube híbrida. El SDK de Python es compatible con los siguientes perfiles de la API:

1. **más reciente**  
    Este perfil tiene como destino las versiones más recientes de la API para todos los proveedores de servicios en la plataforma de Azure.
2.  **2017-03-09-profile**  
    **2017-03-09-profile**  
    Este perfil tiene como destino las versiones de la API de los proveedores de recursos admitidos por Azure Stack.

    Para más información sobre los perfiles de API y Azure Stack, consulte [Administración de perfiles de versión de API en Azure Stack](azure-stack-version-profiles.md).

## <a name="install-azure-python-sdk"></a>Instalación del SDK de Python de Azure

1.  Instale Git desde [el sitio oficial](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2.  Para obtener las instrucciones de instalación del SDK de Python, consulte [Azure para desarrolladores de Python](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python).
3.  Si no está disponible, cree una suscripción y guarde su identificador para usarlo más adelante. Para obtener las instrucciones para crear una suscripción, consulte [Creación de suscripciones para ofertas en Azure Stack](../azure-stack-subscribe-plan-provision-vm.md). 
4.  Cree una entidad de servicio y guarde su identificador y su secreto. Para obtener las instrucciones para crear una entidad de servicio para Azure Stack, consulte [Proporcionar a las aplicaciones acceso a Azure Stack](../azure-stack-create-service-principals.md). 
5.  Asegúrese de que la entidad de servicio tenga rol de colaborador o propietario en la suscripción. Para obtener instrucciones sobre cómo asignar roles a la entidad de servicio, consulte [Proporcionar a las aplicaciones acceso a Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>requisitos previos

Para usar el SDK para Python de Azure con Azure Stack, debe proporcionar los siguientes valores y, a continuación, establecer valores con variables de entorno. Consulte las instrucciones bajo la tabla de su sistema operativo sobre cómo configurar las variables de entorno. 

| Valor | Variables de entorno | DESCRIPCIÓN |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Id. de inquilino | AZURE_TENANT_ID | El valor de su [identificador de inquilino](../azure-stack-identity-overview.md) de Azure Stack. |
| Id. de cliente | AZURE_CLIENT_ID | El identificador de aplicación de la entidad de servicio que guardó al crear esta última en la sección anterior de este documento. |
| Id. de suscripción | AZURE_SUBSCRIPTION_ID | El [identificador de suscripción](../azure-stack-plan-offer-quota-overview.md#subscriptions) es su forma de tener acceso a las ofertas de Azure Stack. |
| Secreto del cliente | AZURE_CLIENT_SECRET | El secreto de aplicación de la entidad de servicio que guardó al crear esta última. |
| Punto de conexión de Resource Manager | ARM_ENDPOINT | Consulte [Punto de conexión de administración de recursos de Azure Stack](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |


## <a name="python-samples-for-azure-stack"></a>Ejemplos de Python para Azure Stack 

Puede usar los siguientes ejemplos de código para realizar tareas de administración comunes de máquinas virtuales en Azure Stack.

Los ejemplos de código enseñan:

- Creación de máquinas virtuales:
    - Creación de una máquina virtual con Linux
    - Creación de una máquina virtual Windows
- Actualización de una máquina virtual:
    - Expansión de una unidad
    - Etiquetado de una máquina virtual
    - Conexión de discos de datos
    - Desacoplamiento de discos de datos
- Manejo de una máquina virtual:
    - Inicio de una máquina virtual
    - Detención de una máquina virtual
    - Reinicio de una máquina virtual
- Enumeración de máquinas virtuales
- Eliminación de una máquina virtual

Para revisar el código necesario para llevar a cabo estas operaciones, vea la función **run_example()** del script de Python **Hybrid/unmanaged-disks/example.py** en el repositorio de GitHub [virtual-machines-python-manage](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Cada operación está claramente etiquetada con un comentario y una función de impresión.
Los ejemplos no aparecen necesariamente en el mismo orden de la lista anterior.


## <a name="run-the-python-sample"></a>Ejecutar el ejemplo de Python

1.  Si aún no lo tiene, instale [Python](https://www.python.org/downloads/).

    Este ejemplo (y el SDK) son compatibles con Python 2.7, 3.4, 3.5 y 3.6.

2.  La recomendación general para el desarrollo de Python es usar un entorno virtual. 
    Para obtener más información, vea https://docs.python.org/3/tutorial/venv.html.
    
    Instale e inicie el entorno virtual con el módulo "venv" en Python 3 (debe instalar [virtualenv](https://pypi.python.org/pypi/virtualenv) para Python 2.7):

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  Clone el repositorio.

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  Instale las dependencias con pip.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Cree una [entidad de servicio](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) para trabajar con Azure Stack. Asegúrese de que la entidad de servicio tenga [rol de colaborador o propietario](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) en la suscripción.

6.  Establezca las siguientes variables y exporte estas variables de entorno al shell actual. 

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  In order to run this sample, Ubuntu 16.04-LTS and WindowsServer 2012-R2-Datacenter images must be present in Azure Stack market place. These can be either [downloaded from Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) or [added to Platform Image Repository](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image).

8. Run the sample.

    ```
    python unmanaged-disks\example.py
    ```

## Notes

You may be tempted to try to retrieve a VM's OS disk by using
`virtual_machine.storage_profile.os_disk`.
In some cases, this may do what you want,
but be aware that it gives you an `OSDisk` object.
In order to update the OS Disk's size, as `example.py` does,
you need not an `OSDisk` object but a `Disk` object.
`example.py` gets the `Disk` object with the following:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## Next steps

- [Azure Python Development Center](https://azure.microsoft.com/develop/python/)
- [Azure Virtual Machines documentation](https://azure.microsoft.com/services/virtual-machines/)
- [Learning Path for Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- If you don't have a Microsoft Azure subscription, you can get a FREE trial account [here](http://go.microsoft.com/fwlink/?LinkId=330212).