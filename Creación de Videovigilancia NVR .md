# Introducción

Neste manual vou ensinar como facer para ter un servidor de vixilancia NVR nunha Raspberry Pi 5 co sistema operativo Raspberry Pi OS Lite sen ningunha interface gráfica.

# Requisitos

- Raspberry Pi 5
- Tarxeta SD de 16GB ou máis
- Fonte de alimentación de 5V e 5A
- Cámara IP
- Cabo Ethernet (nun principio é necesario pero máis adiante imos configurar a wifi)
- [Radxa Penta SATA HAT](https://es.aliexpress.com/item/1005007245905606.html?spm=a2g0o.order_list.order_list_main.14.4069194d6qVDD7&gatewayAdapt=glo2esp)
- [Radxa Power DC12 36W](https://es.aliexpress.com/item/1005007226087758.html?gatewayAdapt=glo2esp)
- Disco duro de 2.5"

# Instalación do sistema operativo

Para instalar o sistema operativo na Raspberry Pi 5 imos usar o Raspberry Pi Imager. Collemos a tarxeta SD e metémola no ordenador, abrimos o Raspberry Pi Imager e seleccionamos a imaxe que queremos instalar. Neste caso, imos instalar o Raspberry Pi OS Lite (64-bit). E configuramos o nome de usuario, o contrasinal e despois a wifi. Despois só quedaría instalar o sistema operativo.

# Configuración do sistema operativo

Metemos a SD na Raspberry Pi 5 e acendémola. Agora conectámonos por SSH á Raspberry Pi 5.
Facendo ping ao nome da Raspberry Pi 5, no meu caso o nome da Raspberry Pi 5 é "Raspberrypi".

```bash
ping [Nome da Raspberry]
```

E agora conectámonos por SSH á Raspberry Pi 5.

```bash
ssh [usuario]@[direccion IP]
```

Agora, dentro do SSH, actualizamos o sistema operativo.

```bash
sudo apt update
sudo apt upgrade
```

## Instalación de Docker

Antes de comezar coa instalación de Docker, temos que descargalo. Para iso imos usar o script que nos proporciona Docker.

```bash
curl -sSL https://get.docker.com | sh
```

Engadimos o noso usuario ao grupo de Docker para non ter que usar sudo.

```bash
sudo usermod -aG docker $USER
```

Reiniciamos a Raspberry Pi 5 para que se apliquen os cambios.

```bash
sudo reboot
```

Conectámonos por SSH á Raspberry Pi 5.

```bash
ssh [usuario]@[direccion IP]
```

Comprobamos que Docker se instalou correctamente.

```bash
groups
```

Se nos aparece "docker" na lista de grupos, significa que Docker se instalou correctamente.

E para verificalo aínda máis, podemos executar o comando:

```bash
docker run hello-world
```

Se nos aparece "Hello from Docker!" na pantalla, significa que Docker se instalou correctamente.

Agora o que imos facer é crear un cartafol para gardar os datos de Docker.

## Instalación de Frigate

Para instalar Frigate, primeiro imos crear un cartafol para gardar os datos de Frigate.

```bash
mkdir frigate-nvr
```

Agora entramos no cartafol.

```bash
cd frigate-nvr
```

Creamos un cartafol chamado "storage" onde se gardarán os vídeos, as imaxes e a información de Frigate.

```bash
mkdir storage
```

Creamos un ficheiro chamado config.yml que vai ter toda a información das cámaras. No meu caso vou usar unha cámara IP TP-Link Tapo C200. Para conectarme tiven que crearlle unha conta para poder acceder a ela desde a Raspberry Pi 5.

Agora o que toca é poñerlle a seguinte configuración ao ficheiro config.yml:

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
  escaleira: # Isto pódelo modificar para poñerlle o nome que queiras á cámara
    ffmpeg:
      inputs:
        - path: rtsp://(Usuario da cámara):(Contrasinal da cámara)@(IP da cámara):554/stream1
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

Se queres poñer máis cámaras, só tes que engadir o seguinte código dentro do apartado de cameras:

```yaml
   escaleira: # Isto pódelo modificar para poñerlle o nome que queiras á cámara
    ffmpeg:
      inputs:
        - path: rtsp://(Usuario da cámara):(Contrasinal da cámara)@(IP da cámara):554/stream1
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

Despois de facer o ficheiro config.yml, o que imos facer é crear un ficheiro docker-compose.yml para poder executar Frigate. Para iso imos usar o seguinte comando:

```bash
nano docker-compose.yml
```

E dentro deste ficheiro imos poñer o seguinte:

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
      - /home/<o_teu_usuario>/frigate-nvr/config.yml:/config/config.yml
      - /home/<o_teu_usuario>/frigate-nvr/storage:/media/frigate
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
      FRIGATE_RTSP_PASSWORD: "<o_teu_contrasinal>"
```

Agora con todo feito, o que queda é executar o comando:

```bash
docker-compose up -d
```

E xa temos Frigate funcionando.

Por último, para acceder a Frigate temos que ir a [IP da Raspberry Pi]:5000 e xa nos deixará entrar en Frigate.

# Máis configuracións para máis comodidade coa Raspberry Pi 5

Agora o que vou ensinar aquí é como facer para ver as temperaturas e as porcentaxes de uso de memoria da Raspberry, despois como acceder a ela desde calquera sitio e, por último, como facer para que a Raspberry funcione coa wifi e non co cabo Ethernet.

## Ver as temperaturas e o uso de todas as memorias na Raspberry Pi 5

Para ver as temperaturas e as memorias da Raspberry Pi 5 imos usar Beszel, un software que nos vai facilitar moita información importante como a porcentaxe de uso da CPU, a súa temperatura e a porcentaxe de uso da memoria RAM. Para instalalo, imos engadir o seguinte ao ficheiro docker-compose.yml:

```yaml
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

Accedemos ao enderezo coa IP da Raspberry e o porto 8090. E xa nos deixará entrar en Beszel.

Agora vainos dar un código que vai ser moi parecido ao que utilizamos anteriormente, así que o que imos facer é copiar e pegar só a parte que non temos.

E con todo iso xa vai funcionar perfectamente Beszel.

## Acceso remoto á Raspberry

Neste punto vou ensinar como facer para acceder á Raspberry desde calquera sitio. Para iso imos utilizar Tailscale, que é unha VPN que nos vai permitir acceder á Raspberry desde calquera sitio sen estar dentro da mesma rede.

Para instalalo, primeiro temos que executar o seguinte comando para descargalo:

```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

Despois de descargalo, imos iniciar Tailscale co seguinte comando:

```bash
sudo tailscale up
```

Agora vainos dar unha ligazón que temos que copiar e pegar no navegador para iniciar sesión en Tailscale. Despois teremos que descargar a aplicación de Tailscale no dispositivo desde o que queiramos acceder, iniciar sesión e xa poderemos acceder á Raspberry desde calquera sitio.
