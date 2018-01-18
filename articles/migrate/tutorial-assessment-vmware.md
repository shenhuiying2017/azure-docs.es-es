---
title: "Detección y evaluación de VM de VMware locales para la migración a Azure con Azure Migrate | Microsoft Docs"
description: "Describe cómo detectar y evaluar VM de VMware locales para la migración a Azure, utilizando el servicio Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: raynew
ms.openlocfilehash: a5019d3f729f2efbd01fca021b0089c7f99b0014
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Detección y evaluación de VM de VMware locales para migración a Azure

El servicio [Azure Migrate](migrate-overview.md) evalúa las cargas de trabajo locales para su migración a Azure.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Cree un proyecto de Azure Migrate.
> * Configure una máquina virtual (VM) de recopilador local para detectar VM de VMware locales para la evaluación.
> * Agrupe las VM y cree una evaluación.


Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de empezar.


## <a name="prerequisites"></a>Requisitos previos

- **VMware**: las máquinas virtuales que planea migrar deben administrarse mediante vCenter Server, versión 5.5, 6.0 o 6.5. Además, necesita un host de ESXi que ejecute la versión 5.0 o posterior para implementar la máquina virtual del recopilador. 
 
> [!NOTE]
> La compatibilidad con Hyper-V está en el mapa de ruta y se habilitará próximamente. 

- **Cuenta de vCenter Server** : necesita una cuenta de solo lectura para acceder el servidor vCenter. Azure Migrate usa esta cuenta para detectar las máquinas virtuales locales.
- **Permisos**: en el servidor vCenter Server, necesitará permisos para crear una máquina virtual mediante la importación de un archivo en formato .OVA. 
- **Configuración de estadísticas**: la configuración de las estadísticas del servidor vCenter Server se debe establecer en el nivel 3 antes de empezar la implementación. Si el nivel es inferior al 3, la valoración funcionará, pero no se recopilarán datos de rendimiento del almacenamiento y la red. El tamaño de las recomendaciones en este caso se hará según los datos de rendimiento de los datos de CPU y memoria, y de los datos de configuración de adaptadores de red y disco. 

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal
Inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="create-a-project"></a>Creación de un proyecto

1. En Azure Portal, haga clic en **Crear un recurso**.
2. Busque **Azure Migrate** y seleccione el servicio **Azure Migrate (versión preliminar)** en los resultados de búsqueda. A continuación, haga clic en **Crear**.
3. Especifique un nombre de proyecto y la suscripción de Azure para el proyecto.
4. Cree un nuevo grupo de recursos.
5. Especifique la ubicación en la que se va a crear el proyecto y haga clic en **Crear**. Los proyectos de Azure Migrate solo se pueden crear en la región Oeste del centro de EE. UU. para esta versión preliminar. Sin embargo, todavía puede planear la migración de cualquier ubicación de Azure de destino. La ubicación especificada para el proyecto solo se utiliza para almacenar los metadatos que se recopilan a partir de máquinas virtuales locales. 

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>Descarga del dispositivo de recopilador

Azure Migrate crea una VM local conocida como el dispositivo de recopilador. Esta VM detecta VM de VMware locales y envía los metadatos sobre ellas para al servicio Azure Migrate. Para configurar el dispositivo de recopilador, descargue un archivo .OVA e impórtelo en el servidor vCenter local para crear la VM.

