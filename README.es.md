<h1 align="center">QitQode</h1>

<p align="center"><strong>Agente de IA para programar en la terminal, con memoria.</strong></p>

<p align="center">
  <a href="https://qitqode.com">Sitio web</a>
</p>

<p align="center">
  <a href="./README.md">English</a> | <a href="./README.zh.md">简体中文</a> | <a href="./README.zht.md">繁體中文</a> | <a href="./README.ja.md">日本語</a> | <a href="./README.fr.md">Français</a> | <a href="./README.ru.md">Русский</a> | <strong>Español</strong> | <a href="./README.pt.md">Português</a>
</p>

---

La mayoría de los agentes de programación olvidan todo en cuanto termina una sesión. QitQode no. Lee y escribe código, ejecuta comandos, gestiona Git — y mantiene una memoria persistente y consultable de tu proyecto entre sesiones, reconstruyendo su propio contexto cuando la conversación se alarga para poder seguir trabajando en lugar de empezar de cero.

Una sola cuenta, ocho capacidades — **Free (no cost)**, **Adaptive**, **Fast**, **Economy**, **Planner**, **Repair**, **Max intelligence**. El pipeline **Orchestrated** es una capacidad separada de Qortex para el flujo multi-etapa gate → plan → build → repair, no una selección interactiva normal. Sin paneles de proveedores, sin malabares con claves de API, sin hojas de cálculo de facturación por modelo.

---

## Inicio rápido

```bash
npm install -g @qitqode/cli
# or: bun add --global @qitqode/cli

# Run
qitqode
```

El primer arranque te guía en el inicio de sesión:

- **Sign in with QitQode** — un flujo de código de dispositivo que funciona en todas partes, incluidas sesiones SSH y entornos aislados remotos: la CLI muestra una URL de verificación y un código (y abre tu navegador cuando hay uno disponible); apruébalo desde cualquier dispositivo para terminar
- **API key** — pega una clave de API de QitQode en su lugar

Luego elige un nivel en el selector de modelos y ponte a trabajar. Esa es toda la configuración.

### Usa QitQode en Español

La TUI detecta automáticamente el idioma de tu sistema. Para cambiarlo manualmente, ejecuta `/language` (o `/lang`) dentro de QitQode y elige Español de la lista.

<details>
<summary><strong>WSL: problemas con el portapapeles</strong></summary>

Si ves texto ilegible al copiar en WSL, instala `xsel`:

```bash
sudo apt install xsel
```

</details>

---

## Por qué QitQode

No necesitas otra envoltura de chat. Necesitas un agente capaz de sostener un trabajo largo sin perder el hilo. QitQode se apoya en cuatro mecanismos:

### 1. Memoria que sobrevive a la sesión

Cada proyecto obtiene una capa de memoria persistente respaldada por la búsqueda de texto completo de SQLite: conocimiento del proyecto en `MEMORY.md`, puntos de control automáticos de la sesión, notas rápidas y registros de progreso por tarea. Al reanudar, la memoria relevante se inyecta automáticamente — clasificada y ajustada a un presupuesto de tokens, no volcada sin más. El agente retoma donde lo dejó en lugar de volver a aprender tu base de código.

### 2. Contexto que se reconstruye solo

Las tareas largas rebasan las ventanas de contexto. QitQode vigila la ventana, guarda puntos de control del estado antes de que se llene y reconstruye el contexto de trabajo a partir del último punto de control, la memoria del proyecto y el progreso de la tarea — así una refactorización de varias horas no muere al llegar al límite de tokens.

### 3. Autonomía de la que puedes pedir cuentas

Define una condición de parada con `/goal`. Cuando el agente cree que ha terminado, un modelo juez independiente revisa la conversación y decide si el objetivo se ha cumplido de verdad — se acabaron los optimistas «¡listo!» a mitad del trabajo. Combínalo con el rastreador de tareas en forma de árbol (`T1`, `T1.1`, …) y los subagentes en paralelo para un trabajo desatendido real.

### 4. Una suscripción, cero fontanería de proveedores

Siete capacidades interactivas, un solo inicio de sesión. Cambia de capacidad a mitad de sesión con `/free`, `/fast`, `/economical`, `/adaptive`, `/planner`, `/repair` o `/max-int`. La capacidad `/orchestrated` está reservada para el camino de pipeline de Qortex en lugar de turnos interactivos ordinarios.

### Y las partes que otros dejan fuera

- **Credenciales cifradas en reposo** — tus tokens de autenticación se sellan con una clave guardada en el keyring del sistema operativo, y las variables de entorno con credenciales se eliminan por defecto de cada proceso hijo que lanza el agente.
- **Una TUI que todos pueden usar** — modo accesible para lectores de pantalla, compatibilidad con `NO_COLOR`, movimiento reducido, control del nivel de detalle de los anuncios y un tema de alto contraste conforme a WCAG AA. De primera clase, no añadido a última hora. (Detalles más abajo.)
- **Licencia MIT sin más** — sin un archivo aparte de restricciones de uso, sin términos de servicio enterrados al final del README.
- **Inicio de sesión con código de dispositivo pensado para máquinas reales** — funciona por SSH, en contenedores y en entornos aislados remotos donde una redirección del navegador por loopback nunca podría.

---

## Funciones principales

### Múltiples agentes

| Agente      | Descripción                                                                 |
| ----------- | --------------------------------------------------------------------------- |
| **build**   | Por defecto. Permisos completos de herramientas para el desarrollo          |
| **plan**    | Modo de análisis de solo lectura para explorar código y diseñar soluciones  |
| **compose** | Modo de orquestación para desarrollo guiado por especificaciones y flujos basados en habilidades |

Pulsa `Alt+M` para alternar entre los agentes principales. El sistema crea subagentes según los necesita.

### Memoria persistente

Memoria entre sesiones basada en la búsqueda de texto completo SQLite FTS5:

- **Memoria del proyecto** (`MEMORY.md`) — conocimiento persistente del proyecto, reglas y decisiones de arquitectura
- **Punto de control de sesión** (`checkpoint.md`) — instantáneas estructuradas del estado que mantiene automáticamente el subagente checkpoint-writer
- **Notas rápidas** (`notes.md`) — zona de notas temporal para los agentes
- **Progreso de tareas** (`tasks/<id>/progress.md`) — registros por tarea

La memoria se inyecta automáticamente cuando se reanuda una sesión, de modo que el agente no necesita volver a aprender el contexto del proyecto.

### Gestión inteligente del contexto

- **Puntos de control automáticos** — decide cuándo guardar el estado de la sesión según la ventana de contexto del modelo
- **Reconstrucción del contexto** — cuando el contexto se acerca al límite, lo reconstruye a partir del último punto de control, la memoria del proyecto, el progreso de la tarea y los mensajes recientes conservados, para que el agente pueda continuar la tarea actual
- **Inyección presupuestada** — usa un presupuesto de tokens para controlar cuánto contenido de puntos de control, memoria y notas entra en el contexto, con clasificación por importancia

### Seguimiento de tareas

Un sistema de tareas en forma de árbol (`T1`, `T1.1`, `T1.2`, …) que se integra automáticamente con el sistema de puntos de control, de modo que el progreso de las tareas se conserva al reanudar sesiones.

### Sistema de subagentes

El agente principal puede crear subagentes bajo demanda. Los subagentes comparten el contexto de la sesión actual y pueden trabajar en paralelo, con seguimiento del ciclo de vida, cancelación y ejecución en segundo plano.

### Objetivo / Condición de parada

El comando `/goal` establece una condición de parada para una sesión. Cuando el agente intenta detenerse, un modelo juez independiente evalúa la conversación para decidir si la condición se cumple realmente — evitando las «paradas optimistas» prematuras durante el trabajo autónomo.

### Modo Compose

El modo Compose ofrece un flujo de trabajo estructurado para el desarrollo guiado por especificaciones. Incluye habilidades integradas para planificación, ejecución, revisión de código, TDD, depuración, verificación y fusión — orquestando el ciclo de vida completo, desde la especificación hasta el código entregado.

### Predicción de prompts

Sugerencias en texto fantasma en línea que predicen tu próximo prompt mientras trabajas — pulsa `Tab` para aceptarlas.

### Investigación profunda

El flujo integrado `/deep-research` ejecuta una investigación estructurada de varios pasos para preguntas que necesitan más que una sola búsqueda.

### Uso headless e IDE

Ejecuta `qitqode serve` para un servidor HTTP headless, o `qitqode acp` para compatibilidad con Agent Client Protocol y controlar QitQode desde editores compatibles y entornos remotos.

**Ejecuciones desatendidas.** Tres flags deciden cuántas veces la TUI se detiene a preguntarte algo:

| Flag | Preguntas | Permisos de herramientas |
| --- | --- | --- |
| `--never-ask` | auto-decididas | siguen preguntándote |
| `--fullauto` | auto-decididas | aprobados automáticamente, salvo lo que tu configuración deniegue explícitamente |
| `--headless` | auto-decididas | aprobados automáticamente — y sin TUI; requiere `--prompt` o stdin |

`--fullauto` mantiene la TUI interactiva normal: ves la sesión mientras avanza y un distintivo `FULL-AUTO` permanece en el prompt mientras se conceden permisos en tu nombre. Todo lo que marques como `deny` en tu configuración se sigue rechazando. `--fullauto` implica `--never-ask`, y combinarlo con `--headless` es inofensivo (headless ya se comporta así).

### Entrada de voz

Entrada de voz por streaming en tiempo real basada en TenVAD. Actívala con `/voice` y habla — el audio se segmenta por pausas y se transcribe de forma incremental en la entrada. Requiere `sox` (`brew install sox` en macOS, similar en otras plataformas) y un modelo de reconocimiento de voz configurado explícitamente mediante el campo de configuración `voice`.

> **Nota:** Los modelos de programación son exclusivos por nivel a través del backend de QitQode. El campo `voice` es una excepción acotada que se usa únicamente para el reconocimiento de voz y el control por voz — no añade modelos a la lista de modelos de programación.

<details>
<summary><strong>Configuración de audio en WSLg</strong></summary>

```bash
sudo apt install -y sox pulseaudio libasound2-plugins
export PULSE_SERVER=unix:/mnt/wslg/PulseServer
```

</details>

<details>
<summary><strong>Audio remoto por SSH (Mac → host remoto)</strong></summary>

```bash
# Mac (local)
brew install pulseaudio
pulseaudio --load="module-native-protocol-tcp auth-ip-acl=127.0.0.1" --exit-idle-time=-1 --daemonize
# Add to ~/.ssh/config: RemoteForward 4713 127.0.0.1:4713

# Remote host
apt install -y pulseaudio pulseaudio-utils sox
export PULSE_SERVER=tcp:127.0.0.1:4713
# Verify: pactl info
```

</details>

### Dream & Distill

- **`/dream`** — analiza los rastros de sesiones recientes, extrae conocimiento persistente a la memoria del proyecto y elimina las entradas obsoletas
- **`/distill`** — detecta flujos de trabajo manuales repetidos en el trabajo reciente y empaqueta los candidatos de alta confianza en habilidades, subagentes o comandos reutilizables

---

## Configuración

QitQode se configura mediante `.qitqode/qitqode.json` en el directorio del proyecto (o `~/.config/qitqode/qitqode.json` de forma global). Las opciones clave incluyen:

- Selección de la capacidad de Qortex (Free (no cost), Adaptive, Fast, Economy, Planner, Repair, Max intelligence, y el pipeline Orchestrated)
- Permisos de agentes y agentes personalizados
- Comportamiento de puntos de control y memoria
- Conexiones a servidores MCP
- Atajos de teclado y tema

El Max Mode (razonamiento paralelo best-of-N con selección por juez) se puede activar mediante `experimental.maxMode` en la configuración.

---

## Accesibilidad

La TUI de QitQode viene con compatibilidad de accesibilidad de primera clase:

- **Modo accesible** — define `QITQODE_TUI_ACCESSIBLE=1` (o `"tui": { "accessible": true }` en la configuración) para una experiencia compatible con lectores de pantalla: renderizado lineal de la pantalla principal (sin pantalla alternativa), sin captura de ratón, baja tasa de fotogramas, movimiento reducido y sin señales sonoras.
- **NO_COLOR** — cualquier valor no vacío de [`NO_COLOR`](https://no-color.org) cambia a un renderizado monocromo con fondos transparentes. La gravedad nunca se transmite solo con color (los avisos llevan los símbolos `ℹ ✓ ▲ ✗`, los diffs conservan los marcadores `+`/`-`).
- **Reducir movimiento** — define `QITQODE_REDUCE_MOTION=1` (o `"tui": { "reduce_motion": true }`) para reemplazar los spinners y las animaciones con texto estático. También se puede alternar en tiempo de ejecución desde la lista de comandos.
- **Sonido** — desactiva las señales sonoras con `QITQODE_TUI_SOUND=0`, `"tui": { "sound": false }` o el interruptor en tiempo de ejecución de la lista de comandos. El modo accesible siempre desactiva el sonido.
- **Nivel de detalle de los anuncios** — en modo accesible, controla lo detallados que son los anuncios del lector de pantalla con `QITQODE_TUI_ANNOUNCEMENTS=quiet|normal|verbose`, `"tui": { "announcements": "quiet" }` o el selector en tiempo de ejecución de la lista de comandos. `quiet` anuncia solo los límites de turno; `normal` (por defecto) añade líneas de inicio de herramienta; `verbose` añade líneas de finalización de herramienta. Los errores y las cancelaciones se anuncian siempre en todos los niveles.
- **Tema de alto contraste** — selecciona el tema integrado `high-contrast` para superficies en blanco y negro puros con colores conformes a WCAG AA.
- **Terminales pequeñas** — la TUI se degrada de forma controlada en terminales estrechas y muestra un mensaje claro cuando la ventana está por debajo del mínimo de 40x8.
- **Diálogos solo con teclado** — cada diálogo es totalmente operable sin ratón: `Esc` siempre cierra, `Tab` (y las flechas, cuando un diálogo tiene una fila de botones o una lista) mueve el foco, y `Enter` o `Space` activa el control enfocado. En terminales planas o con `NO_COLOR`, la fila de lista resaltada lleva además un marcador `›` para que la selección sea distinguible sin color.

Las variables de entorno de accesibilidad son interruptores de un solo sentido: `QITQODE_TUI_ACCESSIBLE=1`, `QITQODE_REDUCE_MOTION=1`, `NO_COLOR` y `QITQODE_TUI_SOUND=0` siempre prevalecen sobre los valores de configuración y los interruptores en tiempo de ejecución — las garantías de accesibilidad no se pueden volver a desactivar. `QITQODE_TUI_ANNOUNCEMENTS` prevalece de igual modo sobre el valor de configuración y el selector en tiempo de ejecución cuando está definido.

---

## Desarrollo

```bash
bun install              # Install dependencies
bun run dev              # Run in development mode
bun turbo typecheck      # Type check
```

---

## Licencia

El código fuente está licenciado bajo la [Licencia MIT](./LICENSE).
