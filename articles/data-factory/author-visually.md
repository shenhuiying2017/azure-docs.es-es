---
title: "Creación visual en Azure Data Factory | Microsoft Docs"
description: "Información acerca de cómo utilizar la creación visual de Azure Data Factory"
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
ms.openlocfilehash: 81b97bb6b6abb5431bedd4efec5f807fa577c4e4
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="visual-authoring-in-azure-data-factory"></a>Creación visual de Azure Data Factory
La experiencia de la interfaz de usuario (UX) de Azure Data Factory le permite crear e implementar visualmente recursos para la factoría de datos sin tener que escribir código. Puede arrastrar y colocar las actividades en un lienzo de canalización, realizar ejecuciones de prueba, depurar de forma iterativa e implementar y supervisar ejecuciones de canalizaciones. Hay dos enfoques a la hora de utilizar la experiencia de la interfaz de usuario para llevar a cabo la creación visual:

- Crear directamente con el servicio Data Factory.
- Crear con la integración de Git de Visual Studio Team Services (VSTS) para la colaboración, el control de código fuente o el control de versiones.

## <a name="author-directly-with-the-data-factory-service"></a>Creación directa con el servicio Data Factory
La creación visual con el servicio Data Factory difiere de la creación visual con VSTS de dos maneras:

- El servicio Data Factory no incluye un repositorio que permita almacenar las entidades JSON para los cambios.
- El servicio Data Factory no está optimizado para la colaboración ni el control de versiones.

![Configuración del servicio Data Factory ](media/author-visually/configure-data-factory.png)

Cuando usa el **lienzo de creación** de UX para crear directamente con el servicio Data Factory, solo está disponible el modo **Publicar**. Los cambios que realice se publicarán directamente en el servicio Data Factory.

