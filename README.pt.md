<h1 align="center">QitQode</h1>

<p align="center"><strong>Agente de programação com IA no terminal, com memória.</strong></p>

<p align="center">
  <a href="https://qitqode.com">Website</a>
</p>

<p align="center">
  <a href="./README.md">English</a> | <a href="./README.zh.md">简体中文</a> | <a href="./README.zht.md">繁體中文</a> | <a href="./README.ja.md">日本語</a> | <a href="./README.fr.md">Français</a> | <a href="./README.ru.md">Русский</a> | <a href="./README.es.md">Español</a> | <strong>Português</strong>
</p>

---

A maioria dos agentes de programação esquece tudo assim que a sessão termina. O QitQode não. Ele lê e escreve código, executa comandos, gere o Git — e mantém uma memória persistente e pesquisável do teu projeto entre sessões, reconstruindo o seu próprio contexto quando este se estende para poder continuar a trabalhar em vez de recomeçar do zero.

Uma conta, oito capacidades — **Free (no cost)**, **Adaptive**, **Fast**, **Economy**, **Planner**, **Repair**, **Max intelligence**. O pipeline **Orchestrated** é uma capacidade separada do Qortex para o fluxo multi-etapa gate → plan → build → repair, não uma seleção interativa normal. Sem painéis de fornecedores, sem malabarismos com chaves de API, sem folhas de cálculo de faturação por modelo.

---

## Início Rápido

```bash
npm install -g @qitqode/cli
# or: bun add --global @qitqode/cli

# Run
qitqode
```

O primeiro arranque guia-te no início de sessão:

- **Iniciar sessão com o QitQode** — um fluxo por código de dispositivo que funciona em todo o lado, incluindo sessões SSH e sandboxes remotas: a CLI mostra um URL de verificação e um código (e abre o navegador quando há um disponível); aprova em qualquer dispositivo para concluir
- **Chave de API** — em alternativa, cola uma chave de API do QitQode

Depois escolhe um nível no seletor de modelos e começa a trabalhar. É toda a configuração necessária.

### Usar o QitQode em Português

A TUI deteta automaticamente o idioma do teu sistema. Para mudar manualmente, executa `/language` (ou `/lang`) dentro do QitQode e escolhe Português na lista.

<details>
<summary><strong>WSL: problemas com a área de transferência</strong></summary>

Se encontrares texto corrompido ao copiar no WSL, instala o `xsel`:

```bash
sudo apt install xsel
```

</details>

---

## Porquê o QitQode

Não precisas de mais um invólucro de chat. Precisas de um agente capaz de sustentar um trabalho de longa duração sem perder o fio à meada. O QitQode assenta em quatro mecanismos:

### 1. Memória que sobrevive à sessão

Cada projeto ganha uma camada de memória persistente, suportada por pesquisa de texto completo em SQLite: conhecimento do projeto em `MEMORY.md`, pontos de verificação automáticos da sessão, notas de rascunho e registos de progresso por tarefa. Quando retomas, a memória relevante é injetada automaticamente — ordenada por relevância e limitada por um orçamento de tokens, não despejada de uma vez. O agente continua de onde ficou em vez de reaprender o teu código.

### 2. Contexto que se reconstrói sozinho

Tarefas longas ultrapassam as janelas de contexto. O QitQode vigia a janela, faz um ponto de verificação do estado antes de esta encher e reconstrói o contexto de trabalho a partir do ponto de verificação mais recente, da memória do projeto e do progresso das tarefas — para que uma refatoração de várias horas não morra no limite de tokens.

### 3. Autonomia de que podes pedir contas

Define uma condição de paragem com `/goal`. Quando o agente considera que terminou, um modelo juiz independente revê a conversa e decide se o objetivo foi realmente cumprido — acabaram os "está tudo pronto!" otimistas a meio do trabalho. Combina-o com o registo de tarefas em árvore (`T1`, `T1.1`, …) e com subagentes em paralelo para trabalho verdadeiramente autónomo.

### 4. Uma subscrição, zero canalização de fornecedores

Sete capacidades interativas, um único início de sessão. Muda de capacidade a meio da sessão com `/free`, `/fast`, `/economical`, `/adaptive`, `/planner`, `/repair` ou `/max-int`. A capacidade `/orchestrated` é reservada para o caminho de pipeline do Qortex em vez de turnos interativos ordinários.

### E as partes que os outros deixam de fora

- **Credenciais encriptadas em repouso** — os teus tokens de autenticação são selados com uma chave guardada na keyring do sistema operativo, e as variáveis de ambiente de credenciais são removidas por predefinição de cada processo-filho que o agente cria.
- **Uma TUI que todos podem usar** — modo acessível a leitores de ecrã, suporte a `NO_COLOR`, movimento reduzido, controlo do detalhe dos anúncios e um tema de alto contraste em conformidade com WCAG AA. De raiz, não acrescentado à pressa. (Detalhes abaixo.)
- **Licença MIT simples** — sem ficheiro separado de restrições de uso, sem termos de serviço escondidos no fim do README.
- **Início de sessão por código de dispositivo pensado para máquinas reais** — funciona por SSH, em contentores e em sandboxes remotas onde um redirecionamento de navegador por loopback nunca conseguiria.

---

## Funcionalidades Principais

### Vários Agentes

| Agente      | Descrição                                                                    |
| ----------- | --------------------------------------------------------------------------- |
| **build**   | Predefinido. Permissões totais de ferramentas para desenvolvimento          |
| **plan**    | Modo de análise só de leitura para exploração de código e desenho de soluções |
| **compose** | Modo de orquestração para desenvolvimento orientado a especificações e fluxos orientados a competências |

Prime `Alt+M` para alternar entre os agentes principais. Os subagentes são criados pelo sistema conforme necessário.

### Memória Persistente

Memória entre sessões, alimentada pela pesquisa de texto completo SQLite FTS5:

- **Memória do projeto** (`MEMORY.md`) — conhecimento persistente do projeto, regras e decisões de arquitetura
- **Ponto de verificação da sessão** (`checkpoint.md`) — instantâneos de estado estruturados mantidos automaticamente pelo subagente checkpoint-writer
- **Notas de rascunho** (`notes.md`) — área de notas temporárias para os agentes
- **Progresso das tarefas** (`tasks/<id>/progress.md`) — registos por tarefa

A memória é injetada automaticamente quando uma sessão é retomada, para que o agente não precise de reaprender o contexto do projeto.

### Gestão Inteligente de Contexto

- **Pontos de verificação automáticos** — decide quando guardar o estado da sessão com base na janela de contexto do modelo
- **Reconstrução de contexto** — quando o contexto se aproxima do limite, reconstrói-o a partir do ponto de verificação mais recente, da memória do projeto, do progresso das tarefas e das mensagens recentes retidas, para que o agente possa continuar a tarefa atual
- **Injeção orçamentada** — usa um orçamento de tokens para controlar quanto conteúdo de ponto de verificação, memória e notas entra no contexto, com ordenação por importância

### Acompanhamento de Tarefas

Um sistema de tarefas em árvore (`T1`, `T1.1`, `T1.2`, …) que se integra automaticamente com o sistema de pontos de verificação, para que o progresso das tarefas seja preservado quando as sessões são retomadas.

### Sistema de Subagentes

O agente principal pode criar subagentes a pedido. Os subagentes partilham o contexto da sessão atual e podem trabalhar em paralelo, com acompanhamento do ciclo de vida, cancelamento e execução em segundo plano.

### Objetivo / Condição de Paragem

O comando `/goal` define uma condição de paragem para uma sessão. Quando o agente tenta parar, um modelo juiz independente avalia a conversa para decidir se a condição está realmente satisfeita — evitando "paragens otimistas" prematuras durante o trabalho autónomo.

### Modo Compose

O modo Compose oferece um fluxo de trabalho estruturado para desenvolvimento orientado a especificações. Inclui competências integradas para planeamento, execução, revisão de código, TDD, depuração, verificação e integração — orquestrando todo o ciclo de vida, da especificação ao código entregue.

### Previsão de Prompt

Sugestões inline em texto fantasma preveem o teu próximo prompt à medida que trabalhas — prime `Tab` para aceitar.

### Investigação Aprofundada

O fluxo integrado `/deep-research` conduz uma investigação estruturada em vários passos para perguntas que precisam de mais do que uma única pesquisa.

### Uso Headless e em IDE

Executa `qitqode serve` para um servidor HTTP headless, ou `qitqode acp` para suporte do Agent Client Protocol, permitindo controlar o QitQode a partir de editores compatíveis e ambientes remotos.

**Execuções sem supervisão.** Três flags decidem quantas vezes a TUI para para lhe perguntar algo:

| Flag | Perguntas | Permissões de ferramentas |
| --- | --- | --- |
| `--never-ask` | decididas automaticamente | continuam a ser-lhe pedidas |
| `--fullauto` | decididas automaticamente | aprovadas automaticamente, exceto o que a sua configuração nega explicitamente |
| `--headless` | decididas automaticamente | aprovadas automaticamente — e sem TUI; requer `--prompt` ou stdin |

`--fullauto` mantém a TUI interativa normal: acompanha a sessão enquanto decorre e um selo `FULL-AUTO` fica no prompt enquanto as permissões estiverem a ser concedidas em seu nome. Tudo o que definir como `deny` na configuração continua a ser recusado. `--fullauto` implica `--never-ask`, e combiná-lo com `--headless` é inofensivo (o headless já se comporta assim).

### Entrada por Voz

Entrada de voz em streaming em tempo real, alimentada pelo TenVAD. Ativa com `/voice` e depois fala — o áudio é segmentado pelas pausas e transcrito de forma incremental para a entrada. Requer o `sox` (`brew install sox` no macOS, semelhante noutras plataformas) e um modelo de reconhecimento de fala explicitamente configurado através do campo de configuração `voice`.

