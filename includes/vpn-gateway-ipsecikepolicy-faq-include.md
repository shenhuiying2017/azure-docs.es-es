<a id="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus" class="xliff"></a>

### ¿Se admite la directiva de IPsec o IKE personalizada en todas las SKU de Azure VPN Gateway?
La directiva IPsec/IKE personalizada se admite en las puertas de enlace Azure VPN Gateway **VpnGw1, VpnGw2, VpnGw3, Estándar** y **HighPerformance**. **Basic** NO se admite.

<a id="how-many-policies-can-i-specify-on-a-connection" class="xliff"></a>

### ¿Cuántas directivas puedo especificar en una conexión?
Solo se puede especificar ***una*** combinación de directivas para una conexión dada.

<a id="can-i-specify-a-partial-policy-on-a-connection-eg-only-ike-algorithms-but-not-ipsec" class="xliff"></a>

### ¿Se puede especificar una directiva parcial en una conexión? (p. ej., solo algoritmos IKE, no IPsec)
No, es preciso especificar todos los algoritmos y parámetros de IKE (modo principal) e IPsec (modo rápido). No se permite la especificación de una directiva parcial.

<a id="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy" class="xliff"></a>

### ¿Cuáles son los algoritmos y los niveles de las claves que se admiten en la directiva personalizada?
En la tabla siguiente se enumeran los algoritmos criptográficos y los niveles de las claves admitidos que pueden configurar los clientes. Es preciso seleccionar una opción en cada campo.

| **IPsec o IKEv2**  | **Opciones**                                                                 |
| ---              | ---                                                                         |
| Cifrado IKEv2 | AES256, AES192, AES128, DES3, DES                                           |
| Integridad de IKEv2  | SHA384, SHA256, SHA1, MD5                                                   |
| Grupo DH         | ECP384, ECP256, DHGroup24, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, No |
| Cifrado IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, No    |
| Integridad de IPsec  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                          |
| Grupo PFS        | ECP384, ECP256, PFS24, PFS2048, PFS14, PFS2, PFS1, No                     |
| Vigencia de SA (QM)*  | Segundos (entero; **mín. 300**) y KB (entero; **mín. 1024**)                                      |
| Selector de tráfico | UsePolicyBasedTrafficSelectors** ($True/$False; valor predeterminado: $False)                             |
|                  |                                                                             |

* (*) La vigencia de SA del modo principal de IKEv2 se fija en 28 800 segundos en las puertas de enlace de VPN de Azure
* (**) En el siguiente elemento de las preguntas frecuentes encontrará información acerca de "UsePolicyBasedTrafficSelectors"

<a id="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations" class="xliff"></a>

### ¿Es preciso que coincidan todos los elementos de la directiva de Azure VPN Gateway con las configuraciones de mis dispositivos VPN locales?
La configuración de su dispositivo VPN local debe coincidir o contener los siguientes algoritmos y parámetros que se especifican en la directiva de IPsec o IKE de Azure:

* Algoritmo de cifrado IKE
* Algoritmo de integridad de IKE
* Grupo DH
* Algoritmo de cifrado IPsec
* Algoritmo de integridad de IPsec
* Grupo PFS
* Selector de tráfico (*)

Las vigencias de SA solo son especificaciones locales, no es preciso que coincidan.

Si habilita **UsePolicyBasedTrafficSelectors**, debe asegurarse de que el dispositivo VPN tiene los selectores de tráfico coincidentes definidos con todas las combinaciones de sus prefijos de red local (puerta de enlace de red local) a o desde los prefijos de red virtual de Azure, en lugar de cualquiera a cualquiera. Por ejemplo, si sus prefijos de red local son 10.1.0.0/16 y 10.2.0.0/16, y sus prefijos de red virtual son 192.168.0.0/16 y 172.16.0.0/16, debe especificar los siguientes selectores de tráfico:
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

Para más información acerca de cómo usar esta opción, consulte [Connect Azure VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Conexión de puertas de enlace de VPN de Azure a varios dispositivos VPN basados en directivas locales mediante PowerShell).

<a id="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways" class="xliff"></a>

### ¿Reemplaza la directiva personalizada los conjuntos de directivas de IPsec o IKE predeterminados en las puertas de enlace de VPN de Azure?
Sí, una vez que se especifica una directiva personalizada en una conexión, Azure VPN Gateway solo utilizará la directiva en la conexión, no solo como iniciador de IKE sino también como respondedor de IKE.

<a id="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected" class="xliff"></a>

### Si quito una directiva de IPsec o IKE personalizada, ¿se que la conexión desprotegida?
No, IPsec o IKE seguirán protegiendo la protección. Una vez que se quite la directiva personalizada de una conexión, Azure VPN Gateway volverá a la [lista predeterminada de las propuestas de IPsec o IKE](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) y volverá a iniciar el protocolo de enlace de IKE con un dispositivo VPN local.

<a id="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection" class="xliff"></a>

### ¿Afectarían a mi conexión VPN la incorporación o actualización de una directiva de IPsec o IKE?
Sí, podría producirse una pequeña interrupción (unos segundos), ya que Azure VPN Gateway anulará la conexión existente y volverá a iniciar el protocolo de enlace de IKE para restablecer el túnel IPsec con los nuevos parámetros y algoritmos criptográficos. Asegúrese de que el dispositivo VPN local también se configura con los algoritmos y niveles de claves coincidentes para minimizar dicha interrupción.

<a id="can-i-use-different-policies-on-different-connections" class="xliff"></a>

### ¿Se pueden usar distintas directivas en conexiones diferentes?
Sí. La directiva personalizada se aplica en función de la conexión. Puede crear y aplicar distintas directivas de IPsec o IKE en conexiones diferentes. También puede elegir aplicar directivas personalizadas a un subconjunto de las conexiones. Las restantes usarán los conjuntos de directivas de IPsec o IKE predeterminados de Azure.

<a id="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well" class="xliff"></a>

### ¿Se puedo usar la directiva personalizada también en una conexión entre redes virtuales?
Sí, puede aplicar directivas personalizadas en las conexiones entre entornos de IPsec o las conexiones entre redes virtuales.

<a id="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources" class="xliff"></a>

### ¿Es preciso especificar la misma directiva en los dos recursos de la conexión entre redes virtuales?
Sí. Un túnel entre redes virtuales consta de dos recursos de conexión en Azure, una para cada dirección. Es preciso asegurarse de que los dos recursos de la conexión tienen la misma directiva, ya que si no es así, la conexión entre redes virtuales no se establecerá.

<a id="does-custom-ipsecike-policy-work-on-expressroute-connection" class="xliff"></a>

### ¿Funciona la directiva de IPsec o IKE personalizada en una conexión ExpressRoute?
No. La directiva de IPsec o IKE solo funciona en conexiones entre redes virtuales a través de las puertas de enlace de VPN de Azure y VPN de S2S.
