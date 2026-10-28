# appFilmesWEB

Versão para navegador do player próprio. Irmão do repositório
**[appFilmesAPK](https://github.com/Lscigliano/appFilmesAPK)** (Android), com o
mesmo objetivo e a mesma metodologia.

> ⚠️ **Fase de descoberta quase concluída.** Ainda não há código de aplicação, e isso é
> deliberado: falta confirmar como o painel entrega o vídeo e se o navegador vai
> conseguir falar com ele (ver "A decisão que trava tudo", abaixo).
> Não escreva código de catálogo antes de resolver isso.

---

## Por que este projeto existe

O app oficial trava ao avançar o filme. O projeto Android já ataca isso trocando o
motor de vídeo. No navegador o problema é outro, mas a origem é a mesma: controle
nenhum sobre o player.

A versão web dá acesso em qualquer computador sem instalar nada.

## O que já se sabe (confirmado em 2026-10-28)

| Fato | Como foi confirmado |
|---|---|
| O painel que **funciona** é o portal CORE, não o endereço que o app oficial exibe | O dono assistiu um filme no navegador, logado, na rede `zaff_corp` |
| O endereço mostrado na tela da conta do app oficial **está morto** | Testado em 3 redes, 11 portas, IPv4 e IPv6 — DNS resolve, nada responde |
| **Não é Xtream Codes** | `player_api.php` devolve 404; e o ID de filme é texto (`oev_z4A`), não número sequencial |
| O filme abre em `index.php?page=moviedetail&id=<ID>` | URL da barra de endereços do navegador |
| O login tem **reCAPTCHA** | Visto no HTML da página de login |

Detalhes e o raciocínio completo em [`docs/DIARIO.md`](docs/DIARIO.md).

## A decisão que trava tudo

Duas perguntas precisam de resposta **antes** de escrever aplicação:

**1. Qual é a URL do vídeo?** Sem ela não há player. Pega-se no navegador já
logado: `F12` → aba *Network* → filtrar por `m3u8` (depois `mp4`, depois `.ts`) →
dar play → copiar o endereço da requisição.

**2. O navegador vai conseguir falar com o painel (CORS)?**
Um site hospedado em outro domínio não pode ler respostas do painel sem que ele
envie os cabeçalhos `Access-Control-Allow-Origin`. O painel serve o próprio site,
então provavelmente nunca liberou isso.

Importante: **vídeo e metadados têm regras diferentes.**
- `<video src="http://painel/...">` funciona cross-origin **sem CORS**.
- `fetch()` de catálogo/JSON **exige CORS**.

Ou seja, é possível que dê para *tocar* o filme e não dê para *listar* o catálogo.
As saídas estão avaliadas em [`docs/ARQUITETURA.md`](docs/ARQUITETURA.md).

## Metodologia

Igual à do projeto Android, e obrigatória:

1. [`docs/PROTOCOLO.md`](docs/PROTOCOLO.md) — as regras
2. [`docs/DIARIO.md`](docs/DIARIO.md) — o que foi descoberto, inclusive os erros
3. [`tasks/TASK.md`](tasks/TASK.md) — o próximo item

## Aviso

Cliente alternativo para uma assinatura legítima do próprio autor. Não contorna
proteção de conteúdo, não gera acesso e não redistribui nada. **Credenciais nunca
entram neste repositório.**
