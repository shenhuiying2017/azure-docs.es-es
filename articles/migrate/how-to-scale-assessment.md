---
title: "Escalado de detección y evaluación mediante Azure Migrate | Microsoft Docs"
description: "Se describe cómo evaluar un número elevado de máquinas locales mediante el servicio Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/08/2018
ms.author: raynew
ms.openlocfilehash: 67661e03e65cde3ec2f1aafd5ef755899cf0c77b
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Detección y evaluación de un entorno grande de VMware

En este artículo se describe cómo evaluar un número elevado de máquinas virtuales (VM) locales mediante [Azure Migrate](migrate-overview.md). Azure Migrate evalúa máquinas para comprobar si son adecuadas para la migración a Azure. El servicio proporciona estimaciones de tamaño y costo para ejecutar las máquinas en Azure.

## <a name="prerequisites"></a>Requisitos previos

- **VMware**: las máquinas virtuales que planea migrar deben administrarse mediante vCenter Server, versión 5.5, 6.0 o 6.5. Además, necesita un host de ESXi que ejecute la versión 5.0 o posterior para implementar la máquina virtual del recopilador.
- **Cuenta de vCenter**: necesita una cuenta de solo lectura para acceder a vCenter Server. Azure Migrate usa esta cuenta para detectar las máquinas virtuales locales.
- **Permisos**: en vCenter Server, necesitará permisos para crear una máquina virtual mediante la importación de un archivo en formato OVA.
- **Configuración de estadísticas**: la configuración de las estadísticas de vCenter Server se debe establecer en el nivel 3 antes de empezar la implementación. Si el nivel es inferior al 3, la evaluación funcionará, pero no se recopilarán datos de rendimiento del almacenamiento y la red. El tamaño de las recomendaciones en este caso se basará en los datos de rendimiento de los datos de CPU y memoria, y de los datos de configuración de adaptadores de red y disco.

## <a name="plan-azure-migrate-projects"></a>Planeación de proyectos de Azure Migrate

Planee las detecciones y evaluaciones en función de los límites siguientes:

| **Entidad** | **Límite de máquinas** |
| ---------- | ----------------- |
| proyecto    | 1500              | 
| Detección  | 1000              |
| Evaluación | 400               |

- Si tiene menos de 400 máquinas para detectar y evaluar, necesita un solo proyecto y una sola detección. En función de los requisitos, puede evaluar todas las máquinas en una única evaluación o dividir las máquinas en varias evaluaciones. 
- Si tiene menos entre 400 y 1000 máquinas para detectar, necesita un solo proyecto con una sola detección. Sin embargo, necesitará varias evaluaciones para evaluar estas máquinas porque una evaluación solo puede contener un máximo de 400 máquinas.
- Si tiene entre 1001 y 1500 máquinas, necesita un solo proyecto con dos detecciones.
- Si tiene más de 1500 máquinas, debe crear varios proyectos y realizar varias detecciones, según sus requisitos. Por ejemplo: 
    - Si tiene 3000 máquinas, puede configurar dos proyectos con dos detecciones o tres proyectos con una sola detección.
    - Si tiene 5000 máquinas, puede configurar cuatro proyectos: dos con una detección de 1500 máquinas y uno con una detección de 500 máquinas. También puede configurar cinco proyectos con una sola detección en cada uno. 

## <a name="plan-multiple-discoveries"></a>Planeación de varias detecciones

Puede usar la misma instancia de Azure Migrate Collector para realizar varias detecciones en uno o varios proyectos. Tenga en cuenta estas consideraciones de planeación:
 
- Al realizar una detección mediante Azure Migrate Collector, puede establecer el ámbito de detección en una carpeta, un centro de datos, un clúster o un host de vCenter Server.
- Para hacer más de una detección, verifique en vCenter Server si las máquinas virtuales que desea detectar están en carpetas, en centros de datos o en clústeres que admiten la limitación de 1000 máquinas.
- Se recomienda que, para los fines de evaluación, mantenga las máquinas con interdependencias dentro del mismo proyecto y de la misma evaluación. En vCenter Server, asegúrese de que las máquinas dependientes están en la misma carpeta, centro de datos o clúster para la evaluación.


