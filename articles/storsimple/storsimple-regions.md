---
title: "Disponibilidad por región de StorSimple | Microsoft Docs"
description: "En este artículo se detallan las regiones de Azure en las que están disponibles los distintos modelos de dispositivo de StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: d47109d541a3df93d9234e27e53d1538f6bc4c6e
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="available-regions-for-your-storsimple"></a>Regiones disponibles para StorSimple

## <a name="overview"></a>Información general

Los centros de datos de Azure funcionan en varias ubicaciones geográficas de todo el mundo para cumplir con las exigencias de rendimiento del cliente, los requisitos y las preferencias con respecto a la ubicación de los datos. Una ubicación geográfica de Azure es un área definida del mundo que contiene al menos una región de Azure. Una región de Azure es un área dentro de una ubicación geográfica que contiene uno o varios centros de datos.

La elección de una región de Azure es muy importante y se ve afectada por factores como la residencia y soberanía de los datos, la disponibilidad del servicio, el rendimiento, el costo y la redundancia. Para más información sobre cómo elegir una región, vaya a [¿Qué región de Azure es la adecuada para mí?](https://azure.microsoft.com/overview/datacenters/how-to-choose/).

Para la solución de StorSimple, la elección de la región viene determinada específicamente por los factores siguientes:

- Las regiones donde está disponible el servicio Administrador de dispositivos de StorSimple.
- Los países donde está disponible el dispositivo físico, virtual o en la nube de StorSimple.
- Las regiones donde deben ubicarse las cuentas de almacenamiento que almacenan datos de StorSimple para un rendimiento óptimo.

En este tutorial se describe la disponibilidad de las regiones del servicio Administrador de dispositivos de StorSimple, los dispositivos físicos locales y en la nube. La información de este artículo se aplica solo a los dispositivos StorSimple de las series 8000 y 1200.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Disponibilidad por región del servicio Administrador de dispositivos de StorSimple

El servicio Administrador de dispositivos de StorSimple se admite actualmente en 12 regiones públicas y dos regiones de Azure Government.

Una región o ubicación se define cuando se crea por primera vez el servicio Administrador de dispositivos de StorSimple. En general, se elige la ubicación más cercana a la región geográfica donde se implementa el dispositivo. Sin embargo, el dispositivo y el servicio también se pueden implementar en ubicaciones diferentes.

Aquí tiene una lista de las regiones donde el servicio Administrador de dispositivos de StorSimple está disponible para la nube pública de Azure y se puede implementar.

![storsimple-device-manager-service-regions](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Para la nube de Azure Government, el servicio Administrador de dispositivos de StorSimple está disponible en los centros de datos Iowa Gob. EE. UU. y Virginia Gob. EE. UU.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Disponibilidad por región de los datos almacenados en StorSimple

Los datos de StorSimple se almacenan físicamente en las cuentas de Azure Storage, y estas cuentas están disponibles en todas las regiones de Azure. Cuando se crea una cuenta de Azure Storage, se elige la ubicación principal de la cuenta de almacenamiento, lo cual determina la región donde residen los datos.

La primera vez que se crea un servicio Administrador de dispositivos de StorSimple y se le asocia una cuenta de almacenamiento, el servicio Administrador de dispositivos de StorSimple y Azure Storage pueden estar en dos ubicaciones diferentes. En ese caso, se le pedirá que cree la cuenta del Administrador de dispositivos de StorSimple y la de almacenamiento de Azure por separado.

En general, elija la región más cercana al servicio de la cuenta de almacenamiento. Sin embargo, es posible que la región de Microsoft Azure más cercana no sea la región con la latencia más baja. La latencia es la que dicta el rendimiento del servicio de red y, por tanto, el rendimiento de la solución. Así pues, si se elige una cuenta de almacenamiento de una región distinta, es importante saber cuáles son las latencias entre el servicio y la región asociada a la cuenta de almacenamiento.

Si usa un StorSimple Cloud Appliance, se recomienda que el servicio y la cuenta de almacenamiento asociada estén en la misma región. Es posible que el rendimiento de las cuentas de almacenamiento que estén una región diferente sea bajo.

## <a name="availability-of-storsimple-device"></a>Disponibilidad del dispositivo de StorSimple

Según el modelo, los dispositivos de StorSimple pueden estar disponibles en zonas geográficas o países diferentes.

### <a name="storsimple-physical-device-models-81008600"></a>Dispositivo físico de StorSimple (modelos 8100/8600)

Los dispositivos físicos StorSimple 8100 o 8600 están disponibles en los países siguientes.

| #  | País        | #  | País     | #  | País      | #  | País              |
|----|----------------|----|-------------|----|--------------|----|----------------------|
| 1  | Australia      | 16 | Hong Kong   | 31 | Nueva Zelanda  | 46 | Sudáfrica         |
| 2  | Austria        | 17 | Hungría     | 32 | Nigeria      | 47 | Corea del Sur          |
| 3  | Bahréin        | 18 | Islandia     | 33 | Noruega       | 48 | España                |
| 4  | Bélgica        | 19 | India       | 34 | Perú         | 49 | Sri Lanka            |
| 5  | Brasil         | 20 | Indonesia   | 35 | Filipinas  | 50 | Suecia               |
| 6  | Canadá         | 21 | Irlanda     | 36 | Polonia       | 51 | Suiza          |
| 7  | Chile          | 22 | Israel      | 37 | Portugal     | 52 | Taiwán               |
| 8  | Colombia       | 23 | Italia       | 38 | Puerto Rico  | 53 | Tailandia             |
| 9  | República Checa | 24 | Japón       | 39 | Qatar        | 54 | Turquía               |
| 10 | Dinamarca        | 25 | Kenia       | 40 | Rumania      | 55 | Ucrania              |
| 11 | Egipto          | 26 | Kuwait      | 41 | Rusia       | 56 | Emiratos Árabes Unidos |
| 12 | Finlandia        | 27 | Macao       | 42 | Arabia Saudí | 57 | Reino Unido       |
| 13 | Francia         | 28 | Malasia    | 43 | Singapur    | 58 | Estados Unidos        |
| 14 | Alemania        | 29 | México      | 44 | Eslovaquia     | 59 | Vietnam              |
| 15 | Grecia         | 30 | Países Bajos | 45 | Eslovenia     | 60 | Croacia              |

Esta lista puede variar a medida que se agreguen más países. Para obtener una lista más actualizada de las regiones geográficas, vaya al apéndice de términos de matriz de almacenamiento en los [Términos del producto](https://www.microsoft.com/en-us/Licensing/product-licensing).

Microsoft puede enviar el hardware físico y proporcionar piezas de hardware de repuesto para StorSimple a las regiones geográficas de la lista anterior.

> [!IMPORTANT]
> No se debe colocar un dispositivo StorSimple físico en una región en la que no se admita. Microsoft no podrá enviar piezas de repuesto a los países donde no se admita StorSimple.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (modelos 8010/8020)

StorSimple Cloud Appliance 8010 o 8020 se admite y está disponible en todas las regiones donde se admite la máquina virtual subyacente. El modelo 8010 usa una máquina virtual _Standard_A3_ que se admite en todas las regiones de Azure.

El modelo 8020 usa Premium Storage y una máquina virtual _Standard_DS3_ para crear un dispositivo de nube. El modelo 8020 se admite en las regiones de Azure que admiten Premium Storage y máquinas virtuales _Standard_DS3_ de Azure. Use [esta lista](https://azure.microsoft.com/regions/services/) para ver si las dos opciones **Virtual Machines > Serie DS** y **Storage > Almacenamiento en disco** están disponibles en su región.

### <a name="storsimple-virtual-array-model-1200"></a>Matriz virtual de StorSimple (modelo 1200)

Si usa una matriz virtual de StorSimple de la serie 1200, la imagen de disco virtual se admite en todas las regiones de Azure.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre los [precios de los distintos modelos de StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Obtenga más información sobre cómo [administrar su cuenta de almacenamiento de StorSimple](storsimple-8000-manage-storage-accounts.md).
* Obtenga más información acerca de cómo [usar el servicio StorSimple Device Manager para administrar cualquier dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
