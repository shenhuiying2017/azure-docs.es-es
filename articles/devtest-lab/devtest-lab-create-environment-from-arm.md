---
title: "Creación de entornos de varias máquinas virtuales y recursos de PaaS con plantillas de Azure Resource Manager | Microsoft Docs"
description: "Obtenga información acerca de cómo crear entornos de varias máquinas virtuales y recursos de PaaS en Azure DevTest Labs a partir de una plantilla de Azure Resource Manager"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: v-craic
ms.openlocfilehash: 55a6c5cd5a0544b297bb68841c5ff0314f48dcc9
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Creación de entornos de varias máquinas virtuales y recursos de PaaS con plantillas de Azure Resource Manager

[Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) le permite fácilmente [crear y agregar una máquina virtual a un laboratorio](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm). Esto funciona bien para crear una máquina virtual a la vez. Sin embargo, si el entorno contiene varias máquinas virtuales, cada una tiene que crearse por separado. Para escenarios como una aplicación web de varios niveles o una granja de SharePoint, se necesita un mecanismo para permitir la creación de varias máquinas virtuales en un solo paso. Mediante el uso de plantillas de Azure Resource Manager, puede definir ahora la infraestructura y la configuración de la solución de Azure e implementar repetidamente varias máquinas virtuales de manera coherente. Esta característica proporciona las siguientes ventajas:

- Las plantillas de Azure Resource Manager se cargan directamente desde el repositorio de control de código fuente (GitHub o Git de Team Services).
- Con la configuración finalizada, los usuarios pueden crear un entorno al seleccionar una plantilla de Azure Resource Manager desde Azure Portal como lo hacen con otros tipos de [bases de máquinas virtuales](./devtest-lab-comparing-vm-base-image-types.md).
- Los recursos de PaaS de Azure se pueden aprovisionar en un entorno a partir de una plantilla de Azure Resource Manager además de desde las máquinas virtuales de IaaS.
- El coste de los entornos puede controlarse desde el laboratorio además del de las máquinas virtuales individuales creadas por otros tipos de bases de datos.
- Los recursos de PaaS se crean y se muestran en el seguimiento del costo; sin embargo, el cierre automático de máquina virtual no se aplica a ellos.
- Los usuarios tienen el mismo control de las directivas de máquinas virtuales para los entornos que el que tienen para las máquinas virtuales de un solo laboratorio.

Más información acerca de las muchas [ventajas del uso de plantillas de Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) para implementar, actualizar o eliminar todos los recursos de laboratorio en una sola operación.

> [!NOTE]
> Al usar una plantilla de Resource Manager como punto de partida para crear más máquinas virtuales de laboratorio, existen algunas diferencias entre crear una o varias. En el artículo sobre el [uso de una plantilla de máquina virtual de Azure Resource Manager](devtest-lab-use-resource-manager-template.md) se explican estas diferencias con mayor detalle.
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