## <a name="create-a-project"></a>Creación de un proyecto

Cree un proyecto de Azure Migrate según sus necesidades:

1. En Azure Portal, haga clic en **Crear un recurso**.
2. Busque **Azure Migrate** y seleccione el servicio **Azure Migrate (versión preliminar)** en los resultados de búsqueda. Seleccione **Crear**.
3. Especifique un nombre de proyecto y la suscripción de Azure para el proyecto.
4. Cree un nuevo grupo de recursos.
5. Especifique la ubicación en la que desea crear el proyecto y seleccione **Crear**. Tenga en cuenta que todavía puede evaluar las máquinas virtuales para una ubicación de destino diferente. La ubicación especificada para el proyecto se utiliza para almacenar los metadatos que se recopilan a partir de máquinas virtuales locales.

## <a name="set-up-the-collector-appliance"></a>Configuración del dispositivo de recopilador

Azure Migrate crea una VM local conocida como el dispositivo de recopilador. Esta máquina virtual detecta máquinas virtuales de VMware locales y envía metadatos sobre ellas al servicio Azure Migrate. Para configurar la aplicación del recopilador, descargue un archivo OVA e impórtelo a la instancia de vCenter Server local.

### <a name="download-the-collector-appliance"></a>Descarga del dispositivo de recopilador

Si tiene varios proyectos, tiene que descargar la aplicación del recopilador solo una vez en vCenter Server. Después de descargar y configurar la aplicación, ejecútela en cada proyecto y especifique el identificador de proyecto exclusivo y la clave.

1. En el proyecto de Azure Migrate, seleccione **Introducción** > **Detectar y evaluar** > **Detectar máquinas**.
2. En **Detectar máquinas**, seleccione **Descargar** para descargar el archivo OVA.
3. En **Copiar las credenciales del proyecto**, copie la clave y el identificador del proyecto. Los necesitará cuando configure el recopilador.

   
### <a name="verify-the-collector-appliance"></a>Comprobación del dispositivo de recopilador

Compruebe que el archivo OVA es seguro, antes de implementarlo:

1. En la máquina en la que descargó el archivo, abra una ventana de comandos de administrador.
2. Ejecute el siguiente comando para generar el código hash para el archivo OVA:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Ejemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Asegúrese de que el código hash generado coincida con la configuración siguiente.
 
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

Importe el archivo descargado a vCenter Server:

