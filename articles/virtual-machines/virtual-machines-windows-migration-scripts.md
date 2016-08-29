<properties
	pageTitle="Clonación de una máquina virtual clásica en Azure Resource Manager con scripts de PowerShell"
	description="En este artículo se muestra cómo clonar una única máquina virtual clásica en Azure Resource Manager mediante scripts de PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkays"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="singhkay"/>

# Clonación de una máquina virtual clásica en Azure Resource Manager con scripts de PowerShell

En este artículo se muestra cómo usar los scripts situados en [Azure/classic-iaas-resourcemanager-migration](https://github.com/Azure/classic-iaas-resourcemanager-migration) para clonar una **única** máquina virtual clásica en el modelo de implementación de Azure Resource Manager.

>[AZURE.IMPORTANT]La clonación de estos scripts puede provocar tiempo de inactividad en la máquina virtual clásica. Si lo que busca es información sobre la migración compatible con la plataforma, visite
- [Platform supported migration of IaaS resources from Classic to Azure Resource Manager stack](./virtual-machines-windows-migration-classic-resource-manager.md) (Migración compatible con la plataforma de recursos de IaaS de la pila de implementación clásica a Azure Resource Manager)
- [Profundización técnica en la migración compatible con la plataforma de la implementación clásica a Azure Resource Manager](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Migración de recursos de IaaS de la implementación clásica a Resource Manager con Azure PowerShell](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## Obtención de los scripts

>[AZURE.NOTE]Los scripts de PowerShell no se incluyen en el soporte técnico de Microsoft. Por lo tanto, son de código abierto en Github y estamos encantados de aceptar PR para correcciones o escenarios adicionales.

Para obtener los scripts, puede descargar el archivo zip desde el repositorio especificado anteriormente o clonar el repositorio mediante **cualquiera** de los comandos siguientes:

```
git clone https://github.com/Azure/classic-iaas-resourcemanager-migration.git
```

**O bien**

```
git clone git@github.com:Azure/classic-iaas-resourcemanager-migration.git
```

## Importación del módulo de migración

El paso siguiente consiste en importar el módulo en la sesión de PowerShell. Para realizar esto, puede utilizar el comando siguiente:

```
Import-Module .\asm2arm.psd1
```

## Autenticación con las suscripciones del modelo de implementación clásica y de Azure Resource Manager

Para poder clonar la máquina virtual clásica, debe autenticar la sesión de PowerShell en las pilas del modelo de implementación clásica y de Azure Resource Manager. Para ello, puede usar los siguientes cmdlets:

```
Add-AzureAccount
Login-AzureRmAccount
```

>[AZURE.IMPORTANT]Asegúrese de que las suscripciones se seleccionan mediante `Select-AzureSubscription` en el caso del modelo de implementación clásica o usando Set-AzureRmContext si es para Azure Resource Manager.

## Uso de scripts

### Los cmdlets

Al importar el módulo se agregarán los dos cmdlets siguientes a su sesión.

```
Add-AzureSMVmToRM
New-AzureSmToRMDeployment
```

Estos cmdlets realizan las funciones que se especifican a continuación.

#### Add-AzureSMVmToRM
- Genera un conjunto de plantillas de Azure Resource Manager y scripts imperativos de PowerShell (para copiar los blobs de disco y si la máquina virtual tiene extensiones de agente de máquina virtual), según una máquina virtual determinada.

>[AZURE.IMPORTANT]Si se especifica el modificador `-Deploy`, este cmdlet llama al cmdlet `New-AzureSmToRMDeployment` para implementar las plantillas de Azure Resource Manager y los scripts imperativos de PowerShell anteriormente generados.

#### New-AzureSmToRMDeployment
- Implementa las plantillas y los scripts imperativos de PowerShell generados por el cmdlet `Add-AzureSMVmToRM` para iniciar la migración.

### Identificación de la máquina virtual clásica que se clonará

Esto se puede realizar de dos maneras: o bien se almacena el estado de la máquina virtual clásica en una variable y se pasa al cmdlet `Add-AzureSMVmToRM`, o se usan directamente los parámetros `ServiceName` y `Name` de la máquina virtual. Después de identificar la máquina virtual clásica, puede usar el cmdlet `Add-AzureSMVmToRM` para clonarla.

```
$vm = Get-AzureVm -ServiceName acloudservice -Name atestvm
Add-AzureSMVmToRM -VM $vm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy
```

**O bien**

```
Add-AzureSMVmToRM -ServiceName acloudservice -Name atestvm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy
```

>[AZURE.IMPORTANT]En los ejemplos anteriores, como usábamos el modificador `-Deploy`, el uso del cmdlet `New-AzureSmToRMDeployment` no era necesario.

## ¿Cómo funciona la migración con estos scripts?

Los cmdlets siguen los pasos para clonar la máquina virtual clásica y generan recursos como tabas hash de PowerShell personalizadas para los proveedores de recursos de almacenamiento, redes y procesos. Esas tablas hash, que representan los recursos anexados a una matriz, se convierten más tarde en una plantilla por medio de la serialización con JSON, y se escriben en un archivo.

La plantilla crea archivos en función de la existencia de extensiones de agente de máquina virtual clásica y del valor de la opción DiskAction. Estos archivos se colocan en el directorio que ha especificado el parámetro OutputFileFolder. Los archivos son:

1. `<ServiceName>-<VMName>-setup<optional timestamp>.json`: este archivo representa los recursos que son necesarios para estar preparado antes de clonar la máquina virtual clásica, y posiblemente será el mismo para todas las sucesivas máquinas virtuales (no mantenemos el estado entre las posteriores ejecuciones, pero como una cuenta de almacenamiento debe aprovisionarse para poder realizar una operación de copia de blob, lo que se hace obligatoriamente, era lógico agrupar recursos parecidos en uno).

2.  `<ServiceName>-<VMName>-deploy<optional timestamp>.json`: contiene la plantilla de la máquina virtual de Resource Manager.
3.  `<ServiceName>-<VMName>-parameters<optional timestamp>.json`: contiene los parámetros reales pasados a las plantillas.
4.  `<ServiceName>-<VMName>-setextensions<optional timestamp>.json`: un conjunto de cmdlets de PowerShell que se ejecutan para configurar las extensiones de agente de máquina virtual.
4.  `<ServiceName>-<VMName>-copydisks<optional timestamp>.json`: un conjunto de cmdlets de PowerShell que se ejecutan para copiar blobs de disco, si se especifica la opción CopyDisks.

Si se establece la marca -Deploy, después de generar los archivos, el cmdlet implementa entonces la plantilla <NombreDeServicio>-<NombreDeMáquinaVirtual>-setup.json, copia los blobs de disco de máquina virtual de origen si el parámetro DiskAction está definido como CopyDisks y luego implementa la plantilla <NombreDeServicio>-<NombreDeMáquinaVirtual>-deploy.json, mediante el archivo <NombreDeServicio>-<NombreDeMáquinaVirtual>-parameters.json para los parámetros. Cuando haya finalizado la implementación de la máquina virtual, si hay un script imperativo (para las extensiones de agente de máquina virtual), o un script para copiar los discos, estos se ejecutan.

### Detalles de la red
La intención del cmdlet no es clonar la configuración de la red clásica en Resource Manager. Utiliza instalaciones de red de la manera más conveniente para clonar la propia máquina virtual. Esto es lo que sucede en diferentes condiciones:

1.  Ninguna red virtual en el grupo de recursos de destino.
    - La máquina virtual de origen no está en una subred: se crea una red virtual predeterminada con 10.0.0.0/16 junto con una subred, con el espacio de direcciones 10.0.0.0/22.
    - La máquina virtual de origen se encuentra en una subred: se detecta la red virtual en la que está la máquina virtual y se copia la especificación de la red virtual, junto con las subredes.
2.  El grupo de recursos de destino tiene una red virtual con un nombre `<VM virtual network>arm` (se anexa la cadena 'arm').
    - Si la red virtual tiene una subred con el mismo nombre y espacio de direcciones, úsela.
    - Si no se encuentra una subred adecuada, busque un bloque de direcciones de las subredes existentes con máscara de 22 bits y úselo.

## Clonación frente a migración de la plataforma

Existen algunas diferencias entre el método de clonación actual y la migración compatible con la plataforma.

### Clonación


| Ventajas | Desventajas |
|--------------------------------------------------------|:----------------------------------------------------------------:|
| Clonación de una máquina virtual dentro de un servicio en la nube. | Las máquinas virtuales experimentan un tiempo de inactividad ya que tienen que estar apagadas. |
| Clonación de cualquier máquina virtual fuera o dentro de una red virtual. | Retrasos largos en escenarios donde se requiere la copia de blobs de disco. |
| | Cambio en la configuración de red de la máquina virtual: IP internas e IP virtuales. |


### Migración de plataforma


| Ventajas | Desventajas |
|----------------------------------|:-------------------------------------------:|
| La mayoría de las configuraciones de máquina virtual dentro de una red Virtual no producirán tiempo de inactividad. | Todas las máquinas virtuales de un servicio en la nube o de la red virtual con todos los recursos implementados en ella se deben migrar juntos. |
 
 
## Escenarios no admitidos

**Los siguientes escenarios escapan del ámbito de los scripts de clonación:**

 1. Detención de una máquina virtual en ejecución
 2. Cambio de los discos de datos
 3. Clonación de máquinas virtuales en ejecución
 4. Clonación de varias máquinas virtuales en un escenario complejo de forma automática
 5. Clonación de toda la configuración de red ASM
 6. Creación de máquinas virtuales de carga equilibrada Se supone que esta es una configuración que el experto de Azure debe controlar explícitamente.
 
 
## Configuraciones probadas

El cmdlet _Add-AzureSMVmToRM_ se validó usando los siguientes casos de prueba:

| programas | Description |
|:---|:---|
| 1 | Máquina virtual de Windows con un disco de sistema operativo existente |
| 2 | Máquina virtual de Linux con un disco de sistema operativo existente |
| 3 | Máquina virtual de Windows con discos de datos y sistema operativo existentes |
| 4 | Máquina virtual de Linux con discos de datos y sistema operativo existentes |
| 5 | Máquina virtual de Windows con un nuevo disco de SO con el que se busca correspondencia desde la Galería de imágenes |
| 6 | Máquina virtual de Linux con un nuevo disco de SO con el que se busca correspondencia desde la Galería de imágenes |
| 7 | Máquina virtual de Windows con un nuevo disco de SO y discos de datos vacíos |
| 8 | Máquina virtual de Linux con un nuevo disco de SO y discos de datos vacíos |
| 9 | Máquina Virtual de Windows con puntos de conexión públicos |
| 10 | Máquina virtual de Linux con puntos de conexión públicos |
| 11 | Máquina virtual de Windows con un certificado de WinRM |
| 12 | Máquina virtual de Windows en una red virtual y una subred |
| 13 | Máquina virtual de Linux en una red virtual y una subred |
| 14 | Máquina virtual de Windows con extensiones personalizadas |
| 15 | Máquina virtual de Windows en un conjunto de disponibilidad |
| 16 | Máquina virtual de Windows en un conjunto de disponibilidad, con varios discos de datos, puntos de conexión públicos, en una red virtual y una subred, y con extensiones personalizadas |
| 17 | Máquina virtual de Linux en un conjunto de disponibilidad, con varios discos de datos, puntos de conexión, en una red virtual y una subred, y con extensiones personalizadas |

## Notas
1. Si se clonan varias máquinas virtuales, una después de otra, con cortos intervalos entre ellas, podrían producirse conflictos de nombres DNS en las direcciones IP públicas debido al tiempo de actualización de la caché DNS.

<!---HONumber=AcomDC_0817_2016-->