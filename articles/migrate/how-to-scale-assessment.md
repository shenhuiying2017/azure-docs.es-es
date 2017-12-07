---
title: "Escalado de detección y evaluación con Azure Migrate | Microsoft Docs"
description: "Se describe cómo evaluar un número elevado de máquinas locales con el servicio Azure Migrate."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dde0d07f-94b7-4b6a-a158-a89aa9324a35
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 930ec182cf329e7dda072dc49bd7f70abb413f2d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Detección y evaluación de un entorno grande de VMware

En este artículo se describe cómo evaluar un número elevado de máquinas locales con [Azure Migrate](migrate-overview.md). Azure Migrate evalúa máquinas para comprobar si son adecuadas para su migración a Azure, y proporciona estimaciones de ajuste de tamaño y costo con relación a la ejecución de la máquina en Azure.

## <a name="prerequisites"></a>Requisitos previos

- **VMware**: necesita al menos una VM de VMware ubicada en un host ESXi o en un clúster que ejecute la versión 5.0 o posterior. El host o clúster debe administrarse mediante un servidor vCenter que ejecute la versión 5.5 o 6.0.
- **Cuenta de vCenter**: se necesita una cuenta de solo lectura con credenciales de administrador para el servidor vCenter. Azure Migrate usa esta cuenta para detectar VM.
- **Permisos**: en el servidor vCenter, necesitará permisos para crear una VM mediante la importación de un archivo en formato .OVA.
- **Configuración de estadísticas**: la configuración de las estadísticas del servidor vCenter se debe establecer en el nivel 2 o más, antes de empezar la implementación.

## <a name="plan-azure-migrate-projects"></a>Planeación de proyectos de Azure Migrate

Un proyecto de Azure Migrate puede evaluar hasta 1500 máquinas. Una sola detección en un proyecto puede detectar hasta 1000 máquinas.

- Si tiene menos de 1000 máquinas para detectar, necesita un solo proyecto con una sola detección.
- Si tiene entre 1000 y 1500 máquinas, necesita un solo proyecto con dos detecciones.
- Si tiene más de 1500 máquinas, debe crear varios proyectos y realizar varias detecciones, según sus requisitos. Por ejemplo:
    - Si tiene 3000 máquinas, podría configurar dos proyectos con dos detecciones o tres proyectos con una sola detección.
    - Si tiene 5000 máquinas, podría configurar cuatro proyectos. Dos con una detección de 1500 máquinas y uno con una detección de 500 máquinas. Como alternativa, podría configurar cinco proyectos con una sola detección en cada uno. 
- Al realizar una detección en Azure Migrate, puede definir el ámbito de detección a un clúster, un centro de datos o una carpeta de VMware.
- Para hacer más de una detección, verifique en vCenter si las máquinas virtuales que desea detectar están en carpetas, en centros de datos o en clústeres que admiten la limitación de 1000 máquinas.
- Se recomienda que, para los fines de evaluación, mantenga las máquinas con interdependencias dentro del mismo proyecto y de la misma evaluación. Por tanto, en vCenter, asegúrese de que las máquinas dependientes están en la misma carpeta, centro de datos o clúster para los fines de evaluación.


## <a name="create-a-project"></a>Creación de un proyecto

Cree un proyecto de Azure Migrate según sus necesidades.

1. En Azure Portal, haga clic en **Crear un recurso**.
2. Busque **Azure Migrate** y seleccione el servicio **Azure Migrate (versión preliminar)** en los resultados de la búsqueda. A continuación, haga clic en **Crear**.
3. Especifique un nombre de proyecto y la suscripción de Azure para el proyecto.
4. Cree un nuevo grupo de recursos.
5. Especifique la región en la que se va a crear el proyecto y haga clic en **Crear**. Los metadatos recopilados a partir de VM locales se almacenan en esta región.

## <a name="set-up-the-collector-appliance"></a>Configuración del dispositivo de recopilador

Azure Migrate crea una VM local conocida como el dispositivo de recopilador. Esta VM detecta VM de VMware locales y envía los metadatos sobre ellas al servicio Azure Migrate. Para configurar el dispositivo de recopilador, descargue un archivo .OVA e impórtelo en el servidor vCenter local para crear la VM.

