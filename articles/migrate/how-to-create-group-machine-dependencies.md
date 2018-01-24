---
title: "Agrupación de máquinas con dependencias de máquina con Azure Migrate | Microsoft Docs"
description: "En este artículo se describe cómo crear una evaluación con dependencias de máquina mediante el servicio Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/25/2017
ms.author: raynew
ms.openlocfilehash: 720380fd14d9eaf4856ad75269a80f2b63a4725f
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Agrupación de máquinas con asignación de dependencias de máquina

En este artículo se describe cómo crear un grupo de máquinas para la evaluación de [Azure Migrate](migrate-overview.md) mediante la visualización de dependencias de máquina. Este método se utiliza normalmente cuando se quiere evaluar grupos de máquinas virtuales con mayores niveles de confianza mediante la comprobación cruzada de dependencias de máquina antes de ejecutar una evaluación. La visualización de dependencia puede ayudarle a planear la migración a Azure de forma eficaz. Le ayuda a asegurarse de que no se deja nada atrás y de que no se produzcan interrupciones inesperadas al realizar una migración a Azure. Puede detectar todos los sistemas interdependientes que tienen que migrarse juntos e identificar si un sistema en ejecución sigue ofreciendo servicio a los usuarios o si es un candidato para la retirada en lugar de la migración. 


## <a name="prepare-machines-for-dependency-mapping"></a>Preparación de las máquinas para la asignación de dependencias
Para ver dependencias de máquinas, debe descargar e instalar agentes en cada máquina local que vaya a evaluar. Además, si tiene máquinas sin conectividad a Internet, debe descargar e instalar en ellas la [puerta de enlace de OMS](../log-analytics/log-analytics-oms-gateway.md).

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
5. Haga clic en **Agregar** para agregar una nueva área de trabajo de OMS. Pegue la clave y el identificador de área de trabajo que ha copiado desde el portal. Haga clic en **Next**.


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
2. Busque la máquina en que instaló los agentes.
3. La columna **Dependencias** de la máquina debería aparecer ahora como **Ver dependencias**. Haga clic en la columna para ver las dependencias de la máquina.
4. En el mapa de dependencias de la máquina aparecen los siguientes detalles:
    - Conexiones TCP de entrada (clientes) y de salida (servidores) hacia y desde la máquina
        - Las máquinas dependientes que no tienen instalado el agente MMA y de dependencias se agrupan en función de los números de puerto.
        - Las máquinas dependientes que tienen instalado el agente MMA y de dependencias se muestran como casillas independientes. 
    - Procesos que se ejecutan dentro de la máquina; aquí puede expandir cada casilla de la máquina para ver los procesos
    - Propiedades, como el nombre de dominio completo, el sistema operativo, la dirección MAC, entre otras, de cada máquina; puede hacer clic en la casilla de cada máquina para ver estos detalles

 ![Ver las dependencias de máquinas](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Puede consultar las dependencias de distintas duraciones; para ello, haga clic en la duración correspondiente en la etiqueta de intervalo de tiempo. De forma predeterminada, el intervalo es una hora. Puede modificar el intervalo de tiempo o especificar las fechas de inicio y finalización, y la duración.
5. Después de identificar máquinas dependientes que desea agrupar, use Ctrl+clic para seleccionar varias máquinas en el mapa y haga clic en **Agrupar máquinas**.
6. Especifique un nombre para el grupo. Verifique que Azure Migrate puede detectar las máquinas dependientes. 

    > [!NOTE]
    > Si Azure Migrate no detecta una máquina dependiente, puede agregarla al grupo. Para agregar estas máquinas al grupo, debe volver a ejecutar el proceso de detección con el ámbito correcto en la instancia de vCenter Server y asegurarse de que Azure Migrate detecta la máquina.  

7. Si desea crear una evaluación para este grupo, seleccione la casilla para crear una evaluación para el grupo.
8. Haga clic en **Aceptar** para guardar el grupo.

Una vez creado el grupo, se recomienda instalar los agentes en todas las máquinas del grupo y restringir el grupo mediante la visualización de la dependencia de todo el grupo.

## <a name="next-steps"></a>pasos siguientes

- [Obtenga información sobre cómo](how-to-create-group-dependencies.md) restringir el grupo mediante la visualización de las dependencias del grupo.
- [Obtenga más información](concepts-assessment-calculation.md) sobre cómo se calculan las evaluaciones.
