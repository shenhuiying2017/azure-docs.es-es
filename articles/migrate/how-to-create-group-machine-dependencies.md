---
title: "Agrupación de máquinas con dependencias de máquina con Azure Migrate | Microsoft Docs"
description: "En este artículo se describe cómo crear una evaluación con dependencias de máquina mediante el servicio Azure Migrate."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 5ed49f3dc71af6a8c1c7b6c9e38fd84452505aec
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Agrupación de máquinas con asignación de dependencias de máquina

En este artículo se describe cómo crear un grupo de máquinas para la evaluación de [Azure Migrate](migrate-overview.md) mediante la asignación de dependencias de máquina. Este método se utiliza normalmente cuando se quiere evaluar grupos de máquinas virtuales con mayores niveles de confianza mediante la comprobación cruzada de dependencias de máquina antes de ejecutar una evaluación.



## <a name="prepare-machines-for-dependency-mapping"></a>Preparación de las máquinas para la asignación de dependencias
Para incluir máquinas en la asignación de dependencias, debe descargar e instalar agentes en cada máquina local que vaya a evaluar. Además, si tiene máquinas sin conectividad a Internet, debe descargar e instalar en ellas la [puerta de enlace de OMS](../log-analytics/log-analytics-oms-gateway.md).

### <a name="download-and-install-the-vm-agents"></a>Descarga e instalación de los agentes en máquinas virtuales
1. En **Introducción**, haga clic en **Administrar** > **Máquinas**y seleccione la máquina requerida.
2. En la columna **Dependencias**, haga clic en **Instalar agentes**. 
3. En la página **Dependencias**, descargue e instale el agente de Microsoft Monitoring Agent (MMA), así como el agente de dependencia en cada máquina virtual que vaya a evaluar.
4. Copie la clave y el identificador de área de trabajo. Las necesitará cuando se instala MMA en la máquina local.

### <a name="install-the-mma"></a>Instalación de MMA

Para instalar al agente en una máquina Windows, siga estos pasos:

1. Haga doble clic en el agente descargado.
2. En la página **principal**, haga clic en **Siguiente**. En la página **Términos de licencia**, haga clic en **Acepto** para aceptar la licencia.
3. En **Carpeta de destino**, mantenga o modifique la carpeta de instalación predeterminada y después haga clic en **Siguiente**. 
4. En **Opciones de instalación del agente**, seleccione **Azure Log Analytics (OMS)** > **Siguiente**. 
5. Haga clic en **Agregar** para agregar una nueva área de trabajo de OMS. Pegue la clave y el identificador de área de trabajo que ha copiado desde el portal. Haga clic en **Siguiente**.


Para instalar al agente en una máquina Linux, siga estos pasos:

1. Transfiera el paquete adecuado (x86 o x x64) al equipo Linux mediante scp o sftp.
2. Instale el paquete mediante el argumento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>Instalación del agente de dependencia
1. Para instalar al agente de dependencia en una máquina Windows, haga doble clic en el archivo de instalación y siga los pasos del asistente.
2. Para instalar el agente de dependencia en una máquina Linux, instale como raíz mediante el siguiente comando:

    ```sh InstallDependencyAgent-Linux64.bin```

[Obtenga más información](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) sobre los sistemas operativos compatibles con el agente de dependencia. 

## <a name="create-a-group"></a>Creación de un grupo

1. Después de instalar los agentes, vaya al portal y haga clic en **Administrar** > **Máquinas**.
2. La columna **Dependencias** debería aparecer ahora como **Ver dependencias**. Haga clic en la columna para ver las dependencias.
3. En cada máquina, puede comprobar lo siguiente:
    - Si MMA y el agente de dependencia están instalados, y si se ha detectado la máquina.
    - El sistema operativo invitado que se ejecuta en la máquina.
    - Puertos y conexiones IP entrantes y salientes.
    - Procesos que se ejecutan en máquinas.
    - Dependencias entre máquinas.

4. Para ver dependencias más granulares, haga clic en el intervalo de tiempo para modificarlo. De forma predeterminada, el intervalo es una hora. Puede modificar el intervalo de tiempo o especificar las fechas de inicio y finalización, y la duración.
5. Después de identificar máquinas dependientes que desea agrupar, seleccione las máquinas en el mapa y haga clic en **Agrupar máquinas**.
6. Especifique un nombre para el grupo. Compruebe que Azure Migrate detecta la máquina. Si no se vuelve a ejecutar el proceso de detección local, puede ejecutar una evaluación inmediatamente si desea.
7. Haga clic en **Aceptar** para guardar el grupo.

    ![Creación de un grupo con dependencias de máquina](./media/how-to-create-group-machine-dependencies/create-group.png)

## <a name="next-steps"></a>Pasos siguientes

- [Obtenga información sobre cómo](how-to-create-group-dependencies.md) refinar el grupo mediante la comprobación de las dependencias del grupo.
- [Aprenda más](concepts-assessment-calculation.md) sobre cómo se calculan las evaluaciones.