### <a name="download-the-collector-appliance"></a>Descarga del dispositivo de recopilador

Si tiene varios proyectos, solo necesita descargar el dispositivo de recopilador una vez en el servidor vCenter. Después de descargar y configurar el dispositivo, ejecútelo en cada proyecto y especifique el identificador de proyecto exclusivo y la clave.

1. En el proyecto de Azure Migrate, haga clic en **Introducción** > **Detectar y evaluar** > **Detectar máquinas**.
2. En **Detectar máquinas**, haga clic en **Descargar**, para descargar el archivo .OVA.
3. En **Copiar las credenciales del proyecto**, copie la clave y el identificador del proyecto. Los necesitará cuando configure el recopilador.

   
### <a name="verify-the-collector-appliance"></a>Comprobación del dispositivo de recopilador

Compruebe que el archivo .OVA es seguro, antes de implementarlo.

1. En la máquina en la que descargó el archivo, abra una ventana de comandos de administrador.
2. Ejecute el siguiente comando para generar el código hash para el archivo OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. El código hash generado debe coincidir con esta configuración.

    **Algoritmo** | **Valor del código hash**
    --- | ---
    MD5 | c283f00f46484bf673dc8383b01d0741 
    SHA1 | 8f49b47f53d051af1780bbc725659ce64e717ed4
    SHA256 | 7aecdbdb2aea712efe99d0c1444503f52d16de5768e783465e226fbbca71501d

## <a name="create-the-collector-vm"></a>Creación de la VM de recopilador

Importe el archivo descargado en el servidor vCenter.

1. En la consola de cliente de vSphere, haga clic en **File** (Archivo) > **Deploy OVF Template** (Implementar plantilla de OVF).

    ![Implementación de OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. En el Deploy OVF Template Wizard (Asistente para implementar la plantilla de OVF) > **Source** (Origen), especifique la ubicación del archivo .ova.
3. En **Name** (Nombre) y **Location** (Ubicación), especifique un nombre descriptivo para la VM de recopilador y el objeto de inventario en el que se hospedará la VM.
5. En **Host/Cluster** (Host o clúster), especifique el host o clúster donde se ejecutará el recopilador de máquina virtual.
7. En el almacenamiento, especifique el destino de almacenamiento para la VM de recopilador.
8. En **Disk Format** (Formato de disco), especifique el tamaño y el tipo de disco.
9. En **Network Mapping** (Asignación de red), especifique la red a la que se conectará la VM de recopilador. La red necesita conectividad a Internet, para enviar metadatos a Azure. 
10. Revise y confirme la configuración y haga clic en **Finish** (Finalizar).

## <a name="identify-the-key-and-id-for-each-project"></a>Identificación de la clave y el identificador de cada proyecto

Si tiene varios proyectos, asegúrese de identificar el identificador y la clave de cada uno. Necesita la clave al ejecutar el recopilador para detectar las máquinas virtuales.

1. En el proyecto, haga clic en **Introducción** > **Detectar y evaluar** > **Detectar máquinas**.
2. En **Copiar las credenciales del proyecto**, copie la clave y el identificador del proyecto. 
    ![Id. de proyecto](./media/how-to-scale-assessment/project-id.png)

## <a name="vcenter-statistics-level-to-collect-the-performance-counters"></a>Nivel de estadísticas de vCenter para recopilar los contadores de rendimiento
A continuación, se muestra la lista de contadores recopilados durante la detección. Los contadores están disponibles de forma predeterminada en distintos niveles del servidor vCenter. Se recomienda establecer el nivel común más alto (nivel 3) para el nivel de estadísticas, para que todos los contadores se recopilen correctamente. Si tiene vCenter establecido en un nivel inferior, solo algunos contadores podrán recopilarse por completo, y los demás estarán establecidos en 0. Por tanto, puede que la evaluación muestre datos incompletos. En la tabla siguiente también se muestran los resultados de la evaluación que resultarán afectados si no se recopila un contador determinado.

|Contador                                  |Nivel    |Por nivel de dispositivo  |Impacto en la evaluación                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |N/D                |Costo y tamaño de VM recomendados                    |
|mem.usage.average                        | 1       |N/D                |Costo y tamaño de VM recomendados                    |
|virtualDisk.read.average                 | 2       |2                 |Tamaño del disco, costo de almacenamiento y tamaño de VM         |
|virtualDisk.write.average                | 2       |2                 |Tamaño del disco, costo de almacenamiento y tamaño de VM         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Tamaño del disco, costo de almacenamiento y tamaño de VM         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Tamaño del disco, costo de almacenamiento y tamaño de VM         |
|net.received.average                     | 2       |3                 |Tamaño de VM y costo de la red                        |
|net.transmitted.average                  | 2       |3                 |Tamaño de VM y costo de la red                        |

> [!WARNING]
> Si configuró un nivel de estadística más alto, los contadores de rendimiento pueden tardar hasta un día en generarse. Por lo tanto, se recomienda ejecutar la detección después de un día.

## <a name="run-the-collector-to-discover-vms"></a>Ejecución del recopilador para detectar VM

Para cada detección que necesite realizar, ejecute el recopilador para detectar máquinas virtuales en el ámbito requerido. Ejecute las detecciones una detrás de otra. No se admiten las detecciones simultáneas, y cada detección debe tener un ámbito distinto.

1. En la consola del cliente de vSphere, haga clic con el botón derecho en VM > **Open Console** (Abrir consola).
2. Proporcione el idioma, la zona horaria y las preferencias de contraseña para el dispositivo.
3. En el escritorio, haga clic en el acceso directo **Run collector** (Ejecutar recopilador).
4. En Azure Migrate Collector, abra **Set Up Prerequisites** (Configurar requisitos previos).
    - Acepte los términos de licencia y lea la información de terceros.
    - El recopilador comprueba que la VM tenga acceso a Internet.
    - Si la VM tiene acceso a Internet a través de un proxy, haga clic en **Proxy settings** (Configuración de proxy) y especifique el puerto de escucha y la dirección del proxy. Especifique las credenciales si el proxy requiere autenticación.
    - El recopilador comprueba que el servicio del generador de perfiles de Windows se está ejecutando. El servicio se instala de forma predeterminada en la VM de recopilador.
    - Descargue e instale VMware PowerCLI.
. En **Detectar máquinas**, realice lo siguiente:
    - Especifique el nombre (FQDN) o la dirección IP del servidor vCenter.
    - En **Nombre de usuario** y **Contraseña**, especifique las credenciales de cuenta de solo lectura que utilizará el recopilador para detectar las VM en el servidor vCenter.
    - En **Ámbito del grupo**, seleccione un ámbito para la detección de VM. El recopilador solo puede detectar VM dentro del ámbito especificado. El ámbito se puede establecer en una carpeta, centro de datos o clúster específico. No debe contener más de 1000 VM. 
    - En n **Tag category for grouping** (Categoría de etiqueta para agrupar), seleccione **Ninguna**.

        ![Selección del ámbito](./media/how-to-scale-assessment/select-scope.png)

1. En **Select Project** (Seleccionar proyecto), especifique el identificador y la clave del proyecto. Si no los copió, abra Azure Portal desde la VM de recopilador. En la página **Introducción** del proyecto, haga clic en **Detectar máquinas** y copie los valores.  
En **Complete Discovery** (Detección completada), supervise el proceso de detección y compruebe que los metadatos recopilados de las VM se encuentran dentro del ámbito. El recopilador proporciona un tiempo de detección aproximado.


### <a name="verify-vms-in-the-portal"></a>Comprobación de VM en el portal

El tiempo de detección depende de cuántas VM se están detectando. Normalmente, para 100 VM, después de que el recopilador finaliza la ejecución, la detección tarda alrededor de una hora en completarse. 

1. En el proyecto de Migration Planner, haga clic en **Administrar** > **Máquinas**.
2. Compruebe que las VM que desea detectar aparecen en el portal.


## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [crear un grupo](how-to-create-a-group.md) para evaluación.
- [Obtenga más información](concepts-assessment-calculation.md) sobre cómo se calculan las evaluaciones.