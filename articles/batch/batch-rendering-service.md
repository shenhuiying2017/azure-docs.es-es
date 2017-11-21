---
title: "Servicio Azure Batch Rendering: representación a escala de nube | Microsoft Docs"
description: "Represente trabajos en máquinas virtuales de Azure directamente desde Maya y según la modalidad de pago por uso."
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.topic: hero-article
ms.date: 09/14/2017
ms.author: danlep
ms.openlocfilehash: aab68b4275edc48e1726b26a7400bbfdd8d40d6f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2017
---
# <a name="get-started-with-the-batch-rendering-service"></a>Introducción al servicio Batch Rendering

El servicio Azure Batch Rendering ofrece funcionalidades de representación para la nube según una modalidad de pago por uso. Este servicio controla la programación y la puesta en cola de los trabajos, la administración de errores y reintentos, y el escalado automático de los trabajos de representación. Admite [Autodesk Maya](https://www.autodesk.com/products/maya/overview), [3ds Max](https://www.autodesk.com/products/3ds-max/overview), [Arnold](https://www.autodesk.com/products/arnold/overview) y [V-Ray](https://www.chaosgroup.com/vray/maya). El complemento de Batch para Maya 2017 facilita el inicio de un trabajo de representación en Azure justo desde el escritorio.

Con Maya y 3ds Max se pueden ejecutar trabajos mediante la aplicación de escritorio [BatchLabs](https://github.com/Azure/BatchLabs) o las [plantillas de la CLI de Batch](batch-cli-templates.md). Con la CLI de Azure Batch puede ejecutar trabajos de Batch sin escribir código. En su lugar, puede usar archivos de plantilla para crear grupos, trabajos y tareas de Batch. Para más información, consulte [Uso de plantillas y transferencia de archivos de la CLI de Azure Batch](batch-cli-templates.md).


## <a name="supported-applications"></a>Aplicaciones admitidas

El servicio Batch Rendering admite actualmente las siguientes aplicaciones:

- Autodesk Maya
- Autodesk 3ds Max
- Autodesk Arnold for Maya
- Autodesk Arnold for 3ds Max
- Chaos Group V-Ray for Maya
- Chaos Group V-Ray for 3ds Max

## <a name="prerequisites"></a>Requisitos previos

Para usar el servicio Batch Rendering, necesita:

- Una [cuenta de Azure](https://azure.microsoft.com/free/).
- **Una cuenta de Azure Batch** Para obtener instrucciones sobre cómo crear una cuenta de Batch en Azure Portal, consulte [Creación de una cuenta de Batch con Azure Portal](batch-account-create-portal.md).
- **Una cuenta de Azure Storage.** Los recursos usados para el trabajo de representación se almacenan en Azure Storage. Puede crear una cuenta de almacenamiento automáticamente al configurar su cuenta de Batch. También puede usar una cuenta de almacenamiento existente. Para aprender más sobre las cuentas de Storage, consulte [Creación, administración o eliminación de una cuenta de almacenamiento en Azure Portal](https://docs.microsoft.com/azure/storage/storage-create-storage-account).
- **BatchLabs** (opcional). [BatchLabs](https://azure.github.io/BatchLabs) es una herramienta de cliente independiente, completa y gratuita que puede ayudarle a crear, depurar y supervisar aplicaciones de Azure Batch. Aunque no es necesario utilizar el servicio Rendering, es una opción útil para desarrollar y depurar las soluciones de Batch.

Para usar el complemento de Batch para Maya, necesita:

- [Autodesk Maya 2017](https://www.autodesk.com/products/maya/overview).
- Un representador compatible como Arnold for Maya o V-Ray for Maya.

## <a name="basic-batch-concepts"></a>Conceptos sobre Batch básico

Antes de empezar a usar el servicio Batch Rendering, resulta útil familiarizarse con algunos conceptos sobre Batch, como los nodos de proceso, los grupos y los trabajos. Para aprender más sobre Azure Batch en general, consulte [Ejecución de cargas de trabajo paralelas intrínsecamente con Batch](batch-technical-overview.md).

### <a name="pools"></a>Grupos

Batch es un servicio de plataforma para la ejecución de trabajos de proceso intensivo, como la representación de un **grupo** de **nodos de proceso**. Cada nodo de proceso de un grupo es una máquina virtual (VM) de Azure que ejecuta Linux o Windows. 

Para más información sobre los grupos de Batch y los nodos de proceso, consulte las secciones [Grupo](batch-api-basics.md#pool) y [Nodo de proceso](batch-api-basics.md#compute-node) de [Desarrollo de soluciones de procesos paralelos a gran escala con Batch](batch-api-basics.md).

### <a name="jobs"></a>Trabajos

Un **trabajo** de Batch es una colección de tareas que se ejecutan en los nodos de proceso de un grupo. Cuando se envía un trabajo de representación, Batch lo divide en tareas y las distribuye a los nodos de proceso del grupo para ejecutarlas.

Puede usar [Azure Portal](https://ms.portal.azure.com/) para supervisar los trabajos y diagnosticar las tareas con error; para ello, descargue los registros de aplicación y conéctese de forma remota a máquinas virtuales individuales mediante RDP o SSH. También puede administrar, supervisar y depurar mediante la [herramienta BatchLabs](https://azure.github.io/BatchLabs).

Para más información sobre los trabajos de Batch, consulte la sección [Trabajo](batch-api-basics.md#job) de [Desarrollo de soluciones de procesos paralelos a gran escala con Batch](batch-api-basics.md).

## <a name="options-for-provisioning-required-applications"></a>Opciones de aprovisionamiento para las aplicaciones necesarias

Para representar un trabajo se pueden necesitar varias aplicaciones, por ejemplo, la combinación de Maya y Arnold o 3ds Max y V-Ray, así como otros complementos de terceros, si procede. Además, algunos clientes pueden necesitan versiones específicas de estas aplicaciones. Por lo tanto, hay varios métodos disponibles para el aprovisionamiento de las aplicaciones y el software necesarios:

### <a name="pre-configured-vm-images"></a>Imágenes de máquina virtual preconfiguradas

Azure proporciona imágenes de Windows y Linux con las versiones sencillas de Maya, 3ds Máx, Arnold y V-Ray previamente instaladas y listas para su uso. Puede seleccionar estas imágenes en [Azure Portal](https://portal.azure.com), el complemento de Maya o [BatchLabs](https://azure.github.io/BatchLabs) al crear un grupo.

En Azure Portal y BatchLabs puede instalar una de las imágenes de máquina virtual con las aplicaciones preinstaladas de la manera siguiente: en la sección Grupos de la cuenta de Batch, seleccione **Nuevo**; en **Agregar grupo**, seleccione **Gráficos y representación (Linux/Windows)** de la lista desplegable **Tipo de imagen**:

![Selección del tipo de imagen para la cuenta de Batch](./media/batch-rendering-service/add-pool.png)

Desplácese hacia abajo y haga clic en **Licencias de gráficos y representación** para abrir la hoja **elegir licencias** y seleccione una licencia de software o varias:

![Selección de la licencia de gráficos y representación para el grupo](./media/batch-rendering-service/graphics-licensing.png)

Las versiones de licencia específicas que se proporcionan son las siguientes:

- Maya 2017
- 3ds Max 2018
- Arnold for Maya 5.0.1.1
- Arnold for 3ds Max 1.0.836
- V-Ray for Maya 3.52.03
- V-Ray for 3ds Max 3.60.01

### <a name="custom-images"></a>Imágenes personalizadas

Azure Batch permite proporcionar imágenes personalizadas. Con esta opción, puede configurar la máquina virtual con las aplicaciones exactas y las versiones específicas que necesite. Para más información, consulte [Uso de una imagen personalizada para crear un grupo de máquinas virtuales](https://docs.microsoft.com/azure/batch/batch-custom-images). Tenga en cuenta que Autodesk y Chaos Group han modificado Arnold y V-Ray respectivamente para la validación de nuestro servicio de licencias. Debe asegurarse de que tiene las versiones compatibles de estas aplicaciones, de lo contrario, la licencia de pago por uso no funcionará. Esta validación de licencia no es necesaria para Maya ni 3ds Max, ya que las versiones actuales no necesitan servidor de licencias al ejecutarse en modo desatendido (en lote/de línea de comandos). Póngase en contacto con el soporte técnico de Azure si no está seguro de cómo continuar con esta opción.

## <a name="options-for-submitting-a-render-job"></a>Opciones para enviar un trabajo de representación

En función de la aplicación 3D, existen varias opciones para el envío de trabajos de representación al servicio:

### <a name="maya"></a>Maya

Con Maya, puede usar:

- [Complemento de Batch para Maya](https://docs.microsoft.com/azure/batch/batch-rendering-service#use-the-batch-plug-in-for-maya-to-submit-a-render-job)
- Aplicación de escritorio [BatchLabs](https://azure.github.io/BatchLabs)
- [Plantillas de la CLI de Batch](batch-cli-templates.md)

### <a name="3ds-max"></a>3ds Max

Con 3ds Max, puede usar:

- La aplicación de escritorio [BatchLabs](https://azure.github.io/BatchLabs) (consulte el artículo sobre los [datos de BatchLabs](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) como guía de uso de las plantillas de BatchLabs para 3ds Max)
- [Plantillas de la CLI de Batch](batch-cli-templates.md)

Las plantillas de BatchLabs para 3ds Max permiten presentar escenas de VRay y Arnold con el servicio Azure Batch Rendering. Hay dos variaciones de la plantilla para VRay y Arnold, una para escenas estándar y otra para escenas más complejas que requieren un archivo de ruta de acceso a los recursos y las texturas de 3ds Max (archivo .mxp). Para más información sobre las plantillas de BatchLabs para 3ds Max, consulte el repositorio de [datos de BatchLabs](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) de GitHub.

Además, puede usar el [SDK de Batch para Python](https://docs.microsoft.com/azure/batch/batch-python-tutorial) para integrar el servicio de representación con la canalización existente.


## <a name="use-the-batch-plug-in-for-maya-to-submit-a-render-job"></a>Uso del complemento de Batch para Maya para enviar un trabajo de representación

Con el complemento de Batch para Maya, puede enviar un trabajo al servicio Batch Rendering directamente desde Maya. En las secciones siguientes se describe cómo configurar el trabajo desde el complemento y luego enviarlo. 

### <a name="load-the-batch-plug-in-for-maya"></a>Carga del complemento de Batch para Maya

El complemento de Batch está disponible en [GitHub](https://github.com/Azure/azure-batch-maya/releases). Descomprima el archivo en un directorio de su elección. Puede cargar el complemento directamente desde el directorio *azure_batch_maya*.

Para cargar el complemento en Maya:

1. Ejecute Maya.
2. Abra **Window (Ventana)** > **Settings/Preferences (Configuración/Preferencias)** > **Plug-in Manager (Administrador de complementos)**.
3. Haga clic en **Examinar**.
4. Desplácese hasta *azure_batch_maya/plug-in/AzureBatch.py* y selecciónelo.

### <a name="authenticate-access-to-your-batch-and-storage-accounts"></a>Autenticación del acceso a sus cuentas de Batch y Storage

Para usar el complemento, debe autenticarse mediante las claves de cuenta de Azure Batch y Azure Storage. Para recuperar las claves de cuenta:

1. Muestre el complemento en Maya y seleccione la pestaña **Config** (Configuración).
2. Acceda a [Azure Portal](https://portal.azure.com).
3. Seleccione **Cuentas de Batch** en el menú de la izquierda. Si es necesario, haga clic en **Más servicios** y filtre por _Batch_.
4. Busque la cuenta de Batch deseada en la lista.
5. Seleccione el elemento de menú **Claves** para mostrar el nombre de la cuenta, la dirección URL de la cuenta y las claves de acceso:
    - Pegue la dirección URL de la cuenta de Batch en el campo **Servicio** del complemento de Batch.
    - Pegue el nombre de la cuenta en el campo **Cuenta de Batch**.
    - Pegue la clave de cuenta principal en el campo **Clave de Batch**.
7. Seleccione Cuentas de Storage en el menú de la izquierda. Si es necesario, haga clic en **Más servicios** y filtre por _Storage_.
8. Busque la cuenta de Storage deseada en la lista.
9. Seleccione el elemento de menú **Claves de acceso** para mostrar los nombres y las claves de la cuenta de almacenamiento.
    - Pegue el nombre de cuenta de Storage en el campo **Cuenta de Storage** del complemento de Batch.
    - Pegue la clave de cuenta principal en el campo **Clave de Storage**.
10. Haga clic en **Autenticar** para asegurarse de que el complemento puede acceder a ambas cuentas.

Cuando se ha autenticado correctamente, el complemento establece el campo de estado en **Autenticado**: 

![Autenticación de las cuentas de Batch y Storage](./media/batch-rendering-service/authentication.png)

### <a name="configure-a-pool-for-a-render-job"></a>Configuración de un grupo para un trabajo de representación

Cuando se hayan autenticado las cuentas de Batch y Storage, configure un grupo para el trabajo de representación. El complemento guarda las selecciones entre sesiones. Cuando haya realizado su configuración preferida, no tendrá que modificarla a menos que la cambie.

Las secciones siguientes le guían por las opciones disponibles en la pestaña **Submit** (Enviar):

#### <a name="specify-a-new-or-existing-pool"></a>Especificación de un grupo nuevo o existente

Para especificar un grupo en el que ejecutar el trabajo de representación, seleccione la pestaña **Submit** (Enviar). Esta pestaña proporciona opciones para la creación de un grupo o la selección de un grupo existente:

- También puede **aprovisionar automáticamente un grupo en este trabajo** (opción predeterminada). Cuando elige esta opción, Batch crea un grupo exclusivamente para el trabajo actual y lo elimina automáticamente cuando el trabajo de representación finaliza. Esta opción es más adecuada si tiene que realizar un único trabajo de representación.
- También puede **reutilizar un grupo persistente existente**. Si tiene un grupo existente que está inactivo, puede especificar ese grupo para ejecutar el trabajo de representación; para ello, selecciónelo en la lista desplegable. La reutilización de un grupo persistente existente ahorra el tiempo necesario para aprovisionar el grupo.  
- También puede **crear un nuevo grupo persistente**. Si elige esta opción, se crea un nuevo grupo para ejecutar el trabajo. El grupo no se elimina cuando el trabajo finaliza, así que puede reutilizarlo en trabajos futuros. Seleccione esta opción cuando tenga una necesidad continua de ejecutar trabajos de representación. En los trabajos posteriores, puede seleccionar **reutilizar un grupo persistente existente** para usar el grupo persistente que creó para el primer trabajo.

![Especificación del grupo, la imagen del SO, el tamaño de máquina virtual y la licencia](./media/batch-rendering-service/submit.png)

Para más información sobre cómo se acumulan los cargos para las máquinas virtuales de Azure, consulte las [P+F sobre los precios de Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#faq) y las [P+F sobre los precios de Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/#faq).

#### <a name="specify-the-os-image-to-provision"></a>Especificación de la imagen del SO para aprovisionar

Puede especificar el tipo de imagen del SO que se usará para aprovisionar los nodos de proceso en el grupo en la pestaña **Env** (Environment) (Ent [Entorno]). Batch admite actualmente las siguientes opciones de imagen para trabajos de representación:

|Sistema operativo  |Imagen  |
|---------|---------|
|Linux     |Versión preliminar de CentOS para Batch |
|Windows     |Versión preliminar de Windows para Batch |

#### <a name="choose-a-vm-size"></a>Selección del tamaño de la máquina virtual

Puede especificar el tamaño de la máquina virtual en la pestaña **Env** (Ent). Para más información sobre los tamaños de máquina virtual disponibles, consulte [Tamaños de las máquinas virtuales Linux en Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) y [Tamaños de las máquinas virtuales Windows en Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes). 

![Especificación de la imagen del SO y el tamaño de máquina virtual en la pestaña Env (Ent)](./media/batch-rendering-service/environment.png)

#### <a name="specify-licensing-options"></a>Especificación de las opciones de licencia

Puede especificar las licencias que quiere usar en la pestaña **Env** (Ent). Las opciones incluyen:

- **Maya**, que está habilitado de forma predeterminada.
- **Arnold**, que está habilitado si se detecta Arnold como el motor de representación activo de Maya.

 Si quiere realizar representaciones con su propia licencia, puede configurar el punto de conexión de la licencia, para ello, agregue las variables de entorno adecuadas a la tabla. Si lo hace, asegúrese de anular la selección de las opciones de licencia predeterminadas.

> [!IMPORTANT]
> Se le factura por el uso de las licencias mientras las máquinas virtuales estén en ejecución en el grupo, aunque estas no se usen actualmente para la representación. Para evitar recargos, vaya a la pestaña **Pools** (Grupos) y cambie el tamaño del grupo a 0 nodos hasta que esté listo para ejecutar otro trabajo de representación. 
>
>

#### <a name="manage-persistent-pools"></a>Administración de grupos persistentes

Puede administrar un grupo persistente existente en la pestaña **Pools** (Grupos). Al seleccionar un grupo de la lista se muestra el estado actual del grupo.

En la pestaña **Pools** (Grupos), también puede eliminar el grupo y cambiar de tamaño el número de máquinas virtuales del grupo. Puede cambiar el tamaño de un grupo a 0 nodos para evitar incurrir en costos entre cargas de trabajo.

![Visualización, cambio de tamaño y eliminación de grupos](./media/batch-rendering-service/pools.png)

### <a name="configure-a-render-job-for-submission"></a>Configuración de un trabajo de representación para su envío

Cuando haya especificado los parámetros del grupo que ejecutará el trabajo de representación, configure el trabajo propiamente dicho. 

#### <a name="specify-scene-parameters"></a>Especificación de los parámetros de escena

El complemento de Batch detecta el motor de representación que usa actualmente en Maya y muestra la configuración de representación adecuada en la pestaña **Submit** (Enviar). Esta configuración incluye el fotograma de inicio, el fotograma de finalización, el prefijo de salida y el paso de fotograma. Puede invalidar la configuración de presentación del archivo de escena especificando valores diferentes en el complemento. Los cambios realizados en la configuración del complemento no se conservan en la configuración de representación del archivo de escena, así que puede realizar cambios de un trabajo a otro sin necesidad de recargar el archivo de escena.

El complemento le advierte si el motor de representación que seleccionó en Maya no se admite.

Si carga una nueva escena mientras el complemento está abierto, haga clic en el botón **Refresh** (Actualizar) para asegurarse de que la configuración está actualizada.

#### <a name="resolve-asset-paths"></a>Resolución de las rutas de acceso a los recursos

Cuando se carga el complemento, examina el archivo de escena en busca de referencias a archivos externos. Estas referencias se muestran en la pestaña **Assets** (Recursos). Si no se puede resolver una ruta a la que se hace referencia, el complemento intenta encontrar el archivo en unas cuantas ubicaciones predeterminadas, como:

- La ubicación del archivo de escena 
- El directorio _sourceimages_ del proyecto actual
- El directorio de trabajo actual. 

Si aun así, el recurso no se encuentra, se muestra con un icono de advertencia:

![Los recursos que faltan se muestran con un icono de advertencia.](./media/batch-rendering-service/missing_assets.png)

Si conoce la ubicación de una referencia de archivo sin resolver, puede hacer clic en el icono de advertencia para que se le pida que agregue una ruta de acceso de búsqueda. El complemento usa entonces esta ruta de acceso de búsqueda para intentar resolver los recursos que faltan. Puede agregar cualquier número de rutas de acceso de búsqueda adicionales.

Cuando se resuelve una referencia, se muestra con un icono de luz verde:

![Recursos resueltos con un icono de luz verde](./media/batch-rendering-service/found_assets.png)

Si la escena requiere otros archivos que el complemento no ha detectado, puede agregar archivos o directorios adicionales. Use los botones **Add Files** (Agregar archivos) y **Add Directory** (Agregar directorio). Si carga una nueva escena mientras el complemento está abierto, asegúrese de hacer clic en **Refresh** (Actualizar) para actualizar las referencias de la escena.

#### <a name="upload-assets-to-an-asset-project"></a>Carga de recursos en un proyecto de recursos

Cuando se envía un trabajo de representación, los archivos a los que se hace referencia que se muestran en la pestaña **Assets** (Recursos) se cargan automáticamente en Azure Storage como un proyecto de recursos. También puede cargar los archivos de recurso con independencia de un trabajo de representación, mediante el botón **Upload** (Cargar) de la pestaña **Assets** (Recursos). El nombre del proyecto de recursos se especifica en el campo **Project** (Proyecto) y se designa de forma predeterminada después del proyecto actual de Maya. Cuando se cargan los archivos de recurso, se conserva la estructura de archivos local. 

Una vez cargados, cualquier número de trabajos de representación puede hacer referencia a ellos. Todos los recursos cargados están disponibles para cualquier trabajo que haga referencia al proyecto de recursos, con independencia de que estén incluidos en la escena. Para cambiar el proyecto de recursos al que hace referencia el siguiente trabajo, cambie el nombre en el campo **Project** (Proyecto) de la pestaña **Assets** (Recursos). Si hay archivos a los que se hace referencia que quiera excluir de la carga, anule la selección de ellos con el botón verde que hay junto a la lista.

#### <a name="submit-and-monitor-the-render-job"></a>Envío y supervisión del trabajo de representación

Después de haber configurado el trabajo de representación en el complemento, haga clic en el botón **Submit Job** (Enviar trabajo) de la pestaña **Submit** (Enviar) para enviar el trabajo a Batch:

![Envío del trabajo de representación](./media/batch-rendering-service/submit_job.png)

Puede supervisar un trabajo que está en curso en la pestaña **Jobs** (Trabajos) del complemento. Seleccione un trabajo de la lista para mostrar su estado actual. También puede usar esta pestaña para cancelar y eliminar trabajos, así como para descargar las salidas y los registros de representación. 

Para descargar las salidas, modifique el campo **Outputs** (Salidas) para establecer el directorio de destino deseado. Haga clic en el icono de engranaje para iniciar un proceso en segundo plano que supervisa el trabajo y descarga las salidas a medida que avanza: 

![Visualización del estado del trabajo y descarga de las salidas](./media/batch-rendering-service/jobs.png)

Puede cerrar Maya sin interrumpir el proceso de descarga.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Batch, consulte [Ejecución de cargas de trabajo paralelas intrínsecamente con Batch](batch-technical-overview.md).
