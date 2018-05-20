---
title: Cobertura de representación en Azure Maps | Microsoft Docs
description: Información sobre la cobertura de representación en Azure Maps
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 03/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: ab05277c4541ae859f79b1108c4cf8a7beb29271
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="azure-maps-render-coverage"></a>Cobertura de representación de Azure Maps

Azure Maps usa mosaicos de trama y mosaicos vectoriales para crear mapas. En su resolución más baja, todo el mundo cabría en un solo mosaico. En su resolución más alta, un solo mosaico representa 38 metros cuadrados. A medida que acerca un mapa, puede ver cada vez más detalles sobre los continentes, regiones, ciudades y calles individuales. Para más información, consulte [Niveles de zoom y cuadrícula de mosaico](zoom-levels-and-tile-grid.md).

Sin embargo, Maps no tiene el mismo nivel de información y precisión en todas las regiones. Las tablas siguientes proporcionan información sobre qué nivel de representación de detalles se puede esperar de cada región.

## <a name="legend"></a>Leyenda

| Símbolo | Significado |
|--------|---------|
| ✓ | La región se representa con datos detallados.   |
| Ø | La región se representa con datos simplificados. |


## <a name="africa"></a>África 


| Region | Mosaicos de trama unificados | Mosaicos vectoriales unificados |
| ------ | :------------------: | :------------------: |
| Argelia                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benín                            | ✓ | ✓ |
| Botsuana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Cabo Verde                       |   | ✓ |
| Camerún                         | ✓ | ✓ |
| República Centroafricana         |   | Ø |
| Chad                             |   | Ø |
| Comores                          |   | Ø |
| Congo                            | ✓ | ✓ |
| República Democrática del Congo | ✓ | ✓ |
| Côte D'ivoire                    |   | Ø |
| Yibuti                         |   | Ø |
| Egipto                            | ✓ | ✓ |
| Guinea Ecuatorial                |   | Ø |
| Eritrea                          |   | Ø |
| Etiopía                         |   | Ø |
| Gabón                            | ✓ | ✓ |
| Gambia                           |   | Ø |
| Ghana                            | ✓ | ✓ |
| Guinea                           |   | Ø |
| Guinea-Bisáu                    |   | Ø |
| Kenia                            | ✓ | ✓ |
| Lesoto                          | ✓ | ✓ |
| Liberia                          |   | Ø |
| Libia                            |   | Ø |
| Madagascar                       |   | Ø |
| Malawi                           | ✓ | ✓ |
| Malí                             | ✓ | ✓ |
| Mauritania                       | ✓ | ✓ |
| Mauricio                        | ✓ | ✓ |
| Mayotte                          | ✓ | ✓ |
| Marruecos                          | ✓ | ✓ |
| Mozambique                       | ✓ | ✓ |
| Namibia                          | ✓ | ✓ |
| Níger                            | ✓ | ✓ |
| Nigeria                          | ✓ | ✓ |
| Reunión                          | ✓ | ✓ |
| Ruanda                           | ✓ | ✓ |
| Santa Elena, Ascensión y Tristán da Cunha |   | Ø |
| Santo Tomé y Príncipe            |   | Ø |
| Senegal                          | ✓ | ✓ |
| Sierra Leona                     |   | Ø |
| Somalia                          |   | Ø |
| Sudáfrica                     | ✓ | ✓ |
| Sudán del Sur                      |   | Ø |
| Sudán                            |   | Ø |
| Suazilandia                        | ✓ | ✓ |
| República Unida de Tanzania      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Túnez                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Zambia                           | ✓ | ✓ |
| Zimbabue                         | ✓ | ✓ |

## <a name="americas"></a>América

