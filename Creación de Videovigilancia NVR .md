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

## Instalación de Docker

Ahora antes de comenzar ca instalación de docker temos que facer unha cousa a cal é descargar docker. Para iso imos usar o script que nos proporciona docker.

```bash
curl -sSL https://get.docker.com | sh
```

Agora engadimos o noso usuario ao grupo de docker para non ter que usar sudo.

```bash
sudo usermod -aG docker $USER
```

Agora reiniciamos a raspberry pi 5 para que se apliquen os cambios.

```bash
sudo reboot
```

Agora conectámonos por SSH á raspberry pi 5.

```bash
ssh [usuario]@[direccion IP]
```

Ahora comprobamos que docker se descargou correctamente.

```bash
groups
```

Se nos aparece "docker" na lista de grupos, significa que docker se descargou correctamente.

E para verificalo ainda máis, podemos executar o comando

```bash
docker run hello-world
```

Se nos aparece "Hello from Docker!" na pantalla, significa que docker se descargou correctamente.

Ahora o que vamos a facer é crear unha carpeta para gardar os datos de docker.

## Instalación de frigate

Ahora imos instalar frigate. Primeiro imos crear unha carpeta para gardar os datos de frigate.

```bash
mkdir frigate-nvr
```

Ahora entramos na carpeta.

```bash
cd frigate-nvr
```

Facemos unha carpeta a cal se vai chamar storage a cal vai gardar os videos, imaxes e a información de frigate.

```bash
mkdir storage
```

Agora creamos un ficheiro chamado config.yml a cal vai ter toda a información das camaras. No meu caso vou usar unha cámara IP TP-Link Tapo C200. A cal para conectarse tuven que crearlle unha conta para poder acceder a ela desde a raspberry pi 5.

Ahora o que toca e poñerlle a seguinte configuración ao ficheiro config.yml:

```yaml
mqtt:
  enabled: false

ffmpeg:
  hwaccel_args: preset-rpi-64-h264

birdseye:
  enabled: true
  mode: continuous

objects:
  track:
    - person

cameras:
  escalera:
    ffmpeg:
      inputs:
        - path: rtsp://(Usuario da camara):(Contraseña da camara)@(IP da camara):554/stream1
          roles:
            - detect
            - record
    detect:
      enabled: true
      width: 1280
      height: 720
    record:
      enabled: true
      retain:
        days: 7
        mode: motion

version: 0.14
```
