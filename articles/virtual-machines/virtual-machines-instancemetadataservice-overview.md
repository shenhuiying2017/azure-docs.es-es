---
title: "Información general sobre el servicio de metadatos de instancia de Azure | Microsoft Docs"
description: "Interfaz RESTful para obtener información sobre proceso, red y próximos eventos de mantenimiento de la máquina virtual."
services: virtual-machines-windows, virtual-machines-linux,virtual-machines-scale-sets, cloud-services
documentationcenter: 
author: harijay
manager: timlt
editor: 
tags: 
ms.service: azure-instancemetadataservice
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: harijay
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: b5c0268e1a0ebfcb33781678a367090cad865907
ms.lasthandoff: 04/11/2017

---

# <a name="azures-instance-metadata-service---preview"></a>Servicio de metadatos de instancia de Azure: versión preliminar

> [!NOTE] 
> Las versiones preliminares están a su disposición con la condición de que acepte los términos de uso. Para más información, consulte [Términos de uso complementarios de las Vistas Previas de Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>

El servicio de metadatos de instancia proporciona información relacionada con las instancias de máquina virtual en ejecución. Esta información se puede usar para administrar y configurar las instancias en Azure. El servicio de metadatos de instancia de Azure es un punto de conexión de RESTful disponible para todas las VM de IaaS creadas a través del nuevo [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN). El punto de conexión está disponible en una dirección IP no enrutable conocida (*169.254.169.254*) a la que se puede acceder solo desde dentro de la máquina virtual. Este servicio proporcionaría datos importantes relacionados con la instancia de máquina virtual, su configuración de red y próximos eventos de mantenimiento. Para más información, consulte las [categorías de metadatos](#instance-metadata-data-categories).

### <a name="important-information"></a>Información importante
Este servicio actualmente está en **versión preliminar** y hospeda información relacionada con la instancia de máquina virtual y próximos eventos de mantenimiento. El servicio sigue recibiendo actualizaciones y esta página refleja las [categorías de datos](#instance-metadata-data-categories) disponibles.


## <a name="service-availability"></a>Disponibilidad del servicio
La versión preliminar actual está disponible en la región **Centro occidental de EE. UU.** de Azure. La tabla siguiente está actualizada con las regiones en las que la versión preliminar del servicio empieza a estar disponible.
Si desea probar el servicio de metadatos de instancia, cree una máquina virtual en [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN) o [Azure Portal](http://portal.azure.com) y siga los ejemplos de la sección de ejemplos para obtener acceso al servicio de metadatos. 

Regiones donde está disponible la versión preliminar del servicio de metadatos de instancia|
------------------------------------------------------------|
Centro occidental de EE.UU. |



## <a name="retrieving-instance-metadata"></a>Recuperación de metadatos de instancia 

Los metadatos de instancia están disponibles para ejecutar máquinas virtuales creadas o administradas mediante [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN). Para tener acceso a todas las categorías de datos de una instancia de máquina virtual, use el identificador URI siguiente

```
 curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

La salida predeterminada de todos los metadatos de instancia está en formato json (tipo de contenido Application/json)

## <a name="usage-examples"></a>Ejemplos de uso
A continuación, hay un conjunto de ejemplos y semánticas de uso para el servicio de metadatos de instancia

### <a name="versioning"></a>Control de versiones 
El servicio de metadatos de instancia tiene versiones. Las versiones son obligatorias y la versión preliminar actual es 2017-03-01.


```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

A medida que agreguemos versiones más recientes, todavía se podrá acceder a las versiones anteriores por motivos de compatibilidad si los scripts tienen dependencias en formatos de datos específicos. Nota: La versión preliminar actual puede que no esté disponible una vez que el servicio esté disponible con carácter general.


### <a name="data-output"></a>Salida de datos
De manera predeterminada, el servicio de metadatos de instancia devuelve los datos en formato json (tipo de contenido=application/json). Elementos de nodo distintos pueden devolver datos en un formato predeterminado diferente según corresponda; la tabla siguiente es una referencia rápida para los formatos de datos 

Elemento | Formato predeterminado de datos | Otros formatos
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | Ninguna

Para formato de texto, use format=text en la dirección URL de solicitud, por ejemplo 

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01&format=text"
```
### <a name="security"></a>Seguridad
El punto de conexión de metadatos de instancia solo es accesible desde la instancia de máquina virtual en ejecución en una dirección IP 169.254.169.254 no enrutable. Además, el servicio de metadatos rechaza cualquier solicitud que tenga el encabezado **X-Forwarded-For**. También es necesario que las solicitudes que incluyan el encabezado **Metadata:true** se envíen para garantizar que la solicitud actual esté dirigida directamente y no como parte de un redireccionamiento accidental. 
### <a name="error"></a>Error
Si no se encuentra un elemento de datos o hay solicitudes con formato incorrecto, el servicio de metadatos de instancia devuelve un error HTTP estándar. Los siguientes son algunos ejemplos de códigos de retorno 

Código de retorno HTTP | Motivo
----------------|-------
200 | OK
400 | Solicitud incorrecta, falta el encabezado, se transmite el valor -H Metadata:true
404 | No se encuentra, el elemento solicitado no existe 
405 | El método no es compatible 
429 | Demasiadas solicitudes, actualmente solo se admite un máximo de 5 consultas por segundo

### <a name="examples"></a>Ejemplos
#### <a name="retrieving-the-network-information"></a>Recuperación de la información de red 

**Solicitud**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-03-01"

```
**Respuesta**
> [!NOTE] 
> La respuesta es una cadena json. La siguiente salida tiene el formato json con utilidad como [jq](https://stedolan.github.io/jq/).
>

```
{
  "interface": [
    {
      "ipv4": {
        "ipaddress": [
          {
            "ipaddress": "10.0.0.4",
            "publicip": "<>.<>.<>.<>"
          }
        ],
        "subnet": [
          {
            "address": "10.0.0.0",
            "dnsservers": [
              {
                "ipaddress": "10.0.0.2"
              },
              {
                "ipaddress": "10.0.0.3"
              }
            ],
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipaddress": []
      },
      "mac": "000D3A00FA89"
    }
  ]
}
```

#### <a name="retrieving-public-ip-address"></a>Recuperación de dirección IP pública

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipaddress/0/publicip?api-version=2017-03-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Recuperación de todos los metadatos para una instancia

**Solicitud**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```
**Respuesta**
> [!NOTE]
> La respuesta es una cadena json. La siguiente salida tiene el formato json con utilidad como [jq](https://stedolan.github.io/jq/).
>

```
{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipaddress": [
            {
              "ipaddress": "10.0.0.4",
              "publicip": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.0.0",
              "dnsservers": [
                {
                  "ipaddress": "10.0.0.2"
                },
                {
                  "ipaddress": "10.0.0.3"
                }
              ],
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipaddress": []
        },
        "mac": "000D3A00FA89"
      }
    ]
  }
}

```
#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Recuperación de metadatos en una máquina virtual Windows

Los metadatos de instancia se pueden recuperar en Windows a través de la utilidad curl de PowerShell. En un símbolo del sistema de PowerShell, ejecute los comandos siguientes: 

**Solicitud**
```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-03-01 | select -ExpandProperty Content
```
**Respuesta**
>[!NOTE]
> La respuesta es una cadena json. La salida siguiente tiene el formato json con la utilidad ConvertTo-Json de PowerShell.
> 

```
{
    "compute":  {
                    "location":  "CentralUSEUAP",
                    "name":  "SQLTest",
                    "offer":  "SQL2016SP1-WS2016",
                    "osType":  "Windows",
                    "platformFaultDomain":  "0",
                    "platformUpdateDomain":  "0",
                    "publisher":  "MicrosoftSQLServer",
                    "sku":  "Enterprise",
                    "version":  "13.0.400110",
                    "vmId":  "453945c8-3923-4366-b2d3-ea4c80e9b70e",
                    "vmSize":  "Standard_DS2"
                },
    "network":  {
                    "interface":  [
                                      "@{ipv4=; ipv6=; mac=002248020E1E}"
                                  ]
                }
}
```


## <a name="instance-metadata-data-categories"></a>Categorías de datos de metadatos de instancia
En la tabla siguiente se muestra una lista de todas las categorías de datos disponibles a través de metadatos de instancia

Datos | Descripción
-----|------------
location | La región de Azure donde se ejecuta la VM 
name | Nombre de la máquina virtual 
offer | Información de oferta de la imagen de VM. Estos valores solo están presentes para las imágenes implementadas desde la galería de imágenes de Azure 
publisher | Publicador de la imagen de VM
sku | SKU específica de la imagen de VM  
versión | Versión de la imagen de VM 
osType | Linux o Windows 
platformUpdateDomain |  [Dominio de actualización](virtual-machines-windows-manage-availability.md) en que se ejecuta la VM. 
platformFaultDomain | [Dominio de error](virtual-machines-windows-manage-availability.md) en que se ejecuta la VM.
vmId | Identificador único de la máquina virtual. Más información [aquí](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
vmSize | [Tamaño de VM](virtual-machines-windows-sizes.md)
ipv4/ipaddress | Dirección IP local de la VM 
ipv4/publicip | Dirección IP pública de la instancia 
subnet/address | Dirección de la subred 
subnet/dnsservers/ipaddress1 | Servidor DNS principal
subnet/dnsservers/ipaddress2 | Servidor DNS secundario
subnet/prefix | Prefijo de la subred, ejemplo, 24
ipv6/ipaddress | Dirección IPv6 de la máquina virtual
mac | Dirección de MAC de la VM 
scheduledevents | ver [scheduledevents](virtual-machines-scheduled-events.md)



## <a name="example-scenarios-for-usage"></a>Escenarios de ejemplo de uso  

### <a name="tracking-vm-running-on-azure"></a>Seguimiento de una máquina virtual que se ejecuta en Azure 

Como proveedor de servicios, es posible que necesite hacer seguimiento de la cantidad de máquinas virtuales que ejecutan su software o que tenga agentes que deban hacer seguimiento de la unicidad de la máquina virtual. Para poder obtener un identificador único para una VM, use el campo vmId desde el servicio de metadatos de instancia 

**Solicitud**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-03-01&format=text"
```
**Respuesta**
```
5c08b38e-4d57-4c23-ac45-aca61037f084

```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Ubicación de contenedores, dominio de error/actualización basado en particiones de datos 

Para ciertos escenarios donde la ubicación de las distintas réplicas es de importancia primordial. Por ejemplo, [la ubicación de réplicas de HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps), o para la ubicación de contenedor a través de un [orquestador](https://kubernetes.io/docs/user-guide/node-selection/) se debe saber en que platformFaultDomain y platformUpdateDomain se ejecuta la máquina virtual. Puede consultar directamente este punto de datos a través de los metadatos de instancia

**Solicitud**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-03-01&format=text" 
```
**Respuesta**
```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Obtención de más información sobre la VM durante el caso de soporte técnico 

Como proveedor de servicios, es posible que reciba una llamada de soporte técnico en la que le gustaría tener más información sobre la máquina virtual. Pedirle al cliente que comparta los metadatos del equipo puede proporcionar información básica para que el profesional de soporte técnico conozca la variante de máquina virtual en Azure. 

**Solicitud**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-03-01"
```
**Respuesta**
> [!NOTE] 
> La respuesta es una cadena json. La siguiente salida tiene el formato json con utilidad como [jq](https://stedolan.github.io/jq/).
>

```
{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }

}
```

## <a name="faq"></a>P+F
1. Recibo un error 400 que indica que la solicitud es incorrecta y no se especifica el encabezado de metadatos requerido. ¿Qué significa?
   * El servicio de metadatos de instancia requiere que el encabezado Metadata:true se transmita en la solicitud. Transmitir el encabezado en la llamada de REST permite acceder al servicio de metadatos de instancia. 
2. ¿Por qué no recibo información de proceso de mi máquina virtual?
   * El servicio de metadatos de instancia actualmente solo admite instancias creadas en Azure Resource Manager; en el futuro podemos agregar compatibilidad con máquinas virtuales de Cloud Services. 
3. Creé mi máquina virtual mediante Azure Resource Manager hace un tiempo. ¿Por qué no veo la información de metadatos de proceso?
   * En el caso de las máquinas virtuales que se crearon después de septiembre de 2016, agregue una [etiqueta](../azure-resource-manager/resource-group-using-tags.md) para empezar a ver los metadatos de proceso. En el caso de máquinas virtuales anteriores (creadas antes de septiembre de 2016), agregue o quite extensiones o discos de datos a la máquina virtual para actualizar los metadatos.
4. ¿Por qué recibo un error 500 de error interno del servidor?
   * Actualmente, la versión preliminar de metadatos de instancia solo está disponible en la región Centro occidental de EE. UU. Implemente las máquinas virtuales en las regiones compatibles.  
4. ¿Dónde puedo compartir preguntas o comentarios adicionales?
   * Envíe comentarios en feedback.azure.com.
    
## <a name="next-steps"></a>Pasos siguientes

Más información sobre [scheduledevents] (virtual-machines-scheduled-events.md)

