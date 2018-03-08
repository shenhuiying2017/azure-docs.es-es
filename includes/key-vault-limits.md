Transacciones clave (N.º máximo de transacciones permitidas en 10 segundos, por almacén y región<sup>1</sup>):

|Tipo de clave|Clave HSM<br>Clave CREAR|Clave HSM<br>Las restantes transacciones|Clave de software<br>Clave CREAR|Clave de software<br>Las restantes transacciones|
|:---|---:|---:|---:|---:|
|2048 bits de RSA|5|1000|10|2000|
|3072 bits de RSA|5|250|10|500|
|4096 bits de RSA|5|125|10|250|
|

Secretos, claves de cuentas de almacenamiento administradas y transacciones de almacén:
| Tipo de transacciones | N.º máximo de transacciones permitidas en 10 segundos, por almacén y región<sup>1</sup> |
| --- | --- |
| Todas las transacciones |2000 |
|

Vea la [Guía de las limitaciones de Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md) para obtener información sobre cómo controlar la limitación cuando se superan estos límites.

<sup>1</sup> El límite global para la suscripción para todos los tipos de transacciones es 5 veces el límite del almacén de claves. Por ejemplo, en "HSM - otras transacciones" el límite es de 5000 transacciones en 10 segundos por suscripción.
