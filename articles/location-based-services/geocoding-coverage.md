---
title: Geocodificación de la cobertura en Azure Location Based Services | Microsoft Docs
description: Aprenda a realizar la geocodificación de la cobertura en Azure Location Based Services
services: location-based-services
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: article
ms.service: location-based-services
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: b2ae0f2cb8eba6ca42b3c82458d1fadf4ea93cdf
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="azure-location-based-services---geocoding-coverage"></a>Azure Location Based Services: geocodificación de la cobertura
Azure Location Based Services (LBS) proporciona información detallada de geocodificación para la búsqueda de direcciones, lugares y otras entidades geográficas alrededor del mundo. Esto es importante para los clientes que quieren saber dónde pueden o no pueden esperar encontrar diversos niveles de fidelidad al buscar direcciones en Search Service. Al buscar regiones con una cobertura de baja fidelidad, se pueden esperar resultados de la búsqueda con una confianza inferior. En la tabla siguiente se proporciona información de cobertura de codificación geográfica de búsqueda de Azure LBS.

| Region             | Puntos de direcciones<sup>1</sup>|Números de casa<sup>2</sup>| Nivel de calle | Nivel de ciudad | Puntos de interés |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| **América**                                            |                 |                |              |            |                    |
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
| |                 |                |              |            |                    |
| **Asia Pacífico**                                        |                 |                |              |            |                    |
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
| |                 |                |              |            |                    |
| **Europa**                                              |                 |                |              |            |                    |
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
| |                 |                |              |            |                    |
| **Oriente Medio y África**                                  |                 |                |              |            |                    |
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


<br>
<br>




|Términos|Definiciones|
|--- |--- |
|**<sup>1</sup> - Punto de dirección**|Se obtiene del índice de direcciones de punto de Búsqueda en línea. Este campo solo se admite cuando se admiten la búsqueda y la codificación geográfica.|
|**<sup>2</sup> - Número de casa**|Se obtiene del índice de interpolación de dirección de Búsqueda en línea. Este campo solo se admite cuando se admiten la búsqueda y la codificación geográfica.|
|**<sup>3</sup> - Código asignado por el usuario**|Código específico de Búsqueda en línea, no un código oficial ISO 3166-1.|
