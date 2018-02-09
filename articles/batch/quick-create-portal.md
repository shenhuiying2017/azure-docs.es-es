---
title: "Inicio rápido de Azure - Ejecución de un trabajo de Batch - Portal"
description: "Aprenda rápidamente a ejecutar un trabajo de Batch con Azure Portal."
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: na
ms.topic: quickstart
ms.date: 01/19/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: a00c8ea07c31d2ab4ba2638f2a7e4adcf5ca4a10
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Inicio rápido: ejecute su primer trabajo de Batch en Azure Portal

Esta guía de inicio rápido muestra cómo utilizar Azure Portal para crear una cuenta de Batch, un *grupo* de nodos de proceso (máquinas virtuales) y un *trabajo* que ejecuta *tareas* básicas en el grupo. Tras completar esta guía de inicio rápido, entenderá los conceptos clave del servicio Batch y estará listo para probar dicho servicio con cargas de trabajo más realistas y a mayor escala.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure 

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-batch-account"></a>Crear una cuenta de Batch

Siga estos pasos para crear una cuenta de Batch de ejemplo para hacer pruebas. Necesita una cuenta de Batch para crear grupos y trabajos. Como se muestra aquí, cualquier cuenta de Azure Storage se puede vincular con la cuenta de Batch. Aunque no es necesaria para esta guía de inicio rápido, la cuenta de almacenamiento es útil para implementar aplicaciones y almacenar los datos de entrada y salida de la mayoría de las cargas de trabajo reales.


1. Haga clic en **Nuevo** > **Proceso** > **Servicio Batch**. 

  ![Batch en Marketplace][marketplace_portal]

2. Escriba los valores de **Nombre de cuenta** y **Grupo de recursos**. El nombre de cuenta debe ser único en la **ubicación** de Azure seleccionada, debe constar solo de caracteres en minúsculas o números, y contener entre 3 y 24 caracteres. 

3. En **Cuenta de almacenamiento**, seleccione una cuenta de almacenamiento de uso general existente o créela.

4. En el resto mantenga los valores predeterminados y haga clic en **Crear** para crear la cuenta.

  ![Crear una cuenta de Batch][account_portal]  

Cuando aparezca el mensaje **Implementación correcta**, vaya a la cuenta de Batch en el portal.

## <a name="create-a-pool-of-compute-nodes"></a>Creación de un grupo de nodos de proceso

Ahora que tiene una cuenta de Batch, cree un grupo de ejemplo de nodos de proceso de Windows para realizar pruebas. El grupo de este ejemplo rápido consta de 2 nodos en los que se ejecutan una imagen de Windows Server 2012 R2 de Azure Marketplace.


1. En la cuenta de Batch, haga clic en **Grupos** > **Agregar**.

2. En **Identificador del grupo**, escriba *mypool*. 

3. En **Sistema operativo**, seleccione la siguiente configuración (puede explorar otras opciones).
  
  |Configuración  |Valor  |
  |---------|---------|
  |**Tipo de imagen**|Marketplace (Linux/Windows)|
  |**Publicador**     |Microsoft Windows Server|
  |**Oferta**     |Windows Server|
  |**Sku**     |2012-R2-Datacenter-smalldisk|

  ![Seleccionar un sistema operativo del grupo][pool_os] 

4. Desplácese hacia abajo para especificar los valores de **Tamaño de nodo** y **Escala**. El tamaño del nodo que se sugiere ofrece un buen equilibrio entre rendimiento y costo para este ejemplo rápido.
  
  |Configuración  |Valor  |
  |---------|---------|
  |**Plan de tarifa del nodo**     |Standard_A1|
  |**Nodos dedicados de destino**     |2|

  ![Seleccione un tamaño de grupo][pool_size] 

5. En el resto mantenga los valores predeterminados y haga clic en **Aceptar** para crear el grupo.

Batch crea el grupo inmediatamente, pero tarda unos minutos en asignar e iniciar los nodos de proceso. Durante de este tiempo, el valor de **Estado de asignación** del grupo es **Cambio de tamaño**. Puede crear un trabajo y tareas mientras se cambia el tamaño del grupo. 

![Grupo en estado Cambio de tamaño][pool_resizing]

Pocos minutos después el estado del grupo es **Estable** y se inician los nodos. Haga clic en **Nodos** para comprobar el estado de los nodos. Cuando el estado de un nodo es **Inactivo**, está listo para ejecutar las tareas. 