1. En el proyecto de Azure Migrate, haga clic en **Introducción** > **Detectar y evaluar** > **Detectar máquinas**.
2. En **Detectar máquinas**, haga clic en **Descargar**, para descargar el archivo .OVA.
3. En **Copiar las credenciales del proyecto**, copie la clave y el identificador del proyecto. Los necesitará cuando configure el recopilador.

    ![Descarga del archivo .ova](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Comprobación del dispositivo de recopilador

Compruebe que el archivo .OVA es seguro, antes de implementarlo.

1. En la máquina en la que descargó el archivo, abra una ventana de comandos de administrador.
2. Ejecute el siguiente comando para generar el código hash para el archivo OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. El código hash generado debe coincidir con esta configuración.
    
    Para la versión 1.0.8.49 de OVA
    **Algoritmo** | **Valor del código hash**
    --- | ---
    MD5 | 8779eea842a1ac465942295c988ac0c7 
    SHA1 | c136c52a0f785e1fd98865e16479dd103704887d
    SHA256 | 5143b1144836f01dd4eaf84ff94bc1d2c53f51ad04b1ca43ade0d14a527ac3f9

    Para la versión 1.0.8.40 de OVA:

    **Algoritmo** | **Valor del código hash**
    --- | ---
    MD5 |afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>Creación de la VM de recopilador

Importe el archivo descargado en el servidor vCenter Server.

1. En la consola de cliente de vSphere, haga clic en **File** (Archivo) > **Deploy OVF Template** (Implementar plantilla de OVF).

    ![Implementación de OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. En el Deploy OVF Template Wizard (Asistente para implementar la plantilla de OVF) > **Source** (Origen), especifique la ubicación del archivo .ova.
3. En **Name** (Nombre) y **Location** (Ubicación), especifique un nombre descriptivo para la VM de recopilador y el objeto de inventario en el que se hospedará la VM.
5. En **Host/Cluster** (Host o clúster), especifique el host o clúster donde se ejecutará el recopilador de máquina virtual.
7. En el almacenamiento, especifique el destino de almacenamiento para la VM de recopilador.
8. En **Disk Format** (Formato de disco), especifique el tamaño y el tipo de disco.
9. En **Network Mapping** (Asignación de red), especifique la red a la que se conectará la VM de recopilador. La red necesita conectividad a Internet, para enviar metadatos a Azure. 
10. Revise y confirme la configuración y haga clic en **Finish** (Finalizar).

## <a name="run-the-collector-to-discover-vms"></a>Ejecución del recopilador para detectar VM

1. En la consola del cliente de vSphere, haga clic con el botón derecho en VM > **Open Console** (Abrir consola).
2. Proporcione el idioma, la zona horaria y las preferencias de contraseña para el dispositivo.
3. En el escritorio, haga clic en el acceso directo **Run collector** (Ejecutar recopilador).
4. En Azure Migrate Collector, abra **Set Up Prerequisites** (Configurar requisitos previos).
    - Acepte los términos de licencia y lea la información de terceros.
    - El recopilador comprueba que la VM tenga acceso a Internet.
    - Si la VM tiene acceso a Internet a través de un proxy, haga clic en **Proxy settings** (Configuración de proxy) y especifique el puerto de escucha y la dirección del proxy. Especifique las credenciales si el proxy requiere autenticación.

    > [!NOTE]
    > La dirección de proxy debe especificarse con el formato http://ProxyIPAddress o http://ProxyFQDN. Solo se admite un proxy HTTP.

    - El recopilador comprueba que el servicio del recopilador se está ejecutando. El servicio se instala de forma predeterminada en la VM de recopilador.
    - Descargue e instale VMware PowerCLI.

5. En **Specify vCenter Server details** (Especificar detalles de vCenter Server), haga lo siguiente:
    - Especifique el nombre (FQDN) o la dirección IP del servidor vCenter.
    - En **Nombre de usuario** y **Contraseña**, especifique las credenciales de cuenta de solo lectura que utilizará el recopilador para detectar las VM en el servidor vCenter.
    - En **Ámbito del grupo**, seleccione un ámbito para la detección de VM. El recopilador solo puede detectar VM dentro del ámbito especificado. El ámbito se puede establecer en una carpeta, centro de datos o clúster específico. No debe contener más de 1000 VM. 

6. En **Specify migration project** (Especificar proyecto de migración), especifique la clave y el identificador de proyecto de Azure Migrate que copió del portal. Si no los copió, abra Azure Portal desde la VM de recopilador. En la página **Introducción** del proyecto, haga clic en **Detectar máquinas** y copie los valores.  
7. En **View collection progress** (Ver progreso de recopilación), supervise la detección y compruebe que los metadatos recopilados de las máquinas virtuales se encuentran dentro del ámbito. El recopilador proporciona un tiempo de detección aproximado.

> [!NOTE]
> El recopilador solo admite "Inglés (Estados Unidos)" como el idioma del sistema operativo y el idioma de la interfaz del recopilador. Próximamente habrá compatibilidad con más idiomas.


### <a name="verify-vms-in-the-portal"></a>Comprobación de VM en el portal

El tiempo de detección depende de cuántas VM se están detectando. Normalmente, para 100 VM, después de que el recopilador finaliza la ejecución, la detección tarda alrededor de una hora en completarse. 

1. En el proyecto de Migration Planner, haga clic en **Administrar** > **Máquinas**.
2. Compruebe que las VM que desea detectar aparecen en el portal.


## <a name="create-and-view-an-assessment"></a>Creación y visualización de una evaluación

Después de detectar las VM, las agrupará y creará una evaluación. 

1. En la página **Introducción** del proyecto, haga clic en **+Crear evaluación**.
2. Haga clic en **View all** (Ver todo) para revisar la configuración de la valoración.
3. Cree el grupo y especifique un nombre para él.
4. Seleccione las máquinas que desee agregar al grupo.
5. Haga clic en **Crear evaluación** para crear el grupo y la evaluación.
6. Una vez creada la evaluación, puede verla en **Introducción** > **Panel**.
7. Haga clic en **Exportar la evaluación** para descargarla como un archivo de Excel.

### <a name="sample-assessment"></a>Evaluación de ejemplo

Este es un ejemplo de informe de evaluación. Incluye información sobre si las VM son compatibles con Azure y los costos mensuales estimados. 

![Informe de evaluación](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Preparación para Azure

Esta vista muestra el estado de preparación para cada máquina.

- Para las VM que están preparadas, Azure Migrate recomienda un tamaño de VM en Azure.
- Para VM que no están preparadas, Azure Migrate explica por qué y proporciona los pasos de corrección.
- Azure Migrate sugiere herramientas que puede usar para la migración. Si la máquina es adecuada para la migración de elevación y cambio, se recomienda el servicio Azure Site Recovery. Si es una máquina de base de datos, se recomienda Azure Database Migration Service.

  ![Preparación de la evaluación](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Costo mensual estimado

Esta vista muestra el cálculo total y el costo de almacenamiento por ejecutar las máquinas virtuales en ejecución en Azure, además de los detalles de cada máquina. Las estimaciones de costos se calculan mediante las recomendaciones de tamaño basadas en el rendimiento para una máquina y sus discos y las propiedades de la evaluación. 

> [!NOTE]
> La estimación de costos que proporciona Azure Migrate es por ejecutar las máquinas virtuales locales como infraestructura de Azure como máquinas virtuales de infraestructura como servicio (IaaS) de Azure. Azure Migrate no tiene en cuenta los costos de cualquier plataforma como servicio (PaaS) o software como servicio (SaaS). 

Los costos mensuales estimados para el proceso y almacenamiento se agregan para todas las VM del grupo. 

![Costo de VM de evaluación](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

Puede obtener un desglose para ver los detalles de una máquina específica.

![Costo de VM de evaluación](./media/tutorial-assessment-vmware/assessment-vm-drill.png) 

## <a name="next-steps"></a>pasos siguientes

- [Aprenda](how-to-scale-assessment.md) a detectar y evaluar un entorno grande de VMware.
- Aprenda a crear grupos de evaluación de confianza elevada mediante la [asignación de dependencias de máquina](how-to-create-group-machine-dependencies.md)
- [Obtenga más información](concepts-assessment-calculation.md) sobre cómo se calculan las evaluaciones.