| Region | Mosaicos de trama unificados | Mosaicos vectoriales unificados |
| ------ | :------------------: | :------------------: |
| Anguila                  | ✓ | ✓ |
| Antigua y Barbuda       | ✓ | ✓ |
| Argentina                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Bahamas                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belice                    | ✓ | ✓ |
| Bermudas                   |   | ✓ |
| Estado Plurinacional de Bolivia |   | ✓ |
| Bonaire, San Eustaquio y Saba |   | ✓ |
| Brasil                    | ✓ | ✓ |
| Canadá                    | ✓ | ✓ |
| Islas Caimán            | ✓ | ✓ |
| Chile                     | ✓ | ✓ |
| Isla Clipperton         |   | ✓ |
| Colombia                  | ✓ | ✓ |
| Costa Rica                |   | ✓ |
| Cuba                      | ✓ | ✓ |
| Curazao                   | ✓ | ✓ |
| Dominica                  | ✓ | ✓ |
| República Dominicana        | ✓ | ✓ |
| Ecuador                   |   | ✓ |
| Islas Malvinas (Falkland Islands) |   | ✓ |
| Guayana Francesa             | ✓ | ✓ |
| Groenlandia                 |   | Ø |
| Granada                   | ✓ | ✓ |
| Guadalupe                | ✓ | ✓ |
| Guatemala                 |   | ✓ |
| Guayana                    | ✓ | ✓ |
| Haití                     | ✓ | ✓ |
| Honduras                  | ✓ | ✓ |
| Jamaica                   | ✓ | ✓ |
| Martinica                | ✓ | ✓ |
| México                    | ✓ | ✓ |
| Montserrat                | ✓ | ✓ |
| Nicaragua                 | ✓ | ✓ |
| Islas Marianas del Norte  |   | ✓ |
| Panamá                    | ✓ | ✓ | 
| Paraguay                  |   | ✓ |
| Perú                      | ✓ | ✓ |
| Puerto Rico               | ✓ | ✓ |
| Quebec (Canadá)           |   | ✓ |
| San Bartolomé          | ✓ | ✓ |
| San Cristóbal y Nieves     | ✓ | ✓ |
| Santa Lucía               | ✓ | ✓ |
| San Martín (Francia)     | ✓ | ✓ |
| San Pedro y Miquelón |   | ✓ |
| San Vicente y las Granadinas | ✓ | ✓ |
| Sint Maarten (Holanda)      | ✓ | ✓ |
| Georgia del Sur e Islas Sandwich del Sur |   | ✓ |
| Surinam                  |   | ✓ |
| Trinidad y Tobago       | ✓ | ✓ |
| Islas Turcas y Caicos  | ✓ | ✓ |
| Estados Unidos             | ✓ | ✓ |
| Uruguay                   | ✓ | ✓ |
| Venezuela                 | ✓ | ✓ |
| Islas Vírgenes Británicas   | ✓ | ✓ |
| Islas Vírgenes (Estados Unidos)      | ✓ | ✓ |

## <a name="asia"></a>Asia 

| Region | Mosaicos de trama unificados | Mosaicos vectoriales unificados |
| ------ | :------------------: | :------------------: |
| Afganistán               |   | Ø |
| Bahréin                   | ✓ | ✓ |
| Bangladesh                |   | Ø |
| Bután                    |   | Ø |
| Territorio Británico del Océano Índico |   | Ø |
| Brunei                    | ✓ | ✓ |
| Camboya                  |   | Ø |
| China                     |   | Ø |
| Islas Cocos (Keeling)   |   | Ø |
| República Popular Democrática de Corea |   | Ø |
| Dokdo y Takeshima       |   | Ø |
| Hong Kong                 | ✓ | ✓ |
| Indonesia                 | ✓ | ✓ |
| Irán                      |   | Ø |
| Iraq                      | ✓ | ✓ |
| Israel                    |   | ✓ |
| Japón                     |   | Ø |
| Jordania                    | ✓ | ✓ |
| Kazajistán                |   | ✓ |
| Kuwait                    | ✓ | ✓ |
| Kirguistán                |   | Ø |
| República Democrática Popular de Laos |   | Ø |
| Líbano                   | ✓ | ✓ |
| Macao                     | ✓ | ✓ |
| Malasia                  | ✓ | ✓ |
| Maldivas                  |   | Ø |
| Mongolia                  |   | Ø |
| Myanmar                   |   | Ø |
| Nepal                     |   | Ø |
| Omán                      | ✓ | ✓ |
| Pakistán                  |   | Ø |
| Filipinas               | ✓ | ✓ |
| Qatar                     | ✓ | ✓ |
| República de Corea         | ✓ | Ø |
| Arabia Saudí              | ✓ | ✓ |
| Islas Senkaku           |   | ✓ |
| Singapur                 | ✓ | ✓|
| Sri Lanka                 |   | Ø |
| República Árabe Siria      |   | Ø |
| Taiwán                    | ✓ | ✓ |
| Tayikistán                |   | Ø |
| Tailandia                  | ✓ | ✓ |
| Timor Oriental               |   | Ø |
| Turkmenistán              |   | Ø |
| Emiratos Árabes Unidos      | ✓ | ✓ |
| Islas menores alejadas de los Estados Unidos |   | Ø |
| Uzbekistán                |   | Ø |
| Vietnam                   | ✓ | ✓ |
| Yemen                     | ✓ | ✓ |

