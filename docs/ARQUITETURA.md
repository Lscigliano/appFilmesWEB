# Arquitetura — e a decisão que precisa ser tomada primeiro

## O problema central: CORS

Um site em `lscigliano.github.io` que tenta `fetch("http://painel/...")` só
consegue **ler** a resposta se o painel devolver o cabeçalho
`Access-Control-Allow-Origin`. O painel serve o próprio site e nunca precisou
liberar isso para terceiros, então o mais provável é que não libere.

**Mas a regra não é a mesma para tudo:**

| Operação | Precisa de CORS? | Consequência |
|---|---|---|
| `<video src="http://painel/video.m3u8">` nativo | **Não** | Tocar o filme deve funcionar |
| `<img src="http://painel/capa.jpg">` | **Não** | Capas devem aparecer |
| `fetch()` / `XHR` de catálogo em JSON ou HTML | **Sim** | Listar filmes provavelmente falha |
| **hls.js** (HLS em navegador que não é Safari) | **Sim** | ⚠️ hls.js busca os segmentos por `fetch` — sem CORS, não toca |

A linha do hls.js é a mais traiçoeira: se o vídeo for **HLS (`.m3u8`)**, o Chrome
não toca nativamente e precisa do hls.js, que **precisa de CORS**. Já o Safari
toca HLS nativo sem CORS. Se o vídeo for **MP4 progressivo**, toca em tudo sem CORS.

> **Por isso a primeira tarefa é descobrir o formato da URL do vídeo.** Ela decide
> se este projeto é simples ou se precisa de servidor.

## As quatro saídas possíveis

Avaliadas honestamente, da mais simples para a mais complexa:

### 1. Site estático puro — *se o vídeo for MP4 e houver CORS no catálogo*
Vite + TypeScript, publicado no GitHub Pages. Custo zero, sem servidor.
**Risco:** depende de duas condições que provavelmente não se confirmam juntas.

### 2. Site estático + catálogo manual — *se o vídeo for MP4 e NÃO houver CORS*
O vídeo toca direto; o catálogo não pode ser buscado pelo navegador. Caminho:
o usuário cola o ID/URL do filme, ou mantém-se uma lista própria.
**Honestamente:** vira um player avulso, não um "Netflix". Pouco valor.

### 3. Proxy próprio — *funciona em qualquer cenário*
Um pequeno servidor (Cloudflare Workers, Deno Deploy, Vercel) repassa as
requisições ao painel e acrescenta os cabeçalhos CORS. Resolve catálogo **e**
HLS.
**Custo:** passa a existir um servidor para manter, e **as credenciais passariam
por ele** — o que contraria a premissa do projeto Android ("seus dados ficam só no
seu aparelho"). Se for por aqui, o proxy precisa ser *stateless* e nunca registrar
nada. **Decisão do dono, não minha.**

### 4. Extensão de navegador — *contorna CORS por desenho*
Extensões podem pedir permissão de host e fazer requisições sem CORS. Mantém tudo
no computador do usuário, sem servidor.
**Custo:** instalar é mais chato que abrir um site, e publicar na loja do Chrome
tem burocracia. Mas é a única saída que **mantém a privacidade do projeto Android**
e resolve o CORS.

## Recomendação (a confirmar com os dados)

Se o vídeo for **HLS**, as opções 1 e 2 morrem e a escolha real é entre
**proxy (3)** e **extensão (4)** — e a pergunta vira: *vale ter um servidor no
meio?*

Se o vídeo for **MP4 progressivo**, a opção 2 fica viável para assistir, e dá para
começar por ela sem servidor nenhum.

**Nada disso se decide sem a URL do vídeo.** É o item 1 do `tasks/TASK.md`.

## O que se reaproveita do projeto Android

O pacote `io.zaffa.filmes.core` foi escrito em Kotlin puro, sem nada de Android,
exatamente para este momento. Mas ele implementa **Xtream Codes**, e agora se sabe
que **este painel não é Xtream**. O que se reaproveita, então, é o *desenho*, não o
código:

- a leitura tolerante de JSON (campo que vem ora número, ora texto);
- a detecção de página de bloqueio de rede antes de culpar o servidor;
- mensagens de erro que dizem a causa real;
- a ideia de motor de vídeo trocável, que no navegador vira
  `<video>` nativo × hls.js × video.js.
