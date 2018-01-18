---
title: Servicio de metadatos de instancia de Azure | Microsoft Docs
description: "Interfaz RESTful para obtener información sobre proceso, red y próximos eventos de mantenimiento de la máquina virtual Linux."
services: virtual-machines-linux
documentationcenter: 
author: harijayms
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: harijayms
ms.openlocfilehash: 40b684fe5681123f3c32d3984b2725f97b427f1b
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="azure-instance-metadata-service"></a>Servicio de metadatos de instancia de Azure


El servicio de metadatos de instancia de Azure proporciona información sobre instancias de máquina virtual en ejecución que pueden usarse para administrar y configurar las máquinas virtuales.
Esto incluye información como SKU, configuración de red y eventos de mantenimiento próximos. Para más información sobre el tipo de información está disponible, vea las [categorías de metadatos](#instance-metadata-data-categories).

El servicio de metadatos de instancia de Azure es un punto de conexión REST al que pueden tener acceso todas las máquinas virtuales IaaS creadas a través de [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). El punto de conexión está disponible en una dirección IP no enrutable conocida (`169.254.169.254`) a la que solo se puede tener acceso desde dentro de la máquina virtual.

> [!IMPORTANT]
> Este servicio está **disponible con carácter general** en todas las regiones de Azure.  Recibe actualizaciones periódicas para exponer información nueva sobre instancias de máquina virtual. En esta página se reflejan las [categorías de datos](#instance-metadata-data-categories) actualizadas disponibles.

## <a name="service-availability"></a>Disponibilidad del servicio
El servicio está disponible con carácter general en todas las regiones de Azure. Puede que no todas las versiones de API estén disponibles en todas las regiones de Azure.

Regiones                                        | ¿Disponibilidad?                                 | Versiones compatibles
-----------------------------------------------|-----------------------------------------------|-----------------
[Todas las regiones globales de Azure disponibles con carácter general](https://azure.microsoft.com/regions/)     | Disponibilidad general   | 2017-04-02, 2017-08-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Disponibilidad general | 2017-04-02
[Azure en China](https://www.azure.cn/)                                                           | Disponibilidad general | 2017-04-02
[Azure Alemania](https://azure.microsoft.com/overview/clouds/germany/)                    | Disponibilidad general | 2017-04-02

Esta tabla se actualiza cuando hay actualizaciones del servicio o hay nuevas versiones compatibles disponibles

Para probar el servicio de metadatos de instancia, cree una máquina virtual desde [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) o [Azure Portal](http://portal.azure.com) en las regiones anteriores, y siga los ejemplos siguientes.

## <a name="usage"></a>Uso

### <a name="versioning"></a>Control de versiones
El servicio de metadatos de instancia tiene versiones. Las versiones son obligatorias y la versión actual de Global Azure es la `2017-08-01`. Las versiones compatibles actuales son (2017-04-02, 2017-08-01)

> [!NOTE] 
> Las versiones preliminares de eventos programados compatibles {más reciente} como la versión de api. Este formato ya no es compatible y dejará de utilizarse en el futuro.

A medida que agreguemos versiones más recientes, todavía se podrá acceder a las versiones anteriores por motivos de compatibilidad si los scripts tienen dependencias en formatos de datos específicos. Pero tenga en cuenta que es posible que la versión preliminar anterior (2017-03-01) no esté disponible una vez que el servicio esté disponible con carácter general.

### <a name="using-headers"></a>Uso de encabezados
Al consultar el servicio de metadatos de instancia, debe proporcionar el encabezado `Metadata: true` para asegurarse de que la solicitud no se ha redirigido de manera involuntaria.

### <a name="retrieving-metadata"></a>Recuperar metadatos

Los metadatos de instancia están disponibles para ejecutar máquinas virtuales creadas o administradas mediante [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Para tener acceso a todas las categorías de datos de una instancia de máquina virtual, use la solicitud siguiente:

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

> [!NOTE] 
> Todas las consultas de metadatos de instancia distinguen mayúsculas de minúsculas.

### <a name="data-output"></a>Salida de datos
De forma predeterminada, el servicio de metadatos de instancia devuelve datos en formato JSON (`Content-Type: application/json`). Pero las distintas API devuelven los datos en formatos diferentes si se solicita.
En la tabla siguiente se muestra una referencia de otros formatos de datos que las API pueden admitir.

API | Formato predeterminado de datos | Otros formatos
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | Ninguna

Para obtener acceso a un formato de respuesta que no sea el predeterminado, especifique el formato solicitado como un parámetro de cadena de consulta en la solicitud. Por ejemplo:

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02&format=text"
```

### <a name="security"></a>Seguridad
El punto de conexión del servicio de metadatos de instancia solo es accesible desde la instancia de máquina virtual en ejecución en una dirección IP no enrutable. Además, el servicio rechaza cualquier solicitud que tenga un encabezado `X-Forwarded-For`.
También es necesario que las solicitudes incluyan un encabezado `Metadata: true` para garantizar que la solicitud actual esté dirigida directamente y no como parte de un redireccionamiento accidental. 

### <a name="error"></a>Error
Si no se encuentra un elemento de datos o hay una solicitud con formato incorrecto, el servicio de metadatos de instancia devuelve errores HTTP estándar. Por ejemplo:

Código de estado HTTP | Motivo
----------------|-------
200 OK |
400 - Solicitud incorrecta | Falta el encabezado `Metadata: true`
404 No encontrado | El elemento solicitado no existe 
405 Método no permitido | Solo se admiten solicitudes `GET` y `POST`
429 Demasiadas solicitudes | La API es compatible actualmente con un máximo de cinco consultas por segundo
500 Error de servicio     | Vuelva a intentarlo más tarde

### <a name="examples"></a>Ejemplos

> [!NOTE] 
> Todas las respuestas de las API son cadenas JSON. Todas las respuestas de ejemplo siguientes se han impreso correctamente para mejorar la legibilidad.

#### <a name="retrieving-network-information"></a>Recuperación de información de red

**Solicitud**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Respuesta**

> [!NOTE] 
> La respuesta es una cadena JSON. La respuesta de ejemplo siguiente se ha impreso correctamente para mejorar la legibilidad.

```
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>Recuperación de dirección IP pública

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-04-02&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Recuperación de todos los metadatos para una instancia

**Solicitud**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

**Respuesta**

> [!NOTE] 
> La respuesta es una cadena JSON. La respuesta de ejemplo siguiente se ha impreso correctamente para mejorar la legibilidad.

```
{
  "compute": {
    "location": "westus",
    "name": "avset2",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "placementGroupId": "",
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "publisher": "Canonical",
    "resourceGroupName": "myrg",
    "sku": "16.04-LTS",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "",
    "version": "16.04.201708030",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmSize": "Standard_D1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Recuperación de metadatos en una máquina virtual con Windows

**Solicitud**

Los metadatos de instancia se pueden recuperar en Windows a través de la utilidad `curl` de PowerShell: 

```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-04-02 | select -ExpandProperty Content
```

O bien a través del cmdlet `Invoke-RestMethod`:
    
```
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-04-02 -Method get 
```

**Respuesta**

> [!NOTE] 
> La respuesta es una cadena JSON. La respuesta de ejemplo siguiente se ha impreso correctamente para mejorar la legibilidad.

```
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>Categorías de datos de metadatos de instancia
Las categorías de datos siguientes están disponibles a través del servicio de metadatos de instancia:

Datos | DESCRIPCIÓN | Versión de introducción 
-----|-------------|-----------------------
location | La región de Azure donde se ejecuta la máquina virtual | 2017-04-02 
Nombre | Nombre de la máquina virtual | 2017-04-02
offer | Ofrece información para la imagen de máquina virtual. Este valor solo está presente para las imágenes que se implementan desde la galería de imágenes de Azure. | 2017-04-02
publisher | Publicador de la imagen de VM | 2017-04-02
sku | SKU específica de la imagen de VM | 2017-04-02
version | Versión de la imagen de máquina virtual | 2017-04-02
osType | Linux o Windows | 2017-04-02
platformUpdateDomain |  El [dominio de actualización](manage-availability.md) en que se ejecuta la máquina virtual. | 2017-04-02
platformFaultDomain | El [dominio de error](manage-availability.md) en que se ejecuta la máquina virtual. | 2017-04-02
vmId | [Identificador único](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para la máquina virtual. | 2017-04-02
vmSize | [Tamaño de VM](sizes.md) | 2017-04-02
subscriptionId | Suscripción de Azure para la máquina virtual | 2017-08-01
etiquetas | [Etiquetas](../../azure-resource-manager/resource-group-using-tags.md) para su máquina virtual  | 2017-08-01
resourceGroupName | [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md) para su máquina virtual | 2017-08-01
placementGroupId | [Grupo de selección de ubicación](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) de su conjunto de escalado de máquina virtual | 2017-08-01
ipv4/privateIpAddress | Dirección IPv4 local de la máquina virtual | 2017-04-02
ipv4/publicIpAddress | Dirección IPv4 pública de la máquina virtual | 2017-04-02
subnet/address | Dirección de subred de la máquina virtual | 2017-04-02 
subnet/prefix | Prefijo de la subred, ejemplo, 24 | 2017-04-02 
ipv6/ipAddress | Dirección IPv6 local de la máquina virtual | 2017-04-02 
macAddress | Dirección de MAC de la VM | 2017-04-02 
scheduledevents | Actualmente en Versión preliminar pública Vea [scheduledevents](scheduled-events.md) | 2017-03-01

## <a name="example-scenarios-for-usage"></a>Escenarios de ejemplo de uso  

### <a name="tracking-vm-running-on-azure"></a>Seguimiento de una máquina virtual que se ejecuta en Azure

Como proveedor de servicios, es posible que necesite hacer seguimiento de la cantidad de máquinas virtuales que ejecutan su software o que tenga agentes que deban hacer seguimiento de la unicidad de la máquina virtual. Para poder obtener un identificador único para una máquina virtual, use el campo `vmId` del servicio de metadatos de instancia.

**Solicitud**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-04-02&format=text"
```

**Respuesta**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Ubicación de los contenedores y el dominio de error/actualización basado en particiones de datos 

Para ciertos escenarios, la ubicación de las distintas réplicas de datos es de máxima importancia. Por ejemplo, para [la ubicación de réplicas de HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) o la ubicación de contenedores a través de un [orquestador](https://kubernetes.io/docs/user-guide/node-selection/) se debe saber en qué `platformFaultDomain` y `platformUpdateDomain` se ejecuta la máquina virtual.
Puede consultar directamente estos datos a través del servicio de metadatos de instancia.

**Solicitud**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-04-02&format=text" 
```

**Respuesta**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Obtención de más información sobre la VM durante el caso de soporte técnico 

Como proveedor de servicios, es posible que reciba una llamada de soporte técnico en la que le gustaría tener más información sobre la máquina virtual. Pedirle al cliente que comparta los metadatos del equipo puede proporcionar información básica para que el profesional de soporte técnico conozca la variante de máquina virtual en Azure. 

**Solicitud**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-04-02"
```

**Respuesta**

> [!NOTE] 
> La respuesta es una cadena JSON. La respuesta de ejemplo siguiente se ha impreso correctamente para mejorar la legibilidad.

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

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Ejemplos de llamadas al servicio de metadatos mediante lenguajes diferentes dentro de la máquina virtual 

Idioma | Ejemplo 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go            
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
    

## <a name="faq"></a>Preguntas más frecuentes
1. Obtengo el error `400 Bad Request, Required metadata header not specified`. ¿Qué significa?
   * El servicio de metadatos de instancia requiere que el encabezado `Metadata: true` se transmita en la solicitud. Transmitir este encabezado en la llamada de REST permite tener acceso al servicio de metadatos de instancia. 
2. ¿Por qué no recibo información de proceso de mi máquina virtual?
   * Actualmente el servicio de metadatos de instancia solo admite instancias creadas con Azure Resource Manager. En el futuro, es posible que se agregue compatibilidad para máquinas virtuales de servicio en la nube.
3. Creé mi máquina virtual mediante Azure Resource Manager hace un tiempo. ¿Por qué no veo la información de metadatos de proceso?
   * En el caso de las máquinas virtuales que se crearon después de septiembre de 2016, agregue una [etiqueta](../../azure-resource-manager/resource-group-using-tags.md) para empezar a ver los metadatos de proceso. En el caso de máquinas virtuales anteriores (creadas antes de septiembre de 2016), agregue o quite extensiones o discos de datos a la máquina virtual para actualizar los metadatos.
4. No veo todos los datos rellenados para la nueva versión de 2017-08-01
   * En el caso de las máquinas virtuales que se crearon después de septiembre de 2016, agregue una [etiqueta](../../azure-resource-manager/resource-group-using-tags.md) para empezar a ver los metadatos de proceso. En el caso de máquinas virtuales anteriores (creadas antes de septiembre de 2016), agregue o quite extensiones o discos de datos a la máquina virtual para actualizar los metadatos.
5. ¿Por qué recibo el error `500 Internal Server Error`?
   * Vuelva a intentar la solicitud en función del sistema de interrupción exponencial. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Azure.
6. ¿Dónde puedo compartir preguntas o comentarios adicionales?
   * Envíe los comentarios en http://feedback.azure.com.
7. ¿Esto funciona para la instancia del conjunto de escala de máquinas virtuales?
   * Sí, el servicio de metadatos está disponible para las instancias del conjunto de escala. 
8. ¿Cómo puedo obtener soporte técnico para el servicio?
   * Para obtener soporte técnico para el servicio, cree una solicitud de soporte técnico en Azure Portal para la máquina virtual en la que no es posible obtener respuesta de metadatos después de reintentos prolongados 

   ![Soporte técnico de metadatos de instancia](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>pasos siguientes

- Más información sobre la API [Scheduled Events](scheduled-events.md) **en versión preliminar pública** proporcionada por el servicio de metadatos de instancia.
