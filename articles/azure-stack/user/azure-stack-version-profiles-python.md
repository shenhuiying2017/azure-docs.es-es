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
ms.date: 04/30/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: a4fe62ba8c0732745326831b977e8975e1210436
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2018
ms.locfileid: "32310301"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Usar perfiles de la versión de la API con Python en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

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

5.  Cree una [entidad de servicio](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals) para trabajar con Azure Stack. Asegúrese de que la entidad de servicio tenga [rol de colaborador o propietario](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) en la suscripción.

6.  Establezca las siguientes variables y exporte estas variables de entorno al shell actual. 

`Note: provide an explanation of where these variables come from?`

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  Tenga en cuenta que para ejecutar este ejemplo, las imágenes Ubuntu 16.04-LTS y WindowsServer 2012 R2-Datacenter deben estar presentes en Marketplace de Azure Stack. Se pueden [descargar de Azure](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-download-azure-marketplace-item) o [agregarse a Platform Image Repository](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-add-vm-image).


8. Ejecute el ejemplo.

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>Notas

Puede tener la tentación de intentar recuperar el disco del sistema operativo de una máquina virtual mediante `virtual_machine.storage_profile.os_disk`.
Eso en algunos casos podría servirle para lo que quiere, pero tenga en cuenta que le proporciona un objeto `OSDisk`.
Para actualizar el tamaño del disco del sistema operativo, como hace `example.py`, no necesita un objeto `OSDisk`, sino un objeto `Disk`.
`example.py` obtiene el objeto `Disk` con lo siguiente:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>Pasos siguientes

- [Centro para desarrolladores de Python de Azure](https://azure.microsoft.com/develop/python/)
- [Documentación de Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
- [Ruta de aprendizaje de Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- Si no tiene una suscripción a Microsoft Azure, puede obtener una cuenta de evaluación GRATUITA [aquí](http://go.microsoft.com/fwlink/?LinkId=330212).
