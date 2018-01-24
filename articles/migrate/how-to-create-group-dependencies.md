---
title: "Refinación de un grupo de evaluación con la asignación de dependencias de grupo en Azure Migrate | Microsoft Docs"
description: "Describe cómo refinar una evaluación mediante la asignación de dependencia de grupo en el servicio Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/22/2017
ms.author: raynew
ms.openlocfilehash: 3b10765894501791004e3a9221363f196cc0c91d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Refinación de un grupo con la asignación de dependencias de grupo

En este artículo se describe cómo restringir un grupo mediante la visualización de las dependencias de todas las máquinas del grupo. Este método se suele utilizar normalmente cuando desea restringir la pertenencia de un grupo existente mediante la comprobación cruzada de las dependencias de grupo antes de ejecutar una evaluación. La restricción de un grupo mediante la visualización de dependencias puede ayudar a planear con eficacia la migración a Azure. Puede detectar todos los sistemas interdependientes que necesitan migrarse juntos. Le ayuda a asegurarse de que no se deja nada atrás y de que no se produzcan interrupciones inesperadas al realizar una migración a Azure. 


> [!NOTE]
> Los grupos de los que desea visualizar las dependencias no deben contener más de diez máquinas. Si tiene más de diez máquinas en el grupo, se recomienda dividirlo en grupos más pequeños para aprovechar la funcionalidad de la visualización de dependencias.


# <a name="prepare-the-group-for-dependency-visualization"></a>Preparación del grupo para la visualización de dependencias
Para ver las dependencias de un grupo, debe descargar e instalar agentes en cada máquina local que forma parte del grupo. Además, si tiene máquinas sin conectividad a Internet, debe descargar e instalar en ellas la [puerta de enlace de OMS](../log-analytics/log-analytics-oms-gateway.md).

### <a name="download-and-install-the-vm-agents"></a>Descarga e instalación de los agentes en máquinas virtuales
1. En **Introducción**, haga clic en **Administrar** > **Grupos** y vaya al grupo requerido.
2. En la lista de máquinas, en la columna **Agente de dependencias**, haga clic en **Requiere instalación** para ver las instrucciones para descargar e instalar los agentes.
3. En la página **Dependencias**, descargue e instale el agente de Microsoft Monitoring Agent (MMA), así como el agente de dependencias en cada máquina virtual que forma parte del grupo.
4. Copie la clave y el identificador de área de trabajo. Los necesitará cuando se instala MMA en las máquinas locales.

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

## <a name="refine-the-group-based-on-dependency-visualization"></a>Restricción del grupo basada en la visualización de dependencias
Después de instalar agentes en todas las máquinas del grupo, puede visualizar las dependencias del grupo y restringirlo siguiendo los pasos indicados a continuación.

1. En el proyecto de Azure Migrate, en **Administrar**, haga clic en  **Grupos** y seleccione el grupo.
2. En la página de grupo, haga clic en  **Ver dependencias** para abrir la asignación de dependencias de grupo.
3. En el mapa de dependencias del grupo aparecen los siguientes detalles:
    - Conexiones TCP de entrada (clientes) y de salida (servidores) hacia y desde las máquinas que forman parte del grupo
        - Las máquinas dependientes que no tienen instalado el agente MMA y de dependencias se agrupan en función de los números de puerto.
        - Las máquinas dependientes que tienen instalado el agente MMA y de dependencias se muestran como casillas independientes. 
    - Procesos que se ejecutan dentro de la máquina; aquí puede expandir cada casilla de la máquina para ver los procesos
    - Propiedades, como el nombre de dominio completo, el sistema operativo, la dirección MAC, entre otras, de cada máquina; puede hacer clic en la casilla de cada máquina para ver estos detalles

     ![Visualización de las dependencias del grupo](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Para ver dependencias más granulares, haga clic en el intervalo de tiempo para modificarlo. De forma predeterminada, el intervalo es una hora. Puede modificar el intervalo de tiempo o especificar las fechas de inicio y finalización, y la duración.
4. Verifique las máquinas dependientes, el proceso que se ejecuta en cada máquina e identifique las máquinas que se deben agregar al grupo o eliminar de él.
5. Use CTRL + Clic para seleccionar máquinas en el mapa y agregarlas o quitarlas del grupo.
    - Solo puede agregar máquinas que se han detectado.
    - Al agregar y quitar máquinas en un grupo, se invalidan sus evaluaciones anteriores.
    - Si lo desea, puede crear una evaluación cuando se modifica el grupo.
5. Haga clic en **Aceptar** para guardar el grupo.

    ![Agregar o quitar máquinas](./media/how-to-create-group-dependencies/add-remove.png)

Si desea comprobar las dependencias de una máquina específica que aparece en el mapa de dependencias de grupo, [configure la asignación de dependencias de máquina](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>pasos siguientes

[Obtenga más información](concepts-assessment-calculation.md) sobre cómo se calculan las evaluaciones.