## <a name="create-a-job"></a>Creación de un trabajo

Ahora que tiene un grupo, cree un trabajo para que se ejecute en él. Un trabajo de Batch es un grupo lógico de una o varias tareas. Un trabajo incluye valores comunes para las tareas, como la prioridad y el grupo en el que se ejecutan las tareas. Inicialmente, el trabajo no tiene tareas. 

1. En la vista de la cuentas de Batch, haga clic en **Trabajos** > **Agregar**. 

2. En **Id. de trabajo**, escriba *myjob*. En **Grupo**, seleccione *mypool*. Deje los valores predeterminados para las opciones de configuración restantes y haga clic en **Aceptar**.

  ![Creación de un trabajo][job_create]

Una vez que se crea el trabajo, se abre la página **Tareas**.

## <a name="create-tasks"></a>Creación de tareas

Ahora cree las tareas de ejemplo que se van a ejecutar en el trabajo. Normalmente crea varias tareas que Batch se encarga de poner en cola y distribuir para ejecutarse en los nodos de proceso. En este ejemplo se crean dos tareas idénticas. En cada una de ellas se ejecuta una línea de comandos que muestra las variables de entorno de Batch de un nodo de proceso y, después, espera 90 segundos. 

Cuando se usa Batch, la línea de comandos es el lugar en el que se especifica la aplicación o el script. Batch proporciona varias formas de implementar aplicaciones y scripts en nodos de proceso. 

Para crear la primera tarea:

1. Haga clic en **Agregar**.

2. En **Id. de tarea**, escriba *mytask*. 

3. En **Línea de comandos**, escriba `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Deje los valores predeterminados para las opciones de configuración restantes y haga clic en **Aceptar**.

  ![Crea una tarea.][task_create]

Después de crear una tarea, Batch la pone en cola para ejecutarla en el grupo. Cuando un nodo esté disponible para ejecutarla, la tarea se ejecuta.

Para crear una segunda tarea, vuelva al paso 1. Escriba otro valor en **Id. de tarea**, pero especifique la misma línea de comandos. Si la primera tarea se sigue ejecutando, Batch inicia la segunda tarea en el otro nodo del grupo.

## <a name="view-task-output"></a>Visualización de la salida de la tarea

Los ejemplos anteriores de la tarea se completan en un par de minutos. Para ver la salida de una tarea completada, haga clic en **Archivos en el nodo** y, después, seleccione el archivo `stdout.txt`. Este archivo muestra la salida estándar de la tarea. El contenido es similar al siguiente:

![Visualización de la salida de la tarea][task_output]

El contenido muestra las variables de entorno de Azure Batch que se definen en el nodo. Al crear sus propios trabajos y tareas de Batch, puede hacer referencia a estas variables de entorno tanto en las líneas de comandos de la tarea como en las aplicaciones y los scripts que ejecutan las líneas de comandos.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si desea continuar con los ejemplos y tutoriales de Batch, utilice la cuenta de Batch y la cuenta de almacenamiento vinculada que se ha creado en esta guía de inicio rápido. Por la propia cuenta de Batch no se cobra nada.

Se cobran el grupo mientras que los nodos estén en ejecución, aunque no haya trabajos programados. Cuando no necesite el grupo, elimínelo. En la vista de cuenta, haga clic en **Grupos** y en el nombre del grupo. Después, haga clic en **Eliminar**.  Al eliminar el grupo, las salidas de tarea de los nodos también se eliminan. 

Cuando no los necesite, elimine el grupo de recursos, la cuenta de Batch y todos los recursos relacionados. Para ello, seleccione el grupo de recursos de la cuenta de Batch y haga clic en **Eliminar grupo de recursos**.

## <a name="next-steps"></a>pasos siguientes

En esta guía de inicio rápido, ha creado una cuenta de Batch, un grupo de Batch y un trabajo de Batch. El trabajo ha ejecutado tareas de ejemplo y se ha visto la salida que se ha creado en uno de los nodos. Ahora que conoce los conceptos clave del servicio Batch, ya esta listo para probar dicho servicio con cargas de trabajo más realistas y a mayor escala. Para más información acerca de Azure Batch, continúe con los tutoriales de Azure Batch. 

> [!div class="nextstepaction"]
> [Tutoriales de Azure Batch](./tutorial-parallel-dotnet.md)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png