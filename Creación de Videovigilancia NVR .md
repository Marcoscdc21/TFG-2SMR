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
  escalera: # Esto podelo modificar para poñerlle o nome que queiras á cámara
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

Se queres poñer mais camaras só tes que engadir o seguinte código dentro do apartadode cameras:

```yaml
   escalera: # Esto podelo modificar para poñerlle o nome que queiras á cámara
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
```

Despois de facer o ficheiro config.yml, o que vamos a facer é crear un ficheiro docker-compose.yml para poder executar frigate. Para eso imos usar o seguinte comando:

```bash
mkdir docker-compose.yml
```

 E dentro de este ficheiro imos poñer o seguinte:

```yml
version: "3.9"
services:
  frigate:
    container_name: frigate
    privileged: true 
    restart: unless-stopped
    image: ghcr.io/blakeblackshear/frigate:stable
    shm_size: "64mb"
    devices:
      - /dev/bus/usb:/dev/bus/usb
      - /dev/video10 
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /home/TuUsuario>/frigate-nvr/config.yml:/config/config.yml
      - /home/<TuUsuario>/frigate-nvr/storage:/media/frigate
      - type: tmpfs 
        target: /tmp/cache
        tmpfs:
          size: 1000000000
    ports:
      - "5000:5000"
      - "8554:8554" 
      - "8555:8555/tcp" 
      - "8555:8555/udp" 
    environment:
      FRIGATE_RTSP_PASSWORD: "<TUContraseña>"
```

Ahora con todo feito o que queda e executar o comando:

```bash
docker-compose up -d
```

E xa temos frigate funcionando.

Ahora para acceder a frigate temos que ir a [IP da Raspberry Pi]:5000 e xa nos deixaría entrar no frigate

# Máis configuracións para mais comodidade ca rapsberry pi 5

Ahora o que vou ensinar aquí é como facer para ver as temperaturas e todo da raspberry, despois para como acceder a ela desde calquera sitio e por último como facer para que a raspberry funcione co wifi e non co cable ethernet.

## Ver temperaturas e todo da raspberry pi 5

Para ver as temperaturas e todo da raspberry pi 5 imos usar beszel o cal é un software que nos vai facilitar moita información. Para instalarlo imos usar o seguinte comando na carpeta de docker-compose.yml:

```bash
services:
  beszel:
    image: henrygd/beszel:latest
    container_name: beszel
    restart: unless-stopped
    ports:
      - "8090:8090"
    volumes:
      - ${PATH_TO_APPDATA}/beszel/data:/beszel_data
```

Ahora accedemos o lugar ca ip da raspberry e o puerto 8090. E xa nos deixaría entrar no beszel

Ahora vainos entregar un código que vai ser moi parecido ao cal utilizamos anteriormente asique o que imos facer é copiar e pegar solo a parte que non temos.

E con todo eso xa vai funcionar perfectamente beszel

## Acceso remoto a raspberry

En este punto vou enseñar como facer para acceder a raspberry desde calquera sitio. Para eso vamos utilizar tailscale o cal é unha VPN que nos vai permitir acceder a raspberry desde calquera sitio sin estar dentro da mesma rede.

Para instalalo primeiro imos ter que poñer o seguinte comando para descargalo:

```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

Despois de descargalo vamos iniciar tailscale co seguinte comando:

```bash
sudo tailscale up
```

Ahora nos vai dar un enlace o cal temos que copiar e pegar no navegador para iniciar sesión en tailscale e ahora teríamos que descargar a aplicación de tailacle no dispositivo que queiramos acceder, iniciar sesión e xa poderíamos acceder a raspberry desde calquera sitio.

## Facer para que a raspberry funcione co wifi

Para facer para que a raspberry funcione co wifi imos usar o comando:
