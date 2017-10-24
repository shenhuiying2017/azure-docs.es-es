---
title: Esquema de rol de Azure Cloud Services | Microsoft Docs
ms.custom: 
ms.date: 12/07/2016
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: e4fbffc1-98eb-449c-971c-de415e45ab34
caps.latest.revision: "12"
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: c25e7d139c7b7fd7c5da6bde8cfb9050eec8a88e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cloud-services-config-role-schema"></a>Esquema de rol de configuración de Azure Cloud Services

El elemento `Role` del archivo de configuración especifica el número de instancias de rol que se implementan para cada rol del servicio, los valores de los parámetros de configuración y las huellas digitales de los certificados asociados a un rol.

Para más información sobre el esquema de configuración de servicios de Azure, consulte el [esquema de configuración de servicio en la nube (clásico)](schema-cscfg-file.md). Para más información sobre el esquema de definición de servicio de Azure, consulte [Cloud Service (classic) Definition Schema](schema-csdef-file.md) (Esquema de definición de servicio en la nube [clásico]).

##  <a name="Role"></a>Elemento Role
En el ejemplo siguiente se muestra el elemento `Role` y sus elementos secundarios.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

En la tabla siguiente se describen los atributos del elemento `Role`.

| Atributo | Descripción |
| --------- | ----------- |
| name   | Necesario. Especifica el nombre del rol. El nombre debe coincidir con el nombre proporcionado en el rol en el archivo de definición de servicio.|
| vmName | Opcional. Especifica el nombre DNS de una máquina virtual. El nombre debe tener 10 caracteres como máximo.|

En la siguiente tabla se describen los elementos secundarios del elemento `Role`.

| Elemento | Descripción |
| ------- | ----------- |
| Instancias | Necesario. Especifica el número de instancias para implementar en el rol. El número de instancias se define mediante un entero en el atributo `count`.|
| Configuración   | Opcional. Especifica un nombre y un valor de configuración en una colección de valores para un rol. El nombre de la configuración se define mediante una cadena en el atributo `name` y el valor de configuración se define mediante una cadena en el atributo `value`.|
| Certificate | Opcional. Especifica el nombre, la huella digital y el algoritmo de un certificado de servicio que se va a asociar con el rol. El nombre del certificado se define mediante una cadena en el atributo `name`. La huella digital del certificado se define mediante una cadena de números hexadecimales que no contienen espacios en el atributo `thumbprint`. Los números hexadecimales deben estar representados por dígitos y caracteres alfanuméricos en mayúscula. El algoritmo de certificado se define mediante una cadena en el atributo `thumbprintAlgorithm`.|

## <a name="see-also"></a>Otras referencias
[Cloud Service (classic) Configuration Schema](schema-cscfg-file.md) (Esquema de configuración de Cloud Service [clásico])