---
title: Servicio de metadatos de instancia de Azure para Windows | Microsoft Docs
description: "Interfaz RESTful para obtener información sobre proceso, red y próximos eventos de mantenimiento de la máquina virtual Windows."
services: virtual-machines-windows
documentationcenter: 
author: harijay
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/11/2017
ms.author: harijay
ms.openlocfilehash: 55b97b89cb297dc08dc73f6714c5159d4565a97c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="azure-instance-metadata-service-for-windows-vms"></a>Servicio de metadatos de instancia de Azure para máquinas virtuales Windows


El servicio de metadatos de instancia de Azure proporciona información sobre instancias de máquina virtual en ejecución que pueden usarse para administrar y configurar las máquinas virtuales.
Esto incluye información como SKU, configuración de red y eventos de mantenimiento próximos. Para más información sobre el tipo de información está disponible, vea las [categorías de metadatos](#instance-metadata-data-categories).

El servicio de metadatos de instancia de Azure es un punto de conexión REST al que pueden tener acceso todas las máquinas virtuales IaaS creadas a través de [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). El punto de conexión está disponible en una dirección IP no enrutable conocida (`169.254.169.254`) a la que solo se puede tener acceso desde dentro de la máquina virtual.



> [!IMPORTANT]
> Este servicio está **disponible con carácter general** en regiones de Azure globales. Azure Cloud se encuentra en versión preliminar pública para Azure Government, Azure en China y Azure Alemania. Recibe actualizaciones periódicas para exponer información nueva sobre instancias de máquina virtual. En esta página se reflejan las [categorías de datos](#instance-metadata-data-categories) actualizadas disponibles.



## <a name="service-availability"></a>Disponibilidad del servicio
El servicio está disponible con carácter general en regiones de Azure globales. El servicio se encuentra en versión preliminar pública en las regiones Government, China y Alemania.

Regiones                                        | ¿Disponibilidad?
-----------------------------------------------|-----------------------------------------------
[Todas las regiones globales de Azure disponibles con carácter general](https://azure.microsoft.com/regions/)     | Disponibilidad general 
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | En versión preliminar 
[Azure en China](https://www.azure.cn/)                                                           | En versión preliminar
[Azure Alemania](https://azure.microsoft.com/overview/clouds/germany/)                    | En versión preliminar

Esta tabla se actualiza cuando el servicio está disponible en otras nubes de Azure.

Para probar el servicio de metadatos de instancia, cree una máquina virtual desde [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) o [Azure Portal](http://portal.azure.com) en las regiones anteriores, y siga los ejemplos siguientes.

## <a name="usage"></a>Uso

### <a name="versioning"></a>Control de versiones
El servicio de metadatos de instancia tiene versiones. Las versiones son obligatorias y la versión actual es la `2017-04-02`.

> [!NOTE] 
> Las versiones preliminares de eventos programados compatibles {más reciente} como la versión de api. Este formato ya no es compatible y dejará de utilizarse en el futuro.

A medida que agreguemos versiones más recientes, todavía se podrá acceder a las versiones anteriores por motivos de compatibilidad si los scripts tienen dependencias en formatos de datos específicos. Pero tenga en cuenta que es posible que la versión preliminar actual (2017-03-01) no esté disponible una vez que el servicio esté disponible con carácter general.

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
De forma predeterminada, el servicio de metadatos de instancia devuelve datos en formato JSON (`Content-Type: application/json`). Pero las distintas API pueden devolver los datos en formatos diferentes si se solicita.
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
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-04-02"
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
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

**Respuesta**

> [!NOTE] 
> La respuesta es una cadena JSON. La respuesta de ejemplo siguiente se ha impreso correctamente para mejorar la legibilidad.

```
{
  "compute": {
    "location": "westcentralus",
    "name": "IMDSSample",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "Canonical",
    "sku": "16.04.0-LTS",
    "version": "16.04.201610200",
    "vmId": "5d33a910-a7a0-4443-9f01-6a807801b29b",
    "vmSize": "Standard_A1"
  },
  "network": {
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
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Recuperación de metadatos en una máquina virtual Windows

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

Datos | Descripción
-----|------------
location | La región de Azure donde se ejecuta la máquina virtual
name | Nombre de la máquina virtual 
offer | Ofrece información para la imagen de máquina virtual. Este valor solo está presente para las imágenes que se implementan desde la galería de imágenes de Azure.
publisher | Publicador de la imagen de VM
sku | SKU específica de la imagen de VM  
versión | Versión de la imagen de máquina virtual 
osType | Linux o Windows 
platformUpdateDomain |  El [dominio de actualización](manage-availability.md) en que se ejecuta la máquina virtual.
platformFaultDomain | El [dominio de error](manage-availability.md) en que se ejecuta la máquina virtual.
vmId | [Identificador único](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para la máquina virtual.
vmSize | [Tamaño de VM](sizes.md)
ipv4/privateIpAddress | Dirección IPv4 local de la máquina virtual 
ipv4/publicIpAddress | Dirección IPv4 pública de la máquina virtual
subnet/address | Dirección de subred de la máquina virtual
subnet/prefix | Prefijo de la subred, ejemplo, 24
ipv6/ipAddress | Dirección IPv6 local de la máquina virtual
macAddress | Dirección de MAC de la VM 
scheduledevents | Actualmente en Versión preliminar pública Vea [scheduledevents](scheduled-events.md)

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
Go Lan   | https://github.com/Microsoft/azureimds/blob/master/imdssample.go            
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Javascript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
    

## <a name="faq"></a>P+F
1. Obtengo el error `400 Bad Request, Required metadata header not specified`. ¿Qué significa?
   * El servicio de metadatos de instancia requiere que el encabezado `Metadata: true` se transmita en la solicitud. Transmitir este encabezado en la llamada de REST permite tener acceso al servicio de metadatos de instancia. 
2. ¿Por qué no recibo información de proceso de mi máquina virtual?
   * Actualmente el servicio de metadatos de instancia solo admite instancias creadas con Azure Resource Manager. En el futuro, es posible que se agregue compatibilidad para máquinas virtuales de servicio en la nube.
3. Creé mi máquina virtual mediante Azure Resource Manager hace un tiempo. ¿Por qué no veo la información de metadatos de proceso?
   * En el caso de las máquinas virtuales que se crearon después de septiembre de 2016, agregue una [etiqueta](../../azure-resource-manager/resource-group-using-tags.md) para empezar a ver los metadatos de proceso. En el caso de máquinas virtuales anteriores (creadas antes de septiembre de 2016), agregue o quite extensiones o discos de datos a la máquina virtual para actualizar los metadatos.
4. ¿Por qué recibo el error `500 Internal Server Error`?
   * Vuelva a intentar la solicitud en función del sistema de interrupción exponencial. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Azure.
5. ¿Dónde puedo compartir preguntas o comentarios adicionales?
   * Envíe los comentarios en http://feedback.azure.com.
7. ¿Esto funciona para la instancia del conjunto de escala de máquinas virtuales?
   * Sí, el servicio de metadatos está disponible para las instancias del conjunto de escala. 
6. ¿Cómo puedo obtener soporte técnico para el servicio?
   * Para obtener soporte técnico para el servicio, cree un problema de compatibilidad en Azure Portal para la máquina virtual en la que no es posible obtener respuesta de metadatos después de reintentos prolongados 

   ![Soporte técnico de metadatos de instancia](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la API [Scheduled Events](scheduled-events.md) **en versión preliminar pública** proporcionada por el servicio de metadatos de instancia.
