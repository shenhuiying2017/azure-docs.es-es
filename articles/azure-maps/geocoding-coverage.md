---
title: Cobertura de geocodificación en Azure Maps | Microsoft Docs
description: Información sobre la cobertura de geocodificación en Azure Maps
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a7dc7a1dbf024b6d334ec374f78d5e912db3835c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
---
# <a name="azure-maps-geocoding-coverage"></a>Cobertura de geocodificación de Azure Maps

Cuando busca una ubicación con Azure Maps, el servicio de búsqueda toma los términos de su búsqueda y devuelve las coordenadas de latitud y longitud mediante un proceso denominado geocodificación. Sin embargo, Maps no tiene el mismo nivel de información y precisión en todas las regiones y países. Use este artículo para determinar qué tipo de ubicaciones puede buscar de forma confiable en cada región. 

## <a name="americas"></a>América

| Region             | Puntos de dirección | Números de casa | Nivel de calle | Nivel de ciudad | Puntos de interés |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguila                                            |                 |                |              |      ✓     |          ✓         |
| Antártida                                          |                 |                |              |      ✓     |          ✓         |
| Antigua y Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentina                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belice                                              |                 |                |              |      ✓     |          ✓         |
| Bermudas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolivia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, San Eustaquio y Saba|                 |                |              |      ✓     |          ✓         |
| Brasil                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Canadá                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Islas Caimán                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chile                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Isla Clipperton                                   |                 |                |              |      ✓     |                    |
| Colombia                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Cuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominica                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominicana                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Ecuador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| El Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Islas Malvinas                                    |                 |                |              |      ✓     |          ✓         |
| Guayana Francesa                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Granada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadalupe|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Haití                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamaica                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinica                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| México                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nicaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panamá                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguay                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Perú                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Puerto Rico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Bartolomé                                    |                 |                |       ✓      |      ✓     |          ✓         |
| San Cristóbal y Nieves                               |                 |                |       ✓      |      ✓     |          ✓         |
| Santa Lucía                                         |                 |                |              |      ✓     |          ✓         |
| San Martín                                        |                 |                |       ✓      |      ✓     |          ✓         |
| San Pedro y Miquelón|                 |                |       ✓      |      ✓     |          ✓         |
| San Vicente y las Granadinas                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Georgia del Sur e Islas Sandwich del Sur        |                 |                |              |      ✓     |          ✓         |
| Surinam                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad y Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Islas menores alejadas de los Estados Unidos                |                 |                |              |      ✓     |          ✓         |
| Estados Unidos de América                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Islas Vírgenes Británicas                            |                 |                |              |      ✓     |          ✓         |
| Islas Vírgenes de los Estados Unidos de América                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Asia Pacífico

| Region             | Puntos de dirección |Números de casa | Nivel de calle | Nivel de ciudad | Puntos de interés |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Samoa Americana                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Australia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesh                                          |                 |                |              |      ✓     |          ✓         |
| Bután                                              |                 |                |              |      ✓     |          ✓         |
| Territorio Británico del Océano Índico                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Camboya                                            |                 |                |              |      ✓     |          ✓         |
| China |                 |                |              |      ✓     |          ✓         |
| Isla Christmas                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Islas Cocos (Keeling)|                 |                |              |      ✓     |          ✓         |
| Comores|                 |                |              |      ✓     |          ✓         |
| Islas Cook                                        |                 |                |              |      ✓     |          ✓         |
| Islas Fiji |                 |                |              |      ✓     |          ✓         |
| Polinesia francesa                                    |                 |                |              |      ✓     |          ✓         |
| Isla Heard e Islas McDonald                   |                 |                |              |      ✓     |          ✓         |
| Hong Kong                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonesia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Japón                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| Macao                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malasia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Micronesia |                 |                |              |      ✓     |          ✓         |
| Mongolia                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Nepal|                 |                |              |      ✓     |          ✓         |
| Nueva Caledonia                                       |                 |                |              |      ✓     |          ✓         |
| Nueva Zelanda                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Isla Norfolk                                      |                 |                |              |      ✓     |          ✓         |
| Corea del Norte                                         |                 |                |              |      ✓     |          ✓         |
| Islas Marianas del Norte                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakistán                                            |                 |                |              |      ✓     |          ✓         |
| Palaos |                 |                |              |      ✓     |          ✓         |
| Papúa Nueva Guinea                                    |                 |                |              |      ✓     |          ✓         |
| Islas Paracelso                                     |                 |                |              |      ✓     |                    |
| Filipinas                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Islas Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Islas Senkaku                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapur                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Islas Salomón                                     |                 |                |              |      ✓     |          ✓         |
| Corea del Sur                                         |                 |                |              |      ✓     |          ✓         |
| Islas de Kuriles del Sur                                     |        ✓        |                |              |      ✓     |          ✓         |
| Islas Spratly                                     |                 |                |              |      ✓     |                    |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Taiwán                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tailandia                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Islas Turcas y Caicos                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis y Futuna|                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Europa

