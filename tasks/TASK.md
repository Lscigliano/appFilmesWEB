# TASK.md — plano ativo

Atualizado em 2026-10-28. Itens **verificáveis**: cada um diz como provar que ficou
pronto. Níveis N1..N4 definidos em `docs/PROTOCOLO.md`, §5.

---

## Fase 0 — Descoberta ✅ (concluída antes de escrever código, de propósito)

- [x] Confirmar qual painel está vivo — é o portal CORE
- [x] Descobrir a URL do vídeo
- [x] Medir o servidor de vídeo (ver tabela abaixo)
- [x] Confirmar que **não** é Xtream Codes

### O que foi medido (2026-10-28)

| Pergunta | Resposta |
|---|---|
| Formato do vídeo | **MP4 progressivo** (não HLS) |
| Suporta `Range`? | **Sim** — 206 em ~0,27s no início, meio, 3/4 e fim |
| **CORS no servidor de vídeo** | **`Access-Control-Allow-Origin: *`** ✅ |
| Token expira? | Não — válido por 34 min de medição contínua |
| Pedir o MP4 sem token | **502** — só o painel emite token |
| User-Agent do VLC | **500** — o servidor recusa |
| Fonte do vídeo | **Mista**: MP4 próprio em alguns títulos, **YouTube embutido** em outros |

## Fase 1 — A decisão que falta

- [ ] **Verificar se o painel (não o servidor de vídeo) libera CORS.**
      *Como:* no navegador já logado, abrir o Console do DevTools em qualquer
      página e rodar `fetch('/index.php?page=movies').then(r=>r.status)`.
      Se der erro de CORS a partir de **outra origem**, o catálogo não pode ser
      lido por um site hospedado em outro domínio.
      *Isso decide entre as opções abaixo.*

- [ ] Decidir e registrar no diário qual caminho seguir:
      - **Extensão de navegador** — roda junto com o site do painel, aproveita o
        login existente, sem CORS e sem servidor. Troca o player do painel pelo
        nosso. **É a melhor opção se o objetivo for corrigir o travamento no PC.**
      - **Página estática** — só toca um vídeo cuja URL você cola. Viável hoje
        (MP4 + CORS liberado no vídeo), mas sem catálogo.
      - **Proxy próprio** — resolve tudo, mas cria um servidor para manter e as
        credenciais passariam por ele. Contraria a premissa de privacidade.

## Fase 2 — Construir (só depois da Fase 1)

- [ ] **N1** — a página/extensão carrega sem erro no console
- [ ] **N2** — um vídeo conhecido **toca**
- [ ] **N3** — o catálogo aparece (se o caminho escolhido permitir)
- [ ] **N4** — **avançar o filme funciona** ← o objetivo de tudo isto

## O que já se sabe que vai dar trabalho

- **Títulos com trailer do YouTube.** O painel embute YouTube em alguns itens. Um
  player que só espera MP4 fica girando para sempre neles — já observado na
  prática. Precisa detectar e deixar o YouTube tocar no player dele.
- **A URL do vídeo só existe depois de o painel montá-la**, com token. Qualquer
  solução precisa capturar essa URL, não construí-la.

---

## Referência cruzada

O projeto Android ([appFilmesAPK](https://github.com/Lscigliano/appFilmesAPK))
resolveu o mesmo problema com arquitetura **híbrida**: carrega a página do painel
e intercepta a URL do vídeo, desviando para um player próprio com motores
trocáveis. A **extensão de navegador é o equivalente exato disso na web** — leia
o diário de lá antes de decidir aqui.
