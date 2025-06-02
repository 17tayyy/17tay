---
title: "Convierte Telegram en tu nube privada: gratis, cifrada y sin límites"
date: 2025-06-02
tags: ["cloud", "python", "blog", "telegram"]
categories: ["Proyectos"]
draft: false
cover:
  image: "/images/TgCloud/portada.png"
  alt: "Imagen del post"
  relative: false
---

## Índice

- [¿Por qué Telegram como cloud?](#por-qué-telegram-como-cloud)
- [¿Qué hace TgCloudCLI?](#qué-hace-tgcloudcli)
- [Instalación y configuración](#instalación-y-configuración)
- [Primeros pasos](#primeros-pasos)
- [Ejemplo real](#ejemplo-real)
- [¿Qué hay detrás?](#qué-hay-detras)
- [Roadmap de lo que se viene](#roadmap-de-lo-que-se-viene)
- [Imágenes](#imagenes)
- [Preguntas frecuentes](#preguntas-frecuentes)
- [Seguridad y privacidad](#seguridad-y-privacidad)
- [¿Te mola la idea?](#te-mola-la-idea)

##  Introducción

¿Y si pudieras tener tu propio sistema de almacenamiento en la nube, ilimitado, gratuito y con cifrado opcional para mayor privacidad?

Pues eso mismo se me ocurrió un día trasteando con un proyecto personal —concretamente **TgRAT**, un *command and control* sobre Telegram— cuando vi que se podían mover archivos desde un ordenador a Telegram de forma fácil y rápida. Entonces pensé: *"Tío, esto se podría usar para algo mucho mejor como tu propio sistema de cloud personal"*. Pero lo más bestia es que era **gratis** y, entre comillas, **ilimitado**.

Digo “entre comillas” porque Telegram tiene sus límites: por ejemplo, el tamaño máximo por archivo es de 2 GB. Pero, siendo realistas, eso es más que suficiente para la mayoría de cosas. Aun así, tengo alguna idea en mente para *bypassear* esa limitación… ya iré desarrollándola más adelante.

Total, que con toda esta movida nació **TgCloud**. Al principio era solo una herramienta CLI que convertía Telegram en tu servidor personal de archivos. Y eso es justo lo que te vengo a presentar en este post: **TgCloudCLI**.

Le pongo "CLI" porque esta herramienta es solo la primera parte del proyecto TgCloud. Actualmente también estoy trabajando en una **API** para poder integrarlo en una web y permitir almacenar todo el contenido directamente en Telegram, de forma cómoda y accesible.

**TgCloudCLI** está desarrollada en Python, y te permite subir y descargar archivos desde Telegram, directamente desde tu terminal, sin complicaciones.


## ¿Por qué Telegram como cloud?

Mira, voy a ser sincero: **Telegram lleva quemando pasta desde hace años**. De verdad, no sé ni cómo no han cerrado todavía. Supongo que sobreviven gracias a las donaciones del propio Dúrov (el fundador) y a lo poco que sacan de Telegram Premium.

¿Y por qué digo que queman pasta? Pues básicamente porque te ofrecen un montón de servicios gratis —entre ellos el almacenamiento en la nube— que te permite guardar **teras y teras de datos sin pagar un euro**. Y lo más loco es que **no hay un límite realista** (más allá del tamaño por archivo).

Además, cuentan con:

- APIs brutales
- Bots rapidísimos
- Cero anuncios
- Infraestructura que responde en milisegundos

Parece que están apostando por captar a la mayor cantidad de usuarios posible, y luego monetizar vendiendo pequeños productos o servicios (como Premium).

**La conclusión es que Telegram no está pensado como un servicio de cloud, así que hay que ser un poco ingenioso...**  
Y ahí es donde entra **TgCloud**.

## ¿Qué hace TgCloudCLI?

**TgCloudCLI** es una herramienta en línea de comandos que te permite gestionar múltiples archivos usando **carpetas virtuales** de forma organizada. Puedes:

- Subir archivos
- Descargar archivos
- Crear carpetas
- Todo esto, directamente desde Telegram

Lo bueno de TgCloudCLI es que es **muy sencilla de usar**, rápida y, lo mejor de todo: **gratuita e ilimitada** (dentro de los límites de Telegram).

> ⚠️ Por ahora, está centrado en funcionalidades básicas. Estoy trabajando en una **API REST** y, más adelante, una **interfaz web** para gestionar los archivos desde cualquier navegador.

## 🛠️ Instalación y configuración

Configurar **TgCloudCLI** es muy fácil. Solo necesitas Python 3.8 o superior, una cuenta de Telegram y ganas de exprimir tu terminal.

### 1. Clona el repositorio

```bash
git clone https://github.com/17tayyy/TgCloudCLI.git
cd TgCloudCLI
```

### 2. (Opcional) Crea un entorno virtual

No es obligatorio, pero recomendable:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Instala las dependencias

```bash
pip install -r requirements.txt
```

### 4. Añade tus credenciales de Telegram

Crea un archivo `config.json` con este formato:

```json
{
  "api_id": 123456,
  "api_hash": "tu_api_hash",
  "chat_id": 123456789
}
```

- El `api_id` y `api_hash` los sacas desde [my.telegram.org](https://my.telegram.org).
- El `chat_id` es donde quieres almacenar los archivos (puede ser tuyo o un grupo/canal privado) tengo un mini tutorial de como sacar el chat_id de un grupo dentro de el post de [TgRAT](https://17tay.pages.dev/posts/post-c2-sobre-telegram/#crear-grupo).

---

## Primeros pasos

Lanza la herramienta con:

```bash
python3 TgCloud.py
```

Una vez dentro, puedes usar comandos como (para ver el menú puedes usar 'help'):

| Comando         | Descripción                                  |
|-----------------|----------------------------------------------|
| `ls`            | Lista archivos o carpetas                    |
| `cd <folder>`   | Entra en una carpeta                         |
| `mkdir <name>`  | Crea una nueva carpeta virtual               |
| `put <archivo>` | Sube un archivo a Telegram                   |
| `get <archivo>` | Descarga un archivo                         |
| `rm <archivo>`  | Elimina un archivo del sistema               |
| `encryption`    | Activa/desactiva el cifrado de archivos      |
| `exit`          | Salir del sistema                            |

> 🛡️ Si activas el modo `encryption`, todos los archivos se cifrarán antes de subirse y se descifrarán al descargarse. La clave se guarda localmente en `encryption.key`.

---

## Ejemplo real

```shell
TgCloud/ > mkdir backups
TgCloud/ > cd backups
TgCloud/backups > encryption
[+] Encryption enabled
TgCloud/backups > put secret.zip
[+] Encrypting file before upload...
[+] Uploading secret.zip [#######---------] 60%
[+] Uploaded 'secret.zip'
TgCloud/backups > get secret.zip
[+] Downloading secret.zip [##########-----] 80%
[+] Decrypting file after download...
[+] Downloaded: secret.zip
```

---

Con esto ya tienes tu propio sistema de almacenamiento privado, en la nube, cifrado y gratis.  
Y lo mejor: lo controlas tú.

---

## ¿Qué hay detras?

TgCloudCLI esta escrito en python usando la libreria Telethon para manejar toda la comunicacion con telegram. la configuración se guarda localmente y se pueden usar multiples cuentas o canales para distribuir la carga

> 📁 Código completo en GitHub:  
> 👉 [github.com/17tayyy/TgCloudCLI](https://github.com/17tayyy/TgCloudCLI)

---

## Roadmap de lo que se viene

Esto no es solo una herramienta de consola. El proyecto completo **TgCloud** incluirá:

- CLI funcional para uso básico (ya disponible)
    
- API REST en desarrollo (con FastAPI + Telethon)
    
- Panel web para gestionar archivos desde el navegador
    
- Cifrado opcional
    
- Compartición de archivos
    
- Algoritmo de compresión/adaptación para ficheros pesados

---

## ¿Te mola la idea? 

Si te interesa el proyecto, **échale un vistazo al repo**, sugiéreme mejoras o abre issues.  
Todo está en código abierto y me flipa recibir feedback, ideas o incluso pull requests.

> 💬 ¿Y tú? ¿Usarías Telegram como tu propio Google Drive?

---

## Imagenes

![Imagen1](/images/TgCloud/image.png)

---
![Imagen2](/images/TgCloud/image2.png)

---

![Imagen3](/images/TgCloud/image3.png)



## Preguntas frecuentes

**¿Puedo usar TgCloudCLI con Telegram Premium?**  
Sí, el límite de tamaño de archivo aumenta a 4GB en cuentas Premium, lo que mejora mucho la experiencia.

**¿Qué pasa si borro el chat donde guardo los archivos?**  
Perderás acceso a esos archivos, ya que Telegram los elimina. Ten cuidado de no borrar el chat o canal.

**¿Se puede compartir el almacenamiento con otros usuarios?**  
Por ahora no, TgCloudCLI es para uso personal, pero la API futura abrirá opciones de colaboración.

**¿Cómo aseguro mis archivos?**  
Puedes activar el cifrado para proteger tus archivos con una clave local antes de subirlos (se genera sola al usar el modo de encriptación).

## Seguridad y privacidad

Aunque Telegram ofrece una solución muy práctica y potente para almacenamiento, hay que tener en cuenta algunos riesgos:

- Los **metadatos** de los archivos (nombres, tamaños, fechas, chat donde se guardan) quedan almacenados en Telegram, y pueden ser accesibles por ellos o en caso de brechas de seguridad.
- El cifrado que implementa **TgCloudCLI** es local y opcional, protege el contenido de los archivos pero no oculta los metadatos ni la existencia de los mismos.
- Por eso, si manejas información sensible, **te recomiendo siempre activar el cifrado** antes de subir cualquier archivo.
- Ten en cuenta también la seguridad de tu cuenta Telegram y la privacidad del chat o canal donde almacenes los datos.

Esta opción de cifrado añade una capa extra para que solo tú puedas leer los archivos, usando una clave guardada localmente y nunca compartida en Telegram. Así que aunque alguien acceda a tu cuenta, sin la clave no podrá abrir tus archivos cifrados.

---

## Despedida

Gracias por leer hasta aquí.  
Esto es solo el principio de lo que quiero montar con TgCloud.  
Si te mola el rollo, ¡sígueme y únete al viaje!

### Sigue el proyecto

👉 Sígueme en [GitHub](https://github.com/17tayyy) para no perderte actualizaciones.   
📥 ¿Ideas, bugs o mejoras? ¡Abre un issue o haz una pull request!

---