![Modo Publicar](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>Creación con la integración de Git de VSTS
La creación visual con la integración de Git de VSTS admite el control del código fuente y la colaboración para trabajar en canalizaciones de factoría de datos. Puede asociar una factoría de datos con un repositorio de cuentas de Git de VSTS para el control del código fuente, la colaboración, el control de versiones, etc. Una sola cuenta de Git de VSTS puede tener varios repositorios, pero un repositorio de Git de VSTS solo puede asociarse a una factoría de datos. Si no tiene un repositorio o una cuenta de VSTS, siga [estas instrucciones](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student) para crear los recursos.

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>Configuración de un repositorio de Git de VSTS con Azure Data Factory
Los usuarios pueden configurar un repositorio GIT de VSTS con una factoría de datos mediante dos métodos.

<a name="method1"></a>
#### <a name="configuration-method-1-lets-get-started-page"></a>Método 1 de configuración: Página Comencemos
En Azure Data Factory, vaya a la página **Comencemos**. Seleccione **Configuración del repositorio de código**:

![Configuración de un repositorio de código de VSTS](media/author-visually/configure-repo.png)

Aparece el panel **Configuración del repositorio**:

![Configuración del repositorio de código](media/author-visually/repo-settings.png)

El panel muestra la siguiente configuración del repositorio de código de VSTS:

| Configuración | DESCRIPCIÓN | Valor |
|:--- |:--- |:--- |
| **Tipo de repositorio** | El tipo de repositorio de código de VSTS.<br/>**Nota**: GitHub actualmente no se admite. | Git de Visual Studio Team Services |
| **Cuenta de Visual Studio Team Services** | El nombre de su cuenta de VSTS. Puede buscar el nombre de cuenta de VSTS en `https://{account name}.visualstudio.com`. Puede [iniciar sesión en su cuenta de VSTS](https://www.visualstudio.com/team-services/git/) para acceder a su perfil de Visual Studio y ver sus proyectos y repositorios. | \<su nombre de cuenta> |
| **ProjectName** | El nombre del proyecto de VSTS. Puede buscar el nombre de proyecto de VSTS en `https://{account name}.visualstudio.com/{project name}`. | \<su nombre de proyecto de VSTS> |
| **RepositoryName** | El nombre del repositorio de código de VSTS. Los proyectos de VSTS contienen repositorios de Git para administrar el código fuente a medida que crece el proyecto. Puede crear un repositorio nuevo o usar uno existente en el proyecto. | \<el nombre del repositorio de código de VSTS> |
| **Import existing Data Factory resources to repository** (Importar recursos existentes de Data Factory en el repositorio). | Especifica si se deben importar los recursos de la factoría de datos existente del **lienzo de creación** de UX en un repositorio Git de VSTS. Active la casilla para importar los recursos de la factoría de datos en el repositorio Git asociado en formato JSON. Esta acción exporta cada recurso individualmente (es decir, los servicios vinculados y los conjuntos de datos se exportan a archivos JSON independientes). Cuando esta casilla no está activada, no se importan los recursos existentes. | Activada (valor predeterminado) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Método de configuración 2: lienzo de creación de UX
En el **lienzo de creación** de la experiencia de interfaz de usuario de Azure Data Factory, busque su factoría de datos. Seleccione el menú desplegable **Factoría de datos** y, a continuación, seleccione **Configurar repositorio de código**.

Aparece un panel de configuración. Para obtener más detalles acerca de las opciones de configuración, consulte las descripciones en <a href="#method1">Método de configuración 1</a>.

![Configuración del repositorio de código para la creación con UX](media/author-visually/configure-repo-2.png)

### <a name="use-version-control"></a>Uso del control de versiones
Los sistemas de control de versiones, conocidos también como _de control de código fuente_, permiten a los desarrolladores colaborar en el código y llevar a cabo el seguimiento de los cambios realizados en la base de código. El control del código fuente es una herramienta esencial para proyectos de varios desarrolladores.

Tan pronto como cada repositorio Git de VSTS se asocia a una factoría de datos, tiene una rama principal. Cuando accede a un repositorio Git de VSTS, puede cambiar el código eligiendo **Sincronizar** o **Publicar**:

![Cambio del código sincronizando o publicando](media/author-visually/sync-publish.png)

#### <a name="sync-code-changes"></a>Cambios de código sincronizando
Tras seleccionar **Sincronizar**, puede incorporar los cambios desde la rama principal a la rama local, o desde la rama local a la rama principal.

![Cambios de código sincronizando](media/author-visually/sync-change.png)

#### <a name="publish-code-changes"></a>Cambios de código publicando
Seleccione **Publicar** para publicar manualmente los cambios de código en la bifurcación principal para el servicio Data Factory.

> [!IMPORTANT]
> La rama principal no es representativa de lo que se implementa en el servicio Data Factory. La rama principal se *debe* publicar manualmente en el servicio Data Factory.

## <a name="use-the-expression-language"></a>Uso del lenguaje de expresión
Puede especificar expresiones para los valores de propiedad mediante el lenguaje de expresión que admite Azure Data Factory. Para más información sobre las expresiones admitidas, consulte [Expresiones y funciones de Azure Data Factory](control-flow-expression-language-functions.md).

Especifique expresiones para los valores de propiedad mediante el **lienzo de creación** de UX:

![Uso del lenguaje de expresión](media/author-visually/expression-language.png)

## <a name="specify-parameters"></a>Especificación de parámetros
Puede especificar parámetros para canalizaciones y conjuntos de datos en la pestaña **Parameters** (Parámetros) de Azure Data Factory. Puede usar fácilmente los parámetros en las propiedades seleccionando **Agregar contenido dinámico**:

![Incorporación de contenido dinámico](media/author-visually/dynamic-content.png)

Puede utilizar los parámetros existentes o especificar otros nuevos para los valores de propiedad:

![Especificación de parámetros para los valores de propiedad](media/author-visually/parameters.png)

## <a name="provide-feedback"></a>Envío de comentarios
Seleccione **Comentarios** para comentar sobre las características o para notificar a Microsoft sobre los problemas con la herramienta:

![Comentarios](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>pasos siguientes
Para más información sobre la supervisión y la administración de canalizaciones, consulte el artículo [Supervisión y administración de canalizaciones mediante programación](monitor-programmatically.md).
