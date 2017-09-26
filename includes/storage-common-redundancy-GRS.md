El almacenamiento con redundancia geográfica (GRS) replica sus datos a una región secundaria que se encuentra a cientos de kilómetros de distancia de la región primaria. Si la cuenta de almacenamiento tiene habilitado GRS, sus datos se mantienen incluso ante un apagón regional completo o un desastre del cual la región principal no se puede recuperar.

En el caso de una cuenta de almacenamiento con GRS habilitado, una actualización primero se envía a la región principal, donde se replica tres veces. A continuación, la actualización se replica de manera asincrónica en la región secundaria, donde también se replica tres veces.

Con GRS, las regiones primarias y secundarias administran las réplicas entre dominios de error y de actualización diferentes dentro de una unidad de escalado de almacenamiento, como se describe con LRS.

Consideraciones:

* Como la replicación asincrónica implica un retraso, ante la eventualidad de un desastre regional es posible que los cambios que todavía no se hayan replicado en la región secundaria se pierdan si no es posible recuperar los datos de la región primaria.
* La réplica no está disponible a menos que Microsoft inicie la conmutación por error para la región secundaria. Si Microsoft inicia una conmutación por error en la región secundaria, tendrá acceso de lectura y escritura a esos datos una vez completada la conmutación por error. Para más información, consulte la [guía de recuperación ante desastres](../articles/storage/common/storage-disaster-recovery-guidance.md). 
* Si una aplicación desea leer desde la región secundaria, el usuario debe habilitar RA-GRS.

Cuando crea una cuenta de almacenamiento, selecciona la región principal de la cuenta. La región secundaria se determina según la región principal y no es posible cambiarla. La siguiente tabla muestra los emparejamientos de la región principal y la secundaria.

| Principal | Secundario |
| --- | --- |
| Centro-Norte de EE. UU | Centro-Sur de EE. UU |
| Centro-Sur de EE. UU | Centro-Norte de EE. UU |
| Este de EE. UU. | Oeste de EE. UU. |
| Oeste de EE. UU. | Este de EE. UU. |
| Este de EE. UU. - 2 | Central EE. UU.: |
| Central EE. UU.: | Este de EE. UU. - 2 |
| Europa del Norte | Europa occidental |
| Europa occidental | Europa del Norte |
| Sudeste de Asia | Asia oriental |
| Asia oriental | Sudeste de Asia |
| Este de China | Norte de China |
| Norte de China | Este de China |
| Este de Japón | Oeste de Japón |
| Oeste de Japón | Este de Japón |
| Sur de Brasil | Centro-Sur de EE. UU |
| Australia Oriental | Sudeste de Australia |
| Sudeste de Australia | Australia Oriental |
| Sur de India | India central |
| India central | Sur de India |
| India occidental | Sur de India |
| Gobierno de EE. UU. - Iowa | Gobierno de EE. UU. - Virginia |
| Gobierno de EE. UU. - Virginia | Gobierno de EE. UU.: Texas |
| Gobierno de EE. UU.: Texas | Gobierno de EE. UU.: Arizona |
| Gobierno de EE. UU.: Arizona | Gobierno de EE. UU.: Texas |
| Centro de Canadá | Este de Canadá |
| Este de Canadá | Centro de Canadá |
| Oeste de Reino Unido | Sur del Reino Unido |
| Sur del Reino Unido 2 | Oeste de Reino Unido |
| Centro de Alemania | Noreste de Alemania |
| Noreste de Alemania | Centro de Alemania |
| Oeste de EE. UU. 2 | Centro occidental de EE.UU. |
| Centro occidental de EE.UU. | Oeste de EE. UU. 2 |

Para obtener información actualizada sobre las regiones compatibles con Azure, consulte [Regiones de Azure](https://azure.microsoft.com/regions/).

>[!NOTE]  
> La región secundaria de Virginia Gob. EE. UU. es Texas Gob. EE. UU. Anteriormente, Virginia Gob. EE. UU. utilizaba Iowa Gob. EE. UU. como región secundaria. Las cuentas de almacenamiento que aún aprovechan Iowa Gob. EE. UU. como región secundaria se están migrando a Texas Gob. EE. UU. como región secundaria. 
> 
> 