1. En la consola de cliente de vSphere, seleccione **File** (Archivo) > **Deploy OVF Template** (Implementar plantilla de OVF).

    ![Implementación de OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. En el Deploy OVF Template Wizard (Asistente para implementar la plantilla de OVF) > **Source** (Origen), especifique la ubicación del archivo OVA.
3. En **Name** (Nombre) y **Location** (Ubicación), especifique un nombre descriptivo para la VM de recopilador y el objeto de inventario en el que se hospedará la VM.
5. En **Host/Cluster** (Host o clúster), especifique el host o clúster donde se ejecutará el recopilador de máquina virtual.
7. En el almacenamiento, especifique el destino de almacenamiento para la VM de recopilador.
8. En **Disk Format** (Formato de disco), especifique el tamaño y el tipo de disco.
9. En **Network Mapping** (Asignación de red), especifique la red a la que se conectará la VM de recopilador. La red necesita conectividad a Internet, para enviar metadatos a Azure. 
10. Revise y confirme la configuración y, a continuación, seleccione **Finish** (Finalizar).

## <a name="identify-the-id-and-key-for-each-project"></a>Identificación del identificador y de la clave de cada proyecto

Si tiene varios proyectos, asegúrese de identificar el identificador y la clave de cada uno. Necesita la clave al ejecutar el recopilador para detectar las máquinas virtuales.

1. En el proyecto, seleccione **Introducción** > **Detectar y evaluar** > **Detectar máquinas**.
2. En **Copiar las credenciales del proyecto**, copie la clave y el identificador del proyecto. 
    ![Copia de las credenciales del proyecto](./media/how-to-scale-assessment/copy-project-credentials.png)

## <a name="set-the-vcenter-statistics-level"></a>Configuración del nivel de estadísticas de vCenter
A continuación, se muestra la lista de contadores de rendimiento recopilados durante la detección. Los contadores están disponibles de forma predeterminada en distintos niveles de vCenter Server. 

Se recomienda establecer el nivel común más alto (3) para el nivel de estadísticas, para que todos los contadores se recopilen correctamente. Si tiene vCenter establecido en un nivel inferior, solo algunos contadores pueden recopilarse por completo y los demás estarán establecidos en 0. Por lo tanto, puede que la evaluación muestre datos incompletos. 

En la tabla siguiente también se muestran los resultados de la evaluación que resultarán afectados si no se recopila un contador determinado.

|Contador                                  |Nivel    |Nivel por dispositivo  |Impacto en la evaluación                               |
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
3. En el escritorio, seleccione el acceso directo **Run collector** (Ejecutar recopilador).
4. En Azure Migrate Collector, abra **Set Up Prerequisites** (Configurar requisitos previos) y luego:

   a. Acepte los términos de licencia y lea la información de terceros.

   El recopilador comprueba que la VM tenga acceso a Internet.
   
   b. Si la máquina virtual tiene acceso a Internet a través de un proxy, seleccione **Configuración de proxy** y especifique el puerto de escucha y la dirección del proxy. Especifique las credenciales si el proxy requiere autenticación.

   El recopilador comprueba que el servicio del recopilador se está ejecutando. El servicio se instala de forma predeterminada en la VM de recopilador.

   c. Descargue e instale VMware PowerCLI.

5. En **Specify vCenter Server details** (Especificar detalles de vCenter Server), haga lo siguiente:
    - Especifique el nombre (FQDN) o la dirección IP de vCenter Server.
    - En **Nombre de usuario** y **Contraseña**, especifique las credenciales de cuenta de solo lectura que utilizará el recopilador para detectar las máquinas virtuales en vCenter Server.
    - En **Seleccionar ámbito**, especifique un ámbito para la detección de máquinas virtuales. El recopilador solo puede detectar máquinas virtuales dentro del ámbito especificado. El ámbito se puede establecer en una carpeta, centro de datos o clúster específico. No debe contener más de 1000 máquinas virtuales. 

6. En **Specify migration project** (Especificar proyecto de migración), especifique el identificador y la clave del proyecto. Si no los copió, abra Azure Portal desde la máquina virtual de recopilador. En la página **Introducción** del proyecto, seleccione **Detectar máquinas** y copie los valores.  
7. En **View collection progress** (Ver progreso de recopilación), supervise el proceso de detección y compruebe que los metadatos recopilados de las máquinas virtuales se encuentran dentro del ámbito. El recopilador proporciona un tiempo de detección aproximado.


### <a name="verify-vms-in-the-portal"></a>Comprobación de VM en el portal

El tiempo de detección depende de cuántas VM se están detectando. Normalmente, para 100 máquinas virtuales, la detección tarda alrededor de una hora en completarse después de que el recopilador finaliza la ejecución. 

1. En el proyecto de Migration Planner, seleccione **Administrar** > **Máquinas**.
2. Compruebe que las VM que desea detectar aparecen en el portal.


## <a name="next-steps"></a>pasos siguientes

- Obtenga información sobre cómo [crear un grupo](how-to-create-a-group.md) para evaluación.
- [Obtenga más información](concepts-assessment-calculation.md) sobre cómo se calculan las evaluaciones.