| Region             | Puntos de dirección |Números de casa | Nivel de calle | Nivel de ciudad | Puntos de interés |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albania                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Armenia                                             |                 |                |              |      ✓     |          ✓         |
| Austria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbaiyán                                          |                 |                |              |      ✓     |          ✓         |
| Bélgica                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bosnia y Herzegovina                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bulgaria                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Bielorrusia|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Croacia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Chipre                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| República Checa                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dinamarca                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estonia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Islas Feroe                                       |                 |                |              |      ✓     |          ✓         |
| Finlandia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Francia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Georgia                                             |                 |                |              |      ✓     |          ✓         |
| Alemania                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibraltar                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grecia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Groenlandia                                           |                 |                |              |      ✓     |          ✓         |
| Guernesey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Hungría                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Islandia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Irlanda (República de)                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Isla de Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Italia                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazajistán                                          |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kirguistán                                          |                 |                |              |      ✓     |          ✓         |
| Letonia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lituania                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Luxemburgo                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Macedonia                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldova                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Mónaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Montenegro                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Países Bajos                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Noruega                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polonia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugal                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Rumania                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Federación Rusa                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Serbia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Eslovaquia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Eslovenia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| España                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Suecia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Suiza                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tayikistán                                          |                 |                |              |      ✓     |          ✓         |
| Turquía                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turkmenistán                                        |                 |                |              |      ✓     |          ✓         |
| Ucrania                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Reino Unido                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uzbekistán                                          |                 |                |              |      ✓     |          ✓         |
| Ciudad del Vaticano                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Oriente Medio y África

| Region             | Puntos de dirección |Números de casa | Nivel de calle | Nivel de ciudad | Puntos de interés |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afganistán                                         |                 |                |              |      ✓     |          ✓         |
| Argelia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahréin                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Benín                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botsuana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Isla Bouvet                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Camerún                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde|                 |                |       ✓      |      ✓     |          ✓         |
| República Centroafricana                            |                 |                |       ✓      |      ✓     |          ✓         |
| Chad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Congo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte D'ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| República Democrática del Congo                        |                 |                |       ✓      |      ✓     |          ✓         |
| Yibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egipto                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| República de Guinea Ecuatorial                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritrea                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiopía                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Territorios Australes Franceses|                 |                |              |      ✓     |          ✓         |
| Gabón                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambia                                              |                 |                |              |      ✓     |          ✓         |
| Ghana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea-Bisáu                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Irán                                                |                 |                |              |      ✓     |          ✓         |
| Iraq                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Israel                                              |                 |                |              |      ✓     |          ✓         |
| Jordania                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kenia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuwait                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Líbano                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Lesoto                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Liberia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libia|                 |                |       ✓      |      ✓     |          ✓         |
| Madagascar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldivas |                 |                |              |      ✓     |          ✓         |
| Malí                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Islas Marshall |                 |                |              |      ✓     |          ✓         |
| Mauritania                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mauricio                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Marruecos                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mozambique                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namibia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Níger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigeria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Omán                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Qatar                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Reunión|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ruanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Santa Elena                                        |                 |                |              |      ✓     |          ✓         |
| Arabia Saudí                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seychelles                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leona                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somalia                                             |                 |                |              |      ✓     |          ✓         |
| Sudáfrica                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sudán del Sur                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Sudán                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Suazilandia                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Siria                                               |                 |                |              |      ✓     |          ✓         |
| República Democrática de Santo Tomé y Príncipe       |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzania                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Túnez                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Emiratos Árabes Unidos                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Cisjordania                                           |                 |                |              |      ✓     |          ✓         |
| Yemen                                               |                 |                |              |      ✓     |          ✓         |
| Zambia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabue                                            |                 |                |       ✓      |      ✓     |          ✓         |



## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de la geocodificación de Azure Maps, consulte las páginas de referencia de [Búsqueda](https://docs.microsoft.com/rest/api/maps/search).

Conozca más información acerca de las [áreas de cobertura del servicio de tráfico de Maps](traffic-coverage.md). 