> **Nota:** Os modelos de programação são exclusivos dos níveis, através do backend do QitQode. O campo `voice` é uma exceção delimitada, usada unicamente para reconhecimento de fala e controlo por voz — não acrescenta modelos à lista de modelos de programação.

<details>
<summary><strong>Configuração de áudio no WSLg</strong></summary>

```bash
sudo apt install -y sox pulseaudio libasound2-plugins
export PULSE_SERVER=unix:/mnt/wslg/PulseServer
```

</details>

<details>
<summary><strong>Áudio remoto por SSH (Mac → anfitrião remoto)</strong></summary>

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

### Dream e Distill

- **`/dream`** — analisa os rastos de sessões recentes, extrai conhecimento persistente para a memória do projeto e remove entradas desatualizadas
- **`/distill`** — descobre fluxos de trabalho manuais repetidos no trabalho recente e empacota os candidatos de alta confiança em competências, subagentes ou comandos reutilizáveis

---

## Configuração

O QitQode é configurado através de `.qitqode/qitqode.json` na pasta do projeto (ou `~/.config/qitqode/qitqode.json` a nível global). As principais opções incluem:

- Seleção da capacidade do modelo (Free (no cost), Adaptive, Fast, Economy, Planner, Repair, Max intelligence, e o pipeline Orchestrated)
- Permissões de agentes e agentes personalizados
- Comportamento dos pontos de verificação e da memória
- Ligações a servidores MCP
- Atalhos de teclado e tema

O Max Mode (raciocínio paralelo best-of-N com seleção por juiz) pode ser ativado através de `experimental.maxMode` na configuração.

---

## Acessibilidade

A TUI do QitQode inclui suporte de acessibilidade de raiz:

- **Modo acessível** — define `QITQODE_TUI_ACCESSIBLE=1` (ou `"tui": { "accessible": true }` na configuração) para uma experiência amigável para leitores de ecrã: renderização linear do ecrã principal (sem ecrã alternativo), sem captura de rato, taxa de fotogramas baixa, movimento reduzido e sem indicações sonoras.
- **NO_COLOR** — qualquer valor não vazio de [`NO_COLOR`](https://no-color.org) muda para renderização monocromática com fundos transparentes. A gravidade nunca é transmitida apenas por cor (os avisos incluem os símbolos `ℹ ✓ ▲ ✗`, as diferenças mantêm os marcadores `+`/`-`).
- **Reduzir movimento** — define `QITQODE_REDUCE_MOTION=1` (ou `"tui": { "reduce_motion": true }`) para substituir animações e indicadores rotativos por texto estático. Também alternável em tempo de execução a partir da lista de comandos.
- **Som** — desativa as indicações sonoras com `QITQODE_TUI_SOUND=0`, `"tui": { "sound": false }` ou a alternância em tempo de execução na lista de comandos. O modo acessível desativa sempre o som.
- **Detalhe dos anúncios** — no modo acessível, controla quão detalhados são os anúncios para leitores de ecrã com `QITQODE_TUI_ANNOUNCEMENTS=quiet|normal|verbose`, `"tui": { "announcements": "quiet" }` ou o seletor em tempo de execução na lista de comandos. `quiet` anuncia apenas as fronteiras de turno; `normal` (predefinição) acrescenta linhas de início de ferramenta; `verbose` acrescenta linhas de conclusão de ferramenta. Os erros e as interrupções são sempre anunciados em todos os níveis.
- **Tema de alto contraste** — seleciona o tema integrado `high-contrast` para superfícies a preto/branco puro com cores em conformidade com WCAG AA.
- **Terminais pequenos** — a TUI degrada-se de forma controlada em terminais estreitos e mostra uma mensagem clara quando a janela fica abaixo do mínimo de 40x8.
- **Diálogos apenas com teclado** — todos os diálogos são totalmente operáveis sem rato: `Esc` fecha sempre, `Tab` (e as teclas de seta, onde um diálogo tem uma fila de botões ou uma lista) move o foco, e `Enter` ou `Space` ativa o controlo em foco. Em terminais simples/NO_COLOR, a linha de lista destacada inclui também um marcador `›` para que a seleção seja percetível sem cor.

As variáveis de ambiente de acessibilidade são interruptores de sentido único: `QITQODE_TUI_ACCESSIBLE=1`, `QITQODE_REDUCE_MOTION=1`, `NO_COLOR` e `QITQODE_TUI_SOUND=0` prevalecem sempre sobre os valores de configuração e as alternâncias em tempo de execução — as garantias de acessibilidade não podem ser anuladas. `QITQODE_TUI_ANNOUNCEMENTS` prevalece de igual modo sobre o valor de configuração e o seletor em tempo de execução, quando definido.

---

## Desenvolvimento

```bash
bun install              # Install dependencies
bun run dev              # Run in development mode
bun turbo typecheck      # Type check
```

---

## Licença

O código-fonte é licenciado sob a [Licença MIT](./LICENSE).
