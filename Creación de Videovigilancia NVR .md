# Introducción

Neste manual vou a ensinar como facer para ter un servidor de vigilacia NVR nunha raspberry pi 5 co sistema opertivo de raspberry pi os lite sin ningunha interface grafica.

# Requisitos

- Raspberry pi 5
- Tarxeta SD de 16GB ou máis
- Fonte de alimentación de 5V e 5A
- Cámara IP
- Cable Ethernet (nun principio é necesario pero máis adiante imos configurar o wifi)
- [Radxa Penta SATA HAT](https://es.aliexpress.com/item/1005007245905606.html?spm=a2g0o.order_list.order_list_main.14.4069194d6qVDD7&gatewayAdapt=glo2esp)
- [Radxa Power DC12 36W](https://es.aliexpress.com/item/1005007226087758.html?gatewayAdapt=glo2esp)
- Disco duro de 2.5"

# Instalación do sistema operativo

Para instalar o sistema operativo na raspberry pi 5 imos usar o Raspberry Pi Imager. Ahora collemos a tarxeta SD e metémola no ordenador. Agora abrimos o Raspberry Pi Imager e seleccionamos a imaxe que queremos instalar. Neste caso, imos instalar o Raspberry Pi OS Lite (64-bit). E configuramos o nome de usuario e a contraseña. Tamén configuramos o wifi. E xa podemos instalar o sistema operativo.

# Configuración do sistema operativo

Hora metemos a SD na raspberry pi 5 e encendémola. Agora conectámonos por SSH á raspberry pi 5.
Facendo ping ao nome da raspberry pi 5. O nome da raspberry pi 5 é "raspberrypi".

```bash
ping [Nombre da Raspberry]
```

E ahora conectámonos por SSH á raspberry pi 5.

```bash
ssh [usuario]@[direccion IP]
```

Ahora dentro do ssh actualizamos o sistema operativo.

```bash
sudo apt update
sudo apt upgrade
```