## <a name="oceania"></a>Oceanía

| Region | Mosaicos de trama unificados | Mosaicos vectoriales unificados |
| ------ | :------------------: | :------------------: |
| Samoa Americana            |   | ✓ |
| Australia                 | ✓ | ✓ |
| Islas Cook              |   | Ø |
| Islas Fiji                      |   | Ø |
| Polinesia francesa          |   | Ø |
| Guam                      | ✓ | ✓ |
| Kiribati                  |   | Ø |
| Islas Marshall          |   | Ø |
| Micronesia                |   | Ø |
| Nauru                     |   | Ø |
| Nueva Caledonia             |   | Ø |
| Nueva Zelanda               | ✓ | ✓ |
| Niue                      |   | Ø |
| Isla Norfolk            |   | Ø |
| Palaos                     |   | Ø |
| Papúa Nueva Guinea          |   | Ø |
| Islas Pitcairn                  |   | Ø |
| Samoa                     |   | Ø |
| Islas Salomón           |   | Ø|
| Tokelau                   |   | Ø |
| Tonga                     |   | Ø |
| Tuvalu                    |   | Ø |
| Vanuatu                   |   | Ø |
| Wallis y Futuna         |   | Ø |


## <a name="europe"></a>Europa

| Region | Mosaicos de trama unificados | Mosaicos vectoriales unificados |
| ------ | :------------------: | :------------------: |
| Albania                   | ✓ | ✓ |
| Andorra                   | ✓ | ✓ |
| Armenia                   |   | Ø |
| Austria                   | ✓ | ✓ |
| Azerbaiyán                |   | Ø |
| Bielorrusia                   | Ø | ✓ |
| Bélgica                   | ✓ | ✓ |
| Bosnia y Herzegovina        | ✓ | ✓ |
| Bulgaria                  | ✓ | ✓ |
| Croacia                   | ✓ | ✓ |
| Chipre                    | ✓ | ✓ |
| República Checa            | ✓ | ✓ |
| Dinamarca                   | ✓ | ✓ |
| Estonia                   | ✓ | ✓ |
| Islas Feroe             |   | Ø |
| Finlandia                   | ✓ | ✓ |
| Francia                    | ✓ | ✓ |
| Georgia                   |   | Ø |
| Alemania                   | ✓ |✓ |
| Gibraltar                 | ✓ |   |
| Grecia                    | ✓ | ✓ |
| Guernesey                  |   | ✓ |
| Hungría                   | ✓ | ✓ |
| Islandia                   | ✓ | ✓ |
| Irlanda (República de)     | ✓ | ✓ |
| Isla de Man               |   | ✓ |
| Italia                     | ✓ | ✓ |
| Jan Mayen                 |   | ✓ |
| Jersey                    |   | ✓ |
| Letonia                    | ✓ | ✓ |
| Liechtenstein             | ✓ | ✓ |
| Lituania                 | ✓ | ✓ |
| Luxemburgo                | ✓ | ✓ |
| Macedonia                 | ✓ | ✓ |
| Malta                     | ✓ | ✓ |
| Moldova                   | ✓ | ✓ |
| Mónaco                    | ✓ | ✓ |
| Montenegro                | ✓ | ✓ |
| Países Bajos               | ✓ | ✓ |
| Noruega                    | ✓ | ✓ |
| Polonia                    | ✓ | ✓ |
| Portugal                  | ✓ | ✓ |
| Rumania                   | ✓ | ✓ |
| Federación Rusa        | ✓ | ✓ |
| San Marino                | ✓ | ✓ |
| Serbia                    | ✓ | ✓ |
| Eslovaquia                  | ✓ | ✓ |
| Eslovenia                  | ✓ | ✓ |
| Islas de Kuriles del Sur           |   | ✓ |
| España                     | ✓ | ✓ |
| Svalbard                  |   | ✓ |
| Suecia                    | ✓ |   |
| Suiza               | ✓ | ✓ |
| Turquía                    | ✓ | ✓ |
| Ucrania                   | ✓ | ✓ |
| Reino Unido            | ✓ | ✓ |
| Ciudad del Vaticano              | ✓ | ✓ |

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de la representación en Azure Maps, consulte [Niveles de zoom y cuadrícula de mosaico](zoom-levels-and-tile-grid.md).

Conozca más información acerca de las [áreas de cobertura del servicio de enrutamiento de Maps](routing-coverage.md). 