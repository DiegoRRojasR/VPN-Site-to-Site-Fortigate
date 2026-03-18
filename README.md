# Implementación de VPN Site-To-Site en FortiGate sobre GNS3

## Video demostrativo

[![Ver video en YouTube](https://img.youtube.com/vi/uGWhksBMckg/maxresdefault.jpg)](https://youtu.be/uGWhksBMckg)

---

## I-) Introducción y Objetivo de la Solución
El presente informe técnico documenta el diseño, implementación y validación de una topología de red basada en una VPN Site-to-Site IPsec utilizando dos dispositivos FortiGate-VM y un router ISP intermedio dentro de un entorno virtualizado en GNS3. La práctica fue desarrollada con el propósito de interconectar dos redes LAN ubicadas en sedes distintas, garantizando comunicación segura entre ambas a través de un túnel cifrado IPsec.
La solución fue diseñada para cumplir con los requerimientos planteados en la asignación, los cuales incluyen la construcción de una topología compuesta por dos FortiGate, un router ISP y dos redes LAN, la configuración de conectividad entre ambas sedes mediante una VPN Site-to-Site, la utilización de un esquema de direccionamiento IP basado en la matrícula 2024-0918, y la validación del funcionamiento mediante pruebas de ping y traceroute entre hosts ubicados en redes remotas.
Desde el punto de vista técnico, la implementación permite demostrar el uso de FortiGate como dispositivo de seguridad y enrutamiento VPN, así como la correcta integración entre conectividad WAN, políticas de firewall, rutas estáticas y negociación IPsec, todo ello dentro de un escenario de laboratorio controlado.

<img width="785" height="648" alt="Screenshot 2026-03-18 161657" src="https://github.com/user-attachments/assets/6adb4ed9-f648-446d-a2ed-b88b4badc1a8" />

---

## II-) Topología Implementada, Interfaces y Direccionamiento IP

La topología implementada se compone de dos sedes lógicas conectadas a través de un router ISP central. En la sede A se desplegó un FortiGate izquierdo, conectado por una interfaz WAN hacia el router ISP y por una interfaz LAN hacia el host PC1. En la sede B se desplegó un FortiGate derecho, también conectado por una interfaz WAN hacia el router ISP y por una interfaz LAN hacia el host PC2. Ambos FortiGate fueron configurados como extremos del túnel VPN Site-to-Site.
El direccionamiento IP utilizado fue elaborado tomando como referencia la matrícula 2024-0918, empleando los valores 24, 09 y 18 como base para construir los segmentos de red del laboratorio. De esta manera, la sede A fue configurada con la red 192.168.24.0/24, mientras que la sede B fue configurada con la red 192.168.18.0/24. Para los enlaces WAN se utilizaron las subredes 10.24.9.0/30 y 10.24.9.4/30.

<img width="656" height="126" alt="Screenshot 2026-03-18 162137" src="https://github.com/user-attachments/assets/853488ed-2214-4e53-8d75-82e5c346678d" />

En el FortiGate de la sede A, la interfaz port1 fue utilizada como interfaz WAN con la dirección 10.24.9.2/30, mientras que la interfaz port2 fue configurada como interfaz LAN con la dirección 192.168.24.1/24. 

<img width="490" height="398" alt="Screenshot 2026-03-18 173154" src="https://github.com/user-attachments/assets/b070c341-6010-4b73-a746-2901ebbb7b77" />

En el FortiGate de la sede B, la interfaz port1 fue configurada con la dirección 10.24.9.6/30 y la interfaz port2 con la dirección 192.168.18.1/24. El router ISP fue configurado con las direcciones 10.24.9.1/30 en la interfaz conectada a la sede A y 10.24.9.5/30 en la interfaz conectada a la sede B.

<img width="386" height="398" alt="Screenshot 2026-03-18 173257" src="https://github.com/user-attachments/assets/fba9b4c0-1e47-4f7b-94aa-117a9ed723d5" />

Los hosts finales fueron configurados manualmente. PC1 recibió la dirección 192.168.24.10/24 con puerta de enlace 192.168.24.1, 

<img width="239" height="190" alt="Screenshot 2026-03-18 173415" src="https://github.com/user-attachments/assets/d0e5390a-164d-4bf4-b7a0-6f3e5dd5721f" />

Mientras que PC2 recibió la dirección 192.168.18.10/24 con puerta de enlace 192.168.18.1.

<img width="256" height="210" alt="Screenshot 2026-03-18 173442" src="https://github.com/user-attachments/assets/1d00b8f3-ce8e-4676-a3c4-c8126ab0e97c" />

En esta práctica no fue necesario implementar VLANs, ya que cada sede fue representada mediante una interfaz LAN física independiente en su respectivo FortiGate, simplificando el diseño y enfocando la práctica en la conectividad segura entre redes remotas.

---

## III-) Configuración de Enrutamiento y Conectividad Base
Antes de establecer el túnel VPN, fue necesario garantizar la correcta conectividad de capa 3 entre ambos extremos WAN. Para ello, el router ISP fue configurado con dos interfaces activas, una hacia cada FortiGate, asegurando el enrutamiento entre las subredes WAN del laboratorio.

<img width="602" height="107" alt="Screenshot 2026-03-18 173843" src="https://github.com/user-attachments/assets/38fdd085-abdc-4db0-b3ed-21c17af65525" />

En cada FortiGate se configuró una ruta por defecto apuntando al router ISP. En la sede A, la ruta por defecto fue dirigida hacia 10.24.9.1 a través de port1. En la sede B, la ruta por defecto fue dirigida hacia 10.24.9.5 a través de port1. Esta configuración permitió que cada peer IPsec pudiera alcanzar la dirección WAN del extremo opuesto, condición indispensable para la posterior negociación del túnel.

<img width="376" height="178" alt="Screenshot 2026-03-18 173922" src="https://github.com/user-attachments/assets/263e92e6-8196-4a60-a5fc-6fce18390da8" />

<img width="326" height="174" alt="Screenshot 2026-03-18 173944" src="https://github.com/user-attachments/assets/c56c254f-aecc-43ad-8550-c238c4783260" />

Durante la fase de implementación se validó la conectividad WAN mediante pruebas de ping desde cada FortiGate hacia su gateway local y hacia la dirección IP pública del otro FortiGate. Estas pruebas permitieron confirmar que el problema inicial del laboratorio se encontraba en la ausencia de direccionamiento estático sobre port1, situación que fue corregida posteriormente. Una vez aplicada la IP correcta en cada interfaz WAN, la conectividad entre peers quedó completamente funcional.

<img width="442" height="599" alt="Screenshot 2026-03-18 174118" src="https://github.com/user-attachments/assets/39693d63-a5e3-4f3d-8caf-af34e873960a" />

---

## IV-) Implementación de la VPN Site-to-Site IPsec

Una vez validada la conectividad base entre los extremos WAN, se procedió a la implementación de la VPN Site-to-Site IPsec entre ambos FortiGate. La configuración fue realizada utilizando el modo interface-based VPN, definiendo una interfaz lógica de túnel denominada VPN-S2S-0918 en ambos extremos.
 
<img width="252" height="128" alt="Screenshot 2026-03-18 174335" src="https://github.com/user-attachments/assets/dba5c8a8-4305-4afe-885a-9322e881d67f" />

En la Fase 1 de IPsec se configuró como interfaz de transporte la port1 de cada FortiGate, se utilizó IKEv2, propuesta criptográfica aes256-sha256, grupo Diffie-Hellman 14 y una clave precompartida común, definida como VPN-2024-0918. El FortiGate de la sede A fue configurado para establecer el túnel hacia la dirección remota 10.24.9.6, mientras que el FortiGate de la sede B fue configurado con destino remoto 10.24.9.2.

<img width="390" height="170" alt="Screenshot 2026-03-18 174407" src="https://github.com/user-attachments/assets/12466369-894e-4963-ae5e-b2aa5e1e94b9" />

En la Fase 2 se definieron como redes protegidas la LAN de cada sede. En el extremo A se configuró como red origen 192.168.24.0/24 y como red destino 192.168.18.0/24. De manera inversa, en el extremo B se configuró como red origen 192.168.18.0/24 y como red destino 192.168.24.0/24. Esta configuración permitió encapsular y cifrar el tráfico entre ambas redes LAN.

<img width="393" height="164" alt="Screenshot 2026-03-18 174454" src="https://github.com/user-attachments/assets/2f01aa58-2ce3-489d-9526-c1df158dce7c" />

Posteriormente, en ambos equipos se crearon rutas estáticas apuntando la red remota hacia la interfaz lógica del túnel, y se añadieron políticas de firewall en ambos sentidos: LAN hacia VPN y VPN hacia LAN, con acción permitida y NAT deshabilitado. Estas políticas fueron fundamentales para permitir el flujo del tráfico interno entre sedes sin traducción de direcciones.

<img width="402" height="590" alt="Screenshot 2026-03-18 174556" src="https://github.com/user-attachments/assets/71e3079b-cc10-4468-963d-f1cf55e591f7" />

---

## V-) Estado del Túnel VPN y Validación Operacional
Una vez completada la configuración, se verificó el estado del túnel IPsec utilizando los comandos de diagnóstico del FortiGate. La salida de get vpn ipsec tunnel summary mostró el túnel VPN-S2S-0918 con el valor selectors(total,up): 1/1, lo cual confirma que tanto la Fase 1 como la Fase 2 fueron negociadas correctamente y que el túnel quedó operativo.

<img width="644" height="58" alt="Screenshot 2026-03-18 174807" src="https://github.com/user-attachments/assets/448b9597-07be-4410-8eb7-33a0b66d3316" />

Posteriormente, mediante diagnose vpn tunnel list, se observó la existencia de una asociación de seguridad activa entre los peers 10.24.9.2 y 10.24.9.6, así como la definición correcta de los selectores de tráfico entre las redes 192.168.24.0/24 y 192.168.18.0/24. Adicionalmente, los contadores de paquetes transmitidos y recibidos aumentaron durante las pruebas, confirmando que el túnel no solo estaba levantado, sino que además transportaba tráfico real entre ambas sedes.

<img width="947" height="374" alt="Screenshot 2026-03-18 174858" src="https://github.com/user-attachments/assets/f32daf77-2095-468b-8d80-5c747fe73122" />

---

## VI-) Pruebas de Conectividad entre las Redes LAN

La validación funcional de la topología se realizó mediante pruebas de conectividad extremo a extremo entre los hosts ubicados en cada sede. Desde PC1, configurado con la dirección 192.168.24.10, se ejecutó un ping hacia PC2, con dirección 192.168.18.10, obteniéndose respuesta satisfactoria. De igual forma, desde PC2 se realizó un ping hacia PC1, confirmando conectividad bidireccional a través del túnel VPN.

<img width="438" height="130" alt="Screenshot 2026-03-18 175037" src="https://github.com/user-attachments/assets/f0890420-f911-4feb-974c-8d2e0dfcbe4f" />

<img width="449" height="126" alt="Screenshot 2026-03-18 175051" src="https://github.com/user-attachments/assets/b1ad14df-b836-4cb7-ba0f-9f729fa1613c" />

Estas pruebas permitieron demostrar que las rutas estáticas, las políticas de firewall y la configuración IPsec se encontraban operando de forma correcta, garantizando la comunicación segura entre ambas redes LAN.

---

## VII-) Validación mediante Traceroute

Como parte de los requerimientos de la práctica, también se realizaron pruebas de traceroute entre ambas redes LAN. Desde PC1 hacia 192.168.18.10, el recorrido mostró como primer salto la puerta de enlace local 192.168.24.1, como segundo salto la dirección WAN del FortiGate remoto 10.24.9.6, y finalmente el host de destino 192.168.18.10. De manera equivalente, desde PC2 hacia 192.168.24.10 se observó primero la puerta de enlace 192.168.18.1, luego la IP WAN del FortiGate remoto 10.24.9.2, y finalmente el destino final.

<img width="587" height="99" alt="Screenshot 2026-03-18 175220" src="https://github.com/user-attachments/assets/34d0aaa6-3d06-4203-a068-1ca494a8089e" />

<img width="589" height="98" alt="Screenshot 2026-03-18 175241" src="https://github.com/user-attachments/assets/bc5258bb-a0e8-4503-9fa0-a2e79809a682" />

En ambos casos apareció el mensaje ICMP type 3, code 3, Destination port unreachable, lo cual es un comportamiento esperado cuando se utiliza la utilidad trace de VPCS, ya que esta emplea paquetes UDP hacia puertos no abiertos en el host final. Lejos de representar una falla, este mensaje confirma que el tráfico alcanzó correctamente el destino y que el host respondió.
Por tanto, el traceroute validó no solo la existencia de conectividad, sino también el recorrido lógico del tráfico a través del túnel VPN entre ambas sedes.

---

## VIII-) Validación General de la Solución
La validación integral de la solución permitió comprobar el cumplimiento de todos los objetivos planteados en la práctica. En primer lugar, se confirmó la correcta implementación de la topología dentro de GNS3, con los dos FortiGate, el router ISP y las dos LAN operando conforme al diseño propuesto. En segundo lugar, se validó la conectividad WAN entre los peers IPsec, requisito indispensable para el establecimiento del túnel.
Posteriormente, se verificó el correcto estado de la VPN Site-to-Site mediante los comandos de monitoreo del FortiGate, observando el selector activo 1/1 y contadores de tráfico en incremento. Finalmente, las pruebas de ping y traceroute entre PC1 y PC2 confirmaron que las redes 192.168.24.0/24 y 192.168.18.0/24 lograron comunicarse de forma segura y estable a través del túnel IPsec.
De esta forma, la práctica quedó validada tanto a nivel de configuración como a nivel funcional, demostrando una implementación consistente y profesional dentro del entorno de laboratorio.

---

## IX-) Conclusiones

La implementación desarrollada sobre FortiGate-VM en GNS3 permitió construir una arquitectura funcional de VPN Site-to-Site IPsec entre dos sedes remotas, cumpliendo con todos los requerimientos establecidos en la práctica. La solución integró direccionamiento IP basado en la matrícula, configuración WAN estática, rutas de alcance, políticas de firewall y túneles IPsec correctamente negociados.
Desde el punto de vista técnico, el laboratorio demostró la capacidad del FortiGate para actuar como extremo VPN, proteger el tráfico entre redes remotas y administrar el flujo de comunicaciones mediante políticas y rutas específicas. La validación final confirmó no solo el levantamiento correcto del túnel, sino también el intercambio real de tráfico cifrado entre las redes LAN.
En conclusión, la práctica evidenció una implementación sólida, funcional y alineada con principios de conectividad segura entre sedes, constituyendo una solución profesional y apropiada dentro del entorno virtual utilizado.
