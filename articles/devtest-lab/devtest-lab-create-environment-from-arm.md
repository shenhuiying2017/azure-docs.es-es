---
title: "Creación de entornos de varias máquinas virtuales y recursos de PaaS con plantillas de Azure Resource Manager | Microsoft Docs"
description: "Obtenga información acerca de cómo crear entornos de varias máquinas virtuales y recursos de PaaS en Azure DevTest Labs a partir de una plantilla de Azure Resource Manager"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: tarcher
ms.openlocfilehash: 4e1aae6c041e4572e7e2281203f969e7649e1480
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Creación de entornos de varias máquinas virtuales y recursos de PaaS con plantillas de Azure Resource Manager

[Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) le permite fácilmente [crear y agregar una máquina virtual a un laboratorio](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-add-vm). Esto funciona bien para crear una máquina virtual a la vez. Sin embargo, si el entorno contiene varias máquinas virtuales, cada una tiene que crearse por separado. Para escenarios como una aplicación web de varios niveles o una granja de SharePoint, se necesita un mecanismo para permitir la creación de varias máquinas virtuales en un solo paso. Mediante el uso de plantillas de Azure Resource Manager, puede definir ahora la infraestructura y la configuración de la solución de Azure e implementar repetidamente varias máquinas virtuales de manera coherente. Esta característica proporciona las siguientes ventajas:

- Las plantillas de Azure Resource Manager se cargan directamente desde el repositorio de control de código fuente (GitHub o Git de Team Services).
- Con la configuración finalizada, los usuarios pueden crear un entorno al seleccionar una plantilla de Azure Resource Manager desde Azure Portal como lo hacen con otros tipos de [bases de datos máquinas virtuales](./devtest-lab-comparing-vm-base-image-types.md).
- Los recursos de PaaS de Azure se pueden aprovisionar en un entorno a partir de una plantilla de Azure Resource Manager además de desde las máquinas virtuales de IaaS.
- El coste de los entornos puede controlarse desde el laboratorio además del de las máquinas virtuales individuales creadas por otros tipos de bases de datos.
- Los recursos de PaaS se crean y se muestran en el seguimiento del costo; sin embargo, el cierre automático de máquina virtual no se aplica a ellos.
- Los usuarios tienen el mismo control de las directivas de máquinas virtuales para los entornos que el que tienen para las máquinas virtuales de un solo laboratorio.

Más información acerca de las muchas [ventajas del uso de plantillas de Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) para implementar, actualizar o eliminar todos los recursos de laboratorio en una sola operación.

> [!NOTE]
> Al usar una plantilla de Resource Manager como punto de partida para crear más máquinas virtuales de laboratorio, existen algunas diferencias entre crear una o varias. En la plantilla de máquina virtual de Azure Resource Manager se explican estas diferencias con mayor detalle.
>
>

## <a name="configure-azure-resource-manager-template-repositories"></a>Configuración de los repositorios de plantillas de Azure Resource Manager

Como uno de los procedimientos recomendados con infraestructura como código y configuración como código, las plantillas de entorno deben administrarse con control de código fuente. Azure DevTest Labs sigue este procedimiento y carga todas las plantillas de Azure Resource Manager directamente desde los repositorios de GitHub o VSTS Git. Como resultado, las plantillas de Resource Manager pueden utilizarse en todo el ciclo de versiones, desde el entorno de prueba hasta el entorno de producción.

Hay un par de reglas que seguir para organizar las plantillas de Azure Resource Manager en un repositorio:

- El archivo de plantilla principal debe llamarse `azuredeploy.json`. 

    ![Archivos de plantilla principal de Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

- Si desea usar valores de parámetro definidos en un archivo de parámetros, este debe llamarse `azuredeploy.parameters.json`.
- Puede usar los parámetros `_artifactsLocation` y `_artifactsLocationSasToken` para construir el valor parametersLink URI y permitir a DevTest Labs administrar automáticamente las plantillas anidadas. Vea [How Azure DevTest Labs makes nested Resource Manager template deployments easier for testing environments](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/) Cómo Azure DevTest Labs facilita la implementación de las plantillas de Resource Manager anidadas para los entornos de prueba) para más información.
- Los metadatos pueden definirse para especificar el nombre para mostrar y la descripción de la plantilla. Estos metadatos deben estar en un archivo denominado `metadata.json`. El archivo de metadatos de ejemplo siguiente muestra cómo especificar el nombre para mostrar y la descripción: 

```json
{
 
"itemDisplayName": "<your template name>",
 
"description": "<description of the template>"
 
}
```

Los siguientes pasos le ayudarán a agregar un repositorio al laboratorio a través de Azure Portal. 

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
1. En la lista de laboratorios, seleccione el laboratorio que desee.   
1. En la hoja del laboratorio, seleccione **Configuration and Policies** (Directivas y configuración).

    ![Directivas y configuración](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Desde la lista de configuración **Configuration and Policies** (Directivas y configuración), seleccione **Repositories** (Repositorios). En la hoja **Repositories** (Repositorios) se enumeran los repositorios que se han agregado al laboratorio. Un repositorio denominado `Public Repo` se genera automáticamente para todos los laboratorios y se conecta al [repositorio de GitHub de DevTest Labs](https://github.com/Azure/azure-devtestlab), que contiene varios artefactos de máquina virtual para su uso.

    ![Repositorio público](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Seleccione **+ Add** (+ Agregar) para agregar el repositorio de plantillas de Azure Resource Manager.
1. Cuando se abra la segunda hoja **Repositories** (Repositorios), escriba la información necesaria como se indica a continuación:
    - **Name** (Nombre): nombre del repositorio que se utiliza en el laboratorio.
    - **URL de clonación de Git**: escriba la URL de clonación HTTPS de GIT de GitHub o Visual Studio Team Services.  
    - **Branch** (Rama): nombre de la rama para acceder a las definiciones de plantilla de Azure Resource Manager. 
    - **Personal access token** (Token de acceso personal): se usa para acceder de forma segura al repositorio. Para obtener el token de Visual Studio Team Services, seleccione **&lt;SuNombre> > Mi perfil > Seguridad > Public access token** (Token de acceso público). Para obtener el token de GitHub, seleccione su avatar seguido de la selección **Configuración > Public access token** (Token de acceso público). 
    - **Rutas de acceso de carpeta**: en uno de los dos campos de entrada, escriba la ruta de acceso de carpeta que comienza con una barra diagonal (/) correspondiente a la URI de clonación de Git de cualquiera de las definiciones de artefacto (primer campo de entrada) o las definiciones de plantilla de Azure Resource Manager.   
    
        ![Repositorio público](./media/devtest-lab-create-environment-from-arm/repo-values.png)

1. Una vez rellenos y validados todos los campos obligatorios, seleccione **Save** (Guardar).

La siguiente sección le guiará a través de la creación de entornos a partir de una plantilla de Azure Resource Manager.

## <a name="create-an-environment-from-an-azure-resource-manager-template"></a>Creación de un entorno a partir de una plantilla de Azure Resource Manager

Una vez configurado un repositorio de plantillas de Azure Resource Manager en el laboratorio, los usuarios de este pueden crear un entorno a través de Azure Portal mediante los pasos siguientes:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
1. En la lista de laboratorios, seleccione el laboratorio que desee.   
1. En la hoja del laboratorio, seleccione **+ Add** (+ Agregar).
1. La hoja **Choose a base** (Elegir base de datos) muestra las imágenes de las bases de datos que se pueden usar con las plantillas de Azure Resource Manager en primer lugar. Seleccione la plantilla de Azure Resource Manager que desee.

    ![Elegir base de datos](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. En la hoja **Add** (Agregar), escriba el valor de **Environment name** (Nombre del entorno). El nombre del entorno es lo que se muestra a los usuarios del laboratorio. Los campos de entrada restantes se definen en la plantilla de Azure Resource Manager. Si se han definido valores predeterminados en la plantilla o el archivo `azuredeploy.parameter.json` está presente, en esos campos de entrada se muestran los valores predeterminados. Para los parámetros de tipo *cadena segura*, puede usar los secretos almacenados en el [almacén secreto personal](https://azure.microsoft.com/en-us/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store) del laboratorio.

    ![Hoja Add (Agregar)](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Hay varios valores de parámetro que, aunque se especifiquen, aparecen vacíos. Por lo tanto, si los usuarios asignan esos valores a parámetros de una plantilla de Azure Resource Manager, DevTest Labs no muestra los valores; en su lugar, muestra los campos de entrada en blanco para que los usuarios del laboratorio escriban un valor al crear el entorno.
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. Seleccione **Add** (Agregar) para crear el entorno. El entorno empieza el aprovisionamiento inmediatamente y el estado se muestra en la lista **My virtual machines** (Mis máquinas virtuales). El laboratorio crea automáticamente un grupo de recursos nuevo para aprovisionar todos los recursos definidos en la plantilla de Azure Resource Manager.
1. Una vez creado el entorno, selecciónelo en la lista **My virtual machines** (Mis máquinas virtuales) para abrir la hoja del grupo de recursos y examinar todos los recursos aprovisionados en el entorno.
    
    ![Lista My virtual machines (Mis máquinas virtuales)](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   También puede expandir el entorno para ver solo la lista de máquinas virtuales aprovisionadas en el entorno.
    
    ![Lista My virtual machines (Mis máquinas virtuales)](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Haga clic en cualquiera de los entornos para ver las acciones disponibles, por ejemplo, se puede aplicar artefactos, adjuntar discos de datos, cambiar el tiempo de apagado automático y mucho más.

    ![Acciones de entorno](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="next-steps"></a>Pasos siguientes
* Una vez creada la máquina virtual, puede conectarse a esta al seleccionar **Connect** (Conectar) en la hoja de la máquina virtual.
* Consulte y administre recursos en un entorno al seleccionarlo de la lista **My virtual machines** (Mis máquinas virtuales) del laboratorio. 
* Explore las [plantillas de Azure Resource Manager de la galería de plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates)
