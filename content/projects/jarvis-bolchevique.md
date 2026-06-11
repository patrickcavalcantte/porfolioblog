---
title: "★ JARVIS BOLCHEVIQUE"
date: 2026-06-11
description: "Assistente pessoal local para Windows 11. Sem Docker · Free tier only · 100% local."
tags: ["Next.js", "TypeScript", "Node.js", "IA", "Gemini", "Voz", "Automação"]
cover: ""
weight: 1
---

> Assistente pessoal local para Windows 11
> Sem Docker · Free tier only · 100% local

**Repositório:** [github.com/patrickcavalcantte/projetojarvis](https://github.com/patrickcavalcantte/projetojarvis)

---

## Status

**v0.5 — FUNCIONAL**

- Controle de voz PT-BR com wake word passivo ("Jarvis")
- IA conversacional via Gemini API com SSE streaming
- Automação residencial Tuya (multi-gang)
- Resumo de clipboard com salvamento no Notion
- Atalhos globais de teclado via AutoHotkey
- Biblioteca de livros com IndexedDB e scanner ISBN
- Modo Crise para pessoas neurodivergentes integrado em `/crise`
- Interface holográfica retrofuturista

---

## Stack

| Camada | Tecnologia |
|--------|-----------|
| Frontend | Next.js 14 · TypeScript · TailwindCSS · Framer Motion |
| Backend | Node.js · Express · TypeScript · dotenv |
| Speech-to-Text | Web Speech API (`webkitSpeechRecognition`) — Chrome/Edge |
| Text-to-Speech | SpeechSynthesis API — nativa, sem internet |
| IA | Gemini API — `gemini-2.5-flash-lite`, SSE streaming, free tier |
| Home Automation | Tuya Cloud API + Mock provider |
| WebSocket | pacote `ws` — estado em tempo real frontend↔backend |
| Execução de sistema | PowerShell via `child_process.exec` |
| Atalhos globais | AutoHotkey v1 |
| Notas | Sistema de arquivos — `Documents/Minhas Anotações/` |
| Biblioteca | IndexedDB (`BibliotecaVermelha`) via idb |

---

## Pré-requisitos

- **Node.js** 18+ — [nodejs.org](https://nodejs.org)
- **Google Chrome** ou **Microsoft Edge** — obrigatório para Web Speech API
- **Windows 11**
- **Conexão com internet** — Web Speech API usa servidores Google; Gemini API
- **AutoHotkey v1** (opcional) — para atalhos globais: [autohotkey.com](https://autohotkey.com)

---

## Instalação

```powershell
# Instalar dependências de backend e frontend de uma vez
npm run install:all
```

Ou separadamente:

```powershell
cd backend && npm install
cd ..\frontend && npm install
```

### Configurar variáveis de ambiente

```powershell
# Backend
copy backend\.env.example backend\.env
# Edite backend\.env e adicione sua GEMINI_API_KEY

# Frontend (opcional — padrão já funciona)
copy frontend\.env.local.example frontend\.env.local
```

**Gemini API Key** — gratuita: [aistudio.google.com/app/apikey](https://aistudio.google.com/app/apikey)

---

## Como rodar

Abra **dois terminais PowerShell** e deixe ambos abertos:

**Terminal 1 — Backend:**

```powershell
cd jarvirbolchevique\backend
npm run dev
```

Aguarde: `[SUCCESS] JARVIS BOLCHEVIQUE — Backend online { port: 3001 }`

**Terminal 2 — Frontend:**

```powershell
cd jarvirbolchevique\frontend
npm run dev
```

Aguarde: `✓ Ready — http://localhost:3000`

Acesse **http://localhost:3000** no Chrome ou Edge.

> Use `http://` — não `https://`. Chrome HSTS pode redirecionar; limpe via `chrome://net-internals/#hsts` se necessário.

---

## Como usar

1. Abra `http://localhost:3000` no Chrome ou Edge
2. Permita o microfone quando solicitado
3. Diga **"Jarvis"** — o wake word ativa o microfone automaticamente
4. Ou clique no holograma central / pressione `Espaço`
5. Ou use `Ctrl+Alt+J` em qualquer janela (requer AutoHotkey)

---

## Comandos de voz

### Aplicativos

```
"Jarvis abre o Chrome / Edge / Figma / Teams / VSCode"
"Jarvis abre o Notion / Terminal / Explorer / Notepad"
"Jarvis fecha o Chrome"
```

### IA livre

```
Qualquer pergunta não reconhecida → Gemini responde via streaming
"Me explica o que é...?"  "Como faço para...?"
```

### Casa inteligente

```
"Liga a luz da sala"
"Desliga o ventilador"
"Apaga tudo"
"Jarvis cinema"        → apaga todos os dispositivos configurados
```

### Produtividade

```
"Bom dia Jarvis"       → fala boas-vindas + toca música
"Para a música"        → para o áudio
"Jarvis anota..."      → salva nota em Documents/Minhas Anotações/
"Resumir clipboard"    → resume o que foi copiado, pergunta onde salvar
```

### Biblioteca

```
"Abra minha biblioteca"
"Adiciona o livro [título]"
"Cataloga o livro [título]"
```

### Atalhos globais (AutoHotkey)
| Atalho | Ação |
|--------|------|
| `Ctrl+Alt+J` | Ativa o microfone do Jarvis em qualquer janela |
| `Ctrl+Shift+S` | Resume o texto selecionado em qualquer app |
| `Ctrl+Shift+E` | Explica o texto selecionado |
| `Ctrl+Shift+T` | Transforma o texto selecionado em tópicos |

### Utilitários

```
"Você está aí?"        → confirma presença
"Me lembra de beber água"
"Conta uma piada"
"Como você está?"
"Quais apps você abre?"
```

---

## Arquitetura

```
jarvirbolchevique/
│
├── backend/
│   └── src/
│       ├── index.ts                        # Entry point Express (porta 3001)
│       ├── ai/
│       │   ├── gemini.client.ts            # geminiChat() + streaming, retry 429
│       │   ├── gemini.guard.ts             # Cache TTL 10min + cooldown 3s
│       │   ├── model.router.ts             # JARVIS_PERSONA + classifyRequest()
│       │   ├── orchestrator.ts             # Path 1: home auto / Path 2: Gemini
│       │   ├── intent.extractor.ts         # Fast-path regex p/ comandos de casa
│       │   └── conversation.context.ts     # Histórico por sessão, MAX_TURNS=10
│       ├── home-automation/
│       │   ├── device.map.ts               # dpCodes[] por dispositivo
│       │   ├── devices/                    # Types, registry, 10 mock devices
│       │   ├── providers/                  # Mock + Tuya + HomeAssistant
│       │   ├── intents/                    # Parser pt-BR: ação + cômodo
│       │   └── services/                   # DeviceService, HomeAutomationService
│       ├── routes/
│       │   ├── ai.ts                       # POST /chat, /chat/stream (SSE)
│       │   ├── commands.ts                 # POST /api/commands/execute
│       │   ├── home.ts                     # POST /api/home/control
│       │   ├── clipboard.ts                # /summarize, /process, /save
│       │   ├── hotkeys.ts                  # /trigger (Ctrl+Alt+J), /process
│       │   ├── notes.ts                    # POST /api/notes/save
│       │   └── books.ts                    # GET /api/books/search (proxy Google)
│       ├── services/
│       │   ├── appLauncher.ts / appCloser.ts
│       │   ├── appRegistry.ts              # Catálogo + aliases PT-BR
│       │   ├── noteWriter.ts               # Salva .md em Documents/
│       │   └── notion.service.ts           # Cria página via @notionhq/client
│       └── ws/wsServer.ts                  # WebSocket broadcast (EventBus)
│
├── frontend/
│   └── src/
│       ├── app/
│       │   ├── page.tsx                    # Interface principal JARVIS
│       │   ├── biblioteca/                 # Rota /biblioteca
│       │   └── crise/                      # Rota /crise (Modo Crise)
│       ├── components/
│       │   ├── intro/                      # HologramAvatar, HudOverlay, Particles
│       │   ├── ui/                         # MicButton, Waveform, CommandHistory...
│       │   └── biblioteca/                 # Desktop, Library, BookCard, Scanner...
│       ├── intents/                        # Sistema modular de intents (14 ativos)
│       ├── hooks/
│       │   ├── useJarvisState.ts           # Máquina de estados global
│       │   ├── useSpeechRecognition.ts     # Web Speech API wrapper
│       │   └── useWakeWord.ts              # Wake word "Jarvis" contínuo
│       ├── services/
│       │   ├── tts.service.ts              # SpeechSynthesis pt-BR
│       │   ├── wakeWordEngine.ts           # Engine de wake word (pause/resume)
│       │   ├── audioPlayer.ts              # Singleton HTML Audio
│       │   ├── pendingAction.ts            # Multi-turn: intercepta próximo transcript
│       │   ├── wsClient.ts                 # WebSocket client
│       │   └── keyboardShortcuts.ts        # Escuta hotkey:activate via WS
│       ├── db/                             # IndexedDB via idb (BibliotecaVermelha)
│       └── stores/                         # Zustand stores
│
├── jarvis-hotkey.ahk                       # Atalhos globais AutoHotkey
└── package.json                            # Scripts raiz: install:all, dev:*
```

---

## Fluxo de processamento

```
Fala detectada (STT)
  │
  ├─ matchIntent() — local, ~1ms
  │     └─ match → speak() + asyncAction opcional
  │
  ├─ APP_COMMAND_PATTERN? (abre/fecha)
  │     └─ SIM → POST /api/commands/execute
  │                 ├─ sucesso → personality.onAppLaunch/Close()
  │                 └─ UNKNOWN → cai para streamAI
  │
  └─ NÃO → POST /api/ai/chat/stream (SSE)
              └─ Gemini gemini-2.5-flash-lite
                    └─ sentences → speak() em streaming (~300ms TTFV)
```

---

## Máquina de estados

```
idle → wakeListening → activating → listening → processing → speaking → success/error
```

`BLOCKING_STATES` (wake word pausado): `activating · listening · processing · speaking · success · error`

---

## Intents registradas

| Intent | Prioridade | Trigger exemplo |
|--------|-----------|-----------------|
| good_morning | 20 | "bom dia jarvis" |
| stop_music | 20 | "para a música", "silêncio" |
| add_book | 19 | "adiciona o livro X" |
| biblioteca | 18 | "abra minha biblioteca" |
| clipboard_summary | 18 | "resumir clipboard" |
| note | 15 | "Jarvis anota..." |
| home | 14 | "liga a luz", "cinema" |
| presence | 12 | "você está aí?" |
| intro | 11 | "Jarvis, quem é você?" |
| joke | 8 | "conta uma piada" |
| apps | 7 | "quais apps você abre?" |
| status | 6 | "como você está?" |
| water | 5 | "me lembra de beber água" |
| crise | 4 | "modo crise", "preciso de ajuda" |

---

## Variáveis de ambiente

Copie `backend/.env.example` → `backend/.env` e preencha:

```env
PORT=3001
FRONTEND_ORIGIN=http://localhost:3000

# Obrigatório — aistudio.google.com/app/apikey
GEMINI_API_KEY=your_key_here
GEMINI_MODEL=gemini-2.5-flash-lite

# Opcional — automação residencial Tuya
TUYA_ACCESS_ID=
TUYA_ACCESS_SECRET=
TUYA_PROJECT_CODE=
TUYA_REGION=us
TUYA_DEVICE_SALA=
TUYA_DEVICE_SALA_DP=switch_1,switch_2,switch_3

# Opcional — salvar resumos no Notion
NOTION_API_KEY=
NOTION_PAGE_ID=
```

---

## Personalidade JARVIS

| Parâmetro | Valor |
|-----------|-------|
| `rate` | 1.08 — ritmo levemente acelerado |
| `pitch` | 0.62 — grave sem ser caricato |
| `lang` | pt-BR |
| Voz preferida | Microsoft Antônio → Daniel → qualquer masculina |

System prompt Gemini: direto, elegante, levemente sarcástico, máx 2 frases.

---

## Problemas conhecidos

| Problema | Causa | Solução |
|----------|-------|---------|
| Erro SSL no Chrome | HSTS forçando https | Usar `http://` · `chrome://net-internals/#hsts` |
| Todos os comandos `[FAIL]` | Backend offline | Iniciar `npm run dev` no backend |
| Microfone não ativa após Ctrl+Alt+J | STT conflito Chrome | AHK chama `wakeWordEngine.pause()` antes — verificar se AHK está rodando |
| Gemini 429 | Free tier esgotado (~250 req/dia) | Aguardar reset diário ou gerar nova chave no AI Studio |
| MP3 não toca | Cache do browser com JS antigo | Deletar `.next/` + `Ctrl+Shift+R` |
| Voz não disponível | SpeechSynthesis sem voz pt-BR carregada | Aguardar 1-2s após abrir a página |
| Web Speech API indisponível | Firefox não suporta | Usar Chrome ou Edge |

---

## Build para produção

```powershell
npm run build          # builda backend + frontend

# Rodar
cd backend && node dist/index.js
cd ..\frontend && npm run start
```

---

## Roadmap

### v0.6 — TTS Neural
- Piper TTS — voz neural local, sem internet
- Voz masculina personalizada em pt-BR

### v0.7 — Memória persistente
- SQLite local para histórico de conversas
- RAG sobre arquivos e notas pessoais

### v0.8 — Sistema de Plugins
- Arquitetura de agentes especializados
- Skills carregáveis dinamicamente

### v1.0 — Desktop App
- Ícone na bandeja do sistema via AutoHotkey
- Inicialização automática com o Windows

---

*JARVIS BOLCHEVIQUE — Sistema clandestino. Sem cloud. Sem rastreamento. Sem Docker.*
