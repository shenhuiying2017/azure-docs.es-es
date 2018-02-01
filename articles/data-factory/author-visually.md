---
title: "Creación de factorías de datos de Azure de forma visual | Microsoft Docs"
description: "Aprenda a crear visualmente factorías de datos de Azure"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/9/2018
ms.author: shlo
ms.openlocfilehash: 3e67665facba78c4ca8e2317f0323b4c5c02a49c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="visually-author-data-factories"></a>Creación visual de factorías de datos
Con la experiencia UX de Azure Data Factory, los usuarios pueden crear e implementar recursos visualmente en su factoría de datos sin escribir una sola línea de código. Esta interfaz sin código permite arrastrar y colocar las actividades en un lienzo de canalización, realizar series de pruebas, depurar de forma iterativa, e implementar y supervisar ejecuciones de canalizaciones. Puede elegir usar la herramienta ADF UX de dos maneras:

1. Trabajar directamente con el servicio Data Factory
2. Configurar la integración de Git de VSTS para la colaboración, el control de código fuente o el control de versiones

## <a name="authoring-with-data-factory"></a>Creación con Data Factory
La primera opción es crear directamente con el modo de Data Factory. Este enfoque se diferencia de la creación mediante el repositorio de código de VSTS en que no hay ningún repositorio que almacene las entidades JSON de sus cambios, ni está optimizado para la colaboración o el control de versiones.

![Configurar Data Factory](media/author-visually/configure-data-factory.png)

En el modo Data Factory, solo hay el modo "Publicar". Los cambios que realice se publican directamente en el servicio Data Factory.

![Publicación de Data Factory](media/author-visually/data-factory-publish.png)

## <a name="authoring-with-vsts-git-integration"></a>Creación con la integración de Git de VSTS
La creación con la integración de Git de VSTS permite el control del código fuente y la colaboración al mismo tiempo que la creación de canalizaciones de factoría de datos. Los usuarios tienen la opción de asociar una factoría de datos con un repositorio de cuentas de Git de VSTS para el control del código fuente, la colaboración y el control de versiones, etc. Una sola cuenta de Git de VSTS puede tener varios repositorios. Sin embargo, un repositorio de Git de VSTS solo puede asociarse con una única factoría de datos. Si aún no tiene una cuenta y un repositorio de VSTS, cree uno [aquí](https://docs.microsoft.com/en-us/vsts/accounts/create-account-msa-or-work-student).

### <a name="configure-vsts-git-repo-with-azure-data-factory"></a>Configuración de un repositorio de Git de VSTS con Azure Data Factory
Los usuarios pueden configurar un repositorio de Git de VSTS con una factoría de datos mediante dos métodos.

#### <a name="method-1-lets-get-started-page"></a>Método 1: Página "Comencemos"

Vaya a la página "Comencemos" y haga clic en "Configurar repositorio de código".

![Configuración del repositorio de código](media/author-visually/configure-repo.png)

Ahí, aparece un panel lateral para configurar los valores del repositorio.

![Configuración de los valores del repositorio](media/author-visually/repo-settings.png)
* **Tipo de repositorio**: Git de Visual Studio Team Services (actualmente no se admite Github).
* **Cuenta de Visual Studio Team Services**: el nombre de la cuenta se puede encontrar en https://{account name}.visualstudio.com. Inicie sesión su cuenta de VSTS [aquí](https://www.visualstudio.com/team-services/git/) y acceda a su perfil de Visual Studio para ver los proyectos y repositorios
* **ProjectName:** el nombre del proyecto se puede encontrar en https://{nombre de la cuenta}.visualstudio.com/{nombre del proyecto}
* **RepositoryName:** el nombre del repositorio. Los proyectos de VSTS contienen repositorios de Git para administrar el código fuente a medida que crece el proyecto. Cree un nuevo repositorio o use uno ya existente en el proyecto.
* **Import existing Data Factory resources to repository** (Importar recursos existentes de Data Factory en el repositorio) :al activar esta casilla, los recursos actuales de factoría de datos creados en el lienzo de UX se pueden importar en el repositorio de Git de VSTS asociado en formato JSON. Esta acción exporta cada recurso individualmente (es decir, los servicios vinculados y los conjuntos de datos se exportan a archivos JSON independientes).    Si desactiva esta casilla, los recursos existentes no se importan en el repositorio de Git.

#### <a name="method-2-from-authoring-canvas"></a>Método 2: Desde Lienzo de creación

En "Lienzo de creación", haga clic en el menú desplegable "Data Factory" debajo del nombre de la factoría de datos. A continuación, haga clic en "Configure Code Repository" (Configurar repositorio de código). Al igual que con el **Método 1**, aparece un panel lateral para configurar el repositorio. Consulte las secciones anteriores para obtener información sobre la configuración.

![Configuración del repositorio de código 2](media/author-visually/configure-repo-2.png)

### <a name="version-control"></a>Control de versiones
Los sistemas de control de versiones, conocidos también como de control de código fuente, permite a los desarrolladores colaborar en el código y realizar el seguimiento de los cambios realizados en la base de código. El control del código fuente es una herramienta esencial para proyectos de varios desarrolladores.

Tan pronto como cada repositorio de Git de VSTS se asocia con una factoría de datos, tiene una rama principal. Ahí, cada usuario que tiene acceso al repositorio de Git de VSTS tiene dos opciones al realizar cambios: sincronizar y publicar.

![Sincronizar y publicar](media/author-visually/sync-publish.png)

#### <a name="sync"></a>Sync

Después de hacer clic en "sincronizar", puede incorporar los cambios de la rama principal a la rama local, o insertar los cambios de la rama local en la rama principal.

![Sincronización de los cambios](media/author-visually/sync-change.png)

#### <a name="publish"></a>Publicar
 Publique los cambios de la rama principal en el servicio Data Factory.

> [!NOTE]
> La **rama principal no es representativa de lo que se implementa en el servicio Data Factory.** La rama principal se *debe* publicar manualmente en el servicio Data Factory.




## <a name="expression-language"></a>Lenguaje de expresiones

Los usuarios pueden especificar expresiones en la definición de valores de propiedad mediante el lenguaje de expresión que admite Azure Data Factory. Para más información sobre las expresiones admitidas, consulte [Expresiones y funciones de Azure Data Factory](control-flow-expression-language-functions.md).

Especifique expresiones en valores de propiedad de UX de este modo.

![Lenguaje de expresiones](media/author-visually/expression-language.png)

## <a name="parameters"></a>Parámetros
Los usuarios pueden especificar parámetros para canalizaciones y conjuntos de datos en la pestaña "Parameters" (Parámetros). Además, utilice parámetros en propiedades fácilmente presionando "Add Dynamic Content" (Agregar contenido dinámico).

![Contenido dinámico](media/author-visually/dynamic-content.png)

Ahí, puede usar un parámetro existente o especificar uno nuevo en el valor de propiedad.

![Parámetros](media/author-visually/parameters.png)

## <a name="feedback"></a>Comentarios
Haga clic en el icono "Feedback" (Comentarios) para enviarnos (Microsoft) comentarios sobre varias características o de los problemas que pudiera tener.

![Comentarios](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>pasos siguientes

Para más información sobre la supervisión y la administración de canalizaciones, consulte el artículo [Supervisión y administración de canalizaciones mediante programación](monitor-programmatically.md).
