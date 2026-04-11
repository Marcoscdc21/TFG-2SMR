# Manual de Usuario - Sistema de Videovigilancia NVR

## Índice

1. [Descrición do sistema](#descrición-do-sistema)
2. [Que inclúe o sistema](#que-inclúe-o-sistema)
3. [Primeiro arranque](#primeiro-arranque)
4. [Acceder á interface de videovigilancia (Frigate)](#acceder-á-interface-de-videovigilancia-frigate)
5. [Funcionalidades principais](#funcionalidades-principais)
6. [Ver as cámaras en tempo real](#ver-as-cámaras-en-tempo-real)
7. [Revisar gravacións](#revisar-gravacións)
8. [Detección de persoas](#detección-de-persoas)
9. [Acceso remoto desde calquera lugar](#acceso-remoto-desde-calquera-lugar)
10. [Monitorización do sistema (Beszel)](#monitorización-do-sistema-beszel)
11. [Mantemento básico](#mantemento-básico)
---

## Descrición do sistema

O sistema de videovigilancia NVR (Network Video Recorder) é unha solución perfecta e segura baseada nunha **Raspberry Pi 5** que permite:

- Ver en directo as cámaras de seguridade desde calquera dispositivo (ordenador, móbil ou tablet).
- Gravar automaticamente cando se detecta movemento.
- Detectar persoas e outras cousas de forma intelixente grazas á intelixencia artificial.
- Acceder á distancia desde calquera lugar do mundo a través de Internet.
- Almacenar as gravacións durante 7 días de forma automática.

O sistema funciona de forma continua as 24 horas do día, os 7 días da semana, sen necesidade de intervención por parte do usuario.

---

## Que inclúe o sistema

O sistema que recibe está composto polos seguintes elementos:

| Compoñente | Descrición |
|---|---|
| **Raspberry Pi 5** | O servidor central que xestiona todo o sistema |
| **Tarxeta SD de 16GB ou mais** | Contén o sistema operativo xa instalado e configurado |
| **Fonte de alimentación de 5V e 5A** | Alimentación de 5V e 5A para a Raspberry Pi |
| **Cámara(s) IP** | Cámara(s) de videovigilancia conectada(s) á rede |
| **Cable Ethernet** | Para conectar a Raspberry Pi ao router |
| **Radxa Penta SATA HAT** *(opcional)* | Permite conectar discos duros adicionais para almacenamento |
| **Radxa Power DC12 36W** *(opcional)* | Alimentación de 12V e 3A para a Raspberry Pi |
| **Disco duro de 2.5"** *(opcional)* | Para almacenamento extra de gravacións |

---

## Primeiro arranque

### Pasos para poñer en marcha o sistema

1. **Conectar o cable Ethernet** da Raspberry Pi ao seu router.
2. **Conectar a(s) cámara(s) IP** ao router (por cable ou por wifi, segundo o modelo).
3. **Conectar a fonte de alimentación** á Raspberry Pi.
4. **Agardar aproximadamente 2-3 minutos** a que o sistema se inicie completamente.
5. Xa está listo para usar!

> **Importante:** Non desconecte a Raspberry Pi da corrente sen seguir o procedemento adecuado. Unha desconexión brusca pode dañar o sistema.

---

## Acceder á interface de videovigilancia (Frigate)

Para acceder á interface principal de videovigilancia:

1. **Abra un navegador web** en calquera dispositivo conectado á mesma rede.
2. **Escriba na barra de direccións:** `http://[IP da Raspberry Pi]:5000`
   - A dirección IP da Raspberry Pi aparece indicada no seu router.(Para acceder ao ruter vas ter que entrar no buscador e poñer a seguinte dirección: <http://192.168.1.1> e aí dentro xa vai encontrar a raspberry pi ca sua IP)
   - Exemplo para acceder a frigate: `http://192.168.1.100:5000`
3. **Accederá á pantalla principal de Frigate**, onde verá todas as cámaras configuradas.

> **Consello:** Pode gardar esta dirección nos favoritos do navegador para acceder máis rapidamente no futuro.

---

## Funcionalidades principais

### Ver as cámaras en tempo real

Unha vez dentro da interface de Frigate:

- Na **pantalla principal** verá unha vista xeral de todas as cámaras conectadas ao sistema (vista Birdseye).
- **Faga clic nunha cámara** para vela en pantalla completa.
- A imaxe actualízase en tempo real, co mínimo de atraso posible.

### Revisar gravacións

O sistema grava automaticamente cando detecta **movemento**. Para revisar as gravacións:

1. Na interface de Frigate, vaia á sección de **gravacións (Recordings)**.
2. Seleccione a **cámara** da que quere ver as gravacións.
3. Seleccione a **data e hora** que lle interesa.
4. Reproduza o vídeo desexado.

 (As gravacións consérvanse durante **7 días**. Pasado ese tempo, elimínanse automaticamente para liberar espazo de almacenamento.)

### Detección de persoas

O sistema dispón de detección intelixente de persoas. Isto significa que:

- O sistema **distingue entre persoas e outros movementos** (animais, coches, sombras, etc.).
- Cando detecta unha persoa, **marca o evento** e pode revisalo despois na sección de eventos.
- Na sección de **Eventos** de Frigate pode ver un historial de todas as deteccións con:
  - Imaxe capturada
  - Data e hora
  - Cámara na que se detectou
  - Tipo de obxecto detectado

---

## Acceso remoto desde calquera lugar

O sistema está configurado con **Tailscale**, unha VPN que lle permite acceder ás cámaras desde calquera lugar do mundo con conexión a Internet.

### Como configurar o acceso remoto no seu dispositivo

1. **Descargue a aplicación Tailscale** no seu dispositivo:
   - **Android:** [Google Play Store](https://play.google.com/store/apps/details?id=com.tailscale.ipn)
   - **iPhone/iPad:** [App Store](https://apps.apple.com/app/tailscale/id1470499037)
   - **Windows/Mac/Linux:** [tailscale.com/download](https://tailscale.com/download)

2. **Inicie sesión** con unha conta que se lle facilitaría xunto co sistema.

3. **Acceda a Frigate** usando a IP de Tailscale da Raspberry Pi:
   - Abra o navegador e escriba: `http://[IP que ten a raspberry en Tailscale]:5000`
   - A IP de Tailscale da Raspberry Pi está ben indicada na aplicación.

> **Nota:** Tailscale funciona de forma automática e segura. Unha vez configurado, non precisa facer nada máis.

---

## Monitorización do sistema (Beszel)

O sistema inclúe unha ferramenta chamada **Beszel** que permite ver o estado de saúde da Raspberry Pi (temperaturas, uso do procesador, memoria, etc.).

### Como acceder

1. Abra un navegador web no mesmo dispositivo conectado á rede.
2. Escriba na barra de direccións: `http://[IP da Raspberry Pi]:8090`
3. Accederá ao panel de control de Beszel.

### Información que pode consultar

| Dato | Descrición |
|---|---|
| **Temperatura** | Temperatura actual do procesador. Debe estar por debaixo dos 80°C (Precaución con este tipo de temperaturas porque se está a ese nivel por moito tempo pode danar o equipo). |
| **Uso de CPU** | Porcentaxe de uso do procesador. |
| **Uso de memoria RAM** | Cantidade de memoria utilizada. |
| **Almacenamento** | Espazo de disco utilizado e dispoñible. |

---

## Mantemento básico

### Regras xerais

- **Non desconecte a Raspberry Pi** tirando directamente do cable de alimentación. Pode corromper o sistema.
- **Manteña o equipo** nun lugar ventilado e protexido do po.
- **Non tape** as entradas nin salidas de ventilación.
- **Non mova** o equipo mentres estea funcionando se ten un disco duro conectado.

### Como apagar o sistema correctamente

Se precisa apagar o sistema (por exemplo, por unha mudanza ou para limpeza), contacte co servizo de soporte técnico para que o apaguen de forma segura de forma remota.

### Como reiniciar o sistema

Se o sistema non responde ou as cámaras non se ven:

Pode reiniciar a raspberry pi 5 dende o panel de control de Beszel. Para iso, faga clic no botón de reiniciar que se atopa na parte superior dereita da pantalla.

---