1. Inicie sesión en el [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
1. En la lista de laboratorios, seleccione el laboratorio que desee.   
1. En el panel **Introducción** del laboratorio, seleccione **Configuración y directivas**.

    ![Directivas y configuración](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Desde la lista de configuración **Configuration and Policies** (Directivas y configuración), seleccione **Repositories** (Repositorios). En el panel **Repositorios** se enumeran los repositorios que se han agregado al laboratorio. Un repositorio denominado `Public Repo` se genera automáticamente para todos los laboratorios y se conecta al [repositorio de GitHub de DevTest Labs](https://github.com/Azure/azure-devtestlab), que contiene varios artefactos de máquina virtual para su uso.

    ![Repositorio público](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Seleccione **+ Add** (+ Agregar) para agregar el repositorio de plantillas de Azure Resource Manager.
1. Cuando se abra el segundo panel **Repositorios**, escriba la información necesaria como se indica a continuación:
    - **Name** (Nombre): nombre del repositorio que se utiliza en el laboratorio.
    - **URL de clonación de Git**: escriba la URL de clonación HTTPS de GIT de GitHub o Visual Studio Team Services.  
    - **Branch** (Rama): nombre de la rama para acceder a las definiciones de plantilla de Azure Resource Manager. 
    - **Personal access token** (Token de acceso personal): se usa para acceder de forma segura al repositorio. Para obtener el token de Visual Studio Team Services, seleccione **&lt;SuNombre> > Mi perfil > Seguridad > Public access token** (Token de acceso público). Para obtener el token de GitHub, seleccione su avatar seguido de la selección **Configuración > Public access token** (Token de acceso público). 
    - **Rutas de acceso de carpeta**: en uno de los dos campos de entrada, escriba la ruta de acceso de carpeta que comienza con una barra diagonal (/) correspondiente a la URI de clonación de Git de cualquiera de las definiciones de artefacto (primer campo de entrada) o las definiciones de plantilla de Azure Resource Manager.   
    
        ![Repositorio público](./media/devtest-lab-create-environment-from-arm/repo-values.png)


1. Una vez rellenos y validados todos los campos obligatorios, seleccione **Save** (Guardar).

La siguiente sección le guiará a través de la creación de entornos a partir de una plantilla de Azure Resource Manager.

## <a name="create-an-environment-from-a-resource-manager-template-using-the-azure-portal"></a>Creación de un entorno a partir de una plantilla de Resource Manager con Azure Portal

Una vez configurado un repositorio de plantillas de Azure Resource Manager en el laboratorio, los usuarios de este pueden crear un entorno a través de Azure Portal mediante los pasos siguientes:

1. Inicie sesión en el [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
1. En la lista de laboratorios, seleccione el laboratorio que desee.   
1. En el panel del laboratorio, seleccione **+ Agregar**.
1. En el panel **Elegir una base** se muestran las imágenes de las bases de datos que se pueden usar con las plantillas de Azure Resource Manager en primer lugar. Seleccione la plantilla de Azure Resource Manager que desee.

    ![Elegir base de datos](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. En el panel **Agregar**, escriba el valor de **Nombre del entorno**. El nombre del entorno es lo que se muestra a los usuarios del laboratorio. Los campos de entrada restantes se definen en la plantilla de Azure Resource Manager. Si se han definido valores predeterminados en la plantilla o el archivo `azuredeploy.parameter.json` está presente, en esos campos de entrada se muestran los valores predeterminados. Para los parámetros de tipo *cadena segura*, puede usar los secretos almacenados en el [almacén secreto personal](https://azure.microsoft.com/en-us/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store) del laboratorio.

    ![Agregar panel](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Hay varios valores de parámetro que, aunque se especifiquen, aparecen vacíos. Por lo tanto, si los usuarios asignan dichos valores a parámetros de una plantilla de Azure Resource Manager, DevTest Labs no muestra los valores. En su lugar, se muestran los campos de entrada en blanco donde los usuarios del laboratorio deben especificar un valor al crear el entorno.
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. Seleccione **Add** (Agregar) para crear el entorno. El entorno empieza el aprovisionamiento inmediatamente y el estado se muestra en la lista **My virtual machines** (Mis máquinas virtuales). El laboratorio crea automáticamente un grupo de recursos nuevo para aprovisionar todos los recursos definidos en la plantilla de Azure Resource Manager.
1. Una vez creado el entorno, selecciónelo en la lista **Mis máquinas virtuales** para abrir el panel del grupo de recursos y examinar todos los recursos aprovisionados en el entorno.
    
    ![Lista My virtual machines (Mis máquinas virtuales)](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   También puede expandir el entorno para ver solo la lista de máquinas virtuales aprovisionadas en el entorno.
    
    ![Lista My virtual machines (Mis máquinas virtuales)](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Haga clic en cualquiera de los entornos para ver las acciones disponibles, por ejemplo, se puede aplicar artefactos, adjuntar discos de datos, cambiar el tiempo de apagado automático y mucho más.

    ![Acciones de entorno](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="deploy-a-resource-manager-template-to-create-a-vm"></a>Implementación de una plantilla de Resource Manager para crear una máquina virtual
Después de guardar una plantilla de Resource Manager y personalizarla según sus necesidades, puede utilizarla para automatizar la creación de una máquina virtual. 
- En [Implementación de recursos con plantillas de Resource Manager y Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) se describe cómo usar PowerShell de Azure con plantillas de Resource Manager para implementar recursos en Azure. 
- En [Implementación de recursos con plantillas de Resource Manager y CLI de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) se describe cómo usar CLI de Azure con plantillas de Resource Manager para implementar recursos en Azure.

> [!NOTE]
> Solo un usuario con permisos de propietario de laboratorio puede crear máquinas virtuales desde una plantilla de Resource Manager con Azure PowerShell. Si desea automatizar la creación de máquinas virtuales mediante una plantilla de Resource Manager y solo tiene permisos de usuario, puede usar el comando [ **az lab vm create** de la CLI ](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create).

### <a name="general-limitations"></a>Limitaciones generales 

Tenga en cuenta estas limitaciones cuando use una plantilla de Resource Manager en DevTest Labs:

- Cualquier plantilla de Resource Manager que cree no puede hacer referencia a los recursos existentes; solo puede hacer referencia a los nuevos recursos. Además, si tiene una plantilla de Resource Manager existente que normalmente implementa fuera de DevTest Labs y contiene referencias a los recursos existentes, no puede usarla en el laboratorio.

   La única excepción a eso es que **puede** hacer referencia a una red virtual existente. 

- No se pueden crear fórmulas a partir de las máquinas virtuales de laboratorio que se crearon con una plantilla de Resource Manager. 

- No se pueden crear imágenes personalizadas a partir de las máquinas virtuales de laboratorio que se crearon con una plantilla de Resource Manager. 

- La mayoría de las directivas no se evalúan al implementar plantillas de Resource Manager.

   Por ejemplo, es posible que tenga una directiva de laboratorio que especifique que un usuario solo puede crear cinco máquinas virtuales. Sin embargo, si el usuario implementa una plantilla de Resource Manager que crea docenas de máquinas virtuales, esto sí que se permite. Entre las directivas que no se evalúan se incluyen:

   - Número de máquinas virtuales por usuario
   - Número de máquinas virtuales premium por usuario de laboratorio
   - Número de discos premium por usuario de laboratorio


### <a name="configure-environment-resource-group-access-rights-for-lab-users"></a>Configuración de los derechos de acceso a grupos de recursos de entorno para los usuarios del laboratorio

Los usuarios del laboratorio pueden implementar una plantilla de Resource Manager. Sin embargo, de forma predeterminada, tienen derechos de acceso de lectura, lo que significa que no pueden cambiar los recursos de un grupo de recursos del entorno. Por ejemplo, no pueden detener ni iniciar sus recursos.

Siga estos pasos para conceder derechos de acceso de colaborador a los usuarios del laboratorio. A continuación, cuando implementen una plantilla de Resource Manager, podrán editar los recursos en ese entorno. 


1. En el panel **Introducción** del laboratorio, seleccione **Configuración y directivas**.
1. Seleccione **Configuración del laboratorio**.
1. En el panel Configuración de laboratorio, seleccione **Colaborador** para conceder permisos de escritura a los usuarios del laboratorio.

    ![Configuración de los derechos de acceso de los usuarios del laboratorio](./media/devtest-lab-create-environment-from-arm/configure-access-rights.png)

1. Seleccione **Guardar**.

## <a name="next-steps"></a>pasos siguientes
* Una vez creada la máquina virtual, puede conectarse a esta al seleccionar **Conectar** en el panel de administración de la máquina virtual.
* Consulte y administre recursos en un entorno al seleccionarlo de la lista **My virtual machines** (Mis máquinas virtuales) del laboratorio. 
* Explore las [plantillas de Azure Resource Manager de la galería de plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates).
