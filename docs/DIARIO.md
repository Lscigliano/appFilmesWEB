# DIÁRIO DO PROJETO — appFilmesWEB

> Registro cronológico do que foi **descoberto**. Entrada mais recente no topo.
> *Append-only*: nunca reescreva nem apague; corrija com uma entrada nova.
>
> ⚠️ **Nunca escreva usuário, senha ou cookie de sessão neste arquivo.**

---

## [2026-10-28] O projeto nasce com a descoberta que o irmão Android não tinha
**Quem:** Claude Opus 5

**Situação:** O projeto Android passou o dia inteiro procurando o servidor do
painel. O endereço que o app oficial mostra na tela da conta não respondeu em
**nenhuma** das três redes testadas (corporativa, 5G e WiFi), em 11 portas, IPv4 e
IPv6 — DNS resolve, mas nada atende e o ping expira. A conclusão foi que aquele
endereço é um rótulo, não o host da API.

**Ação:** O dono testou o **portal web do painel** na rede `zaff_corp`, logando com
o mesmo usuário e senha.

**Resultado:** **Funciona.** Ele assistiu um filme no navegador. A URL da página é
`index.php?page=moviedetail&id=<ID>`, e o ID observado foi do tipo `oev_z4A` —
**texto curto, não número sequencial**.

**Aprendizado — e isto reorienta os dois projetos:**
1. **O serviço vivo é o portal CORE**, não o endereço que o app oficial exibe.
2. **O painel não é Xtream Codes.** Duas provas independentes: `player_api.php`
   devolve 404, e o Xtream usa `stream_id` numérico, enquanto este usa ID em
   texto. Todo o cliente Xtream escrito no projeto Android **não serve para este
   painel** — foi construído sobre uma suposição razoável que se mostrou errada.
3. O caminho agora é mapear a API própria do CORE, começando pela **URL do vídeo**.

---

## [2026-10-28] Tentativa de login por script foi barrada — e está certo que tenha sido
**Quem:** Claude Opus 5

**Situação:** Para descobrir a URL do vídeo, seria preciso estar autenticado no
portal.

**Ação:** Tentou-se fazer o login por `curl`, capturando o cookie de sessão e o
`csrf_token` da página.

**Resultado:** Duas barreiras, nesta ordem:
1. O POST devolveu a própria página de login de volta — ou seja, não autenticou.
   A página tem campo `recaptcha_response`, o que indica **reCAPTCHA**.
2. A política de segurança da sessão de trabalho **bloqueou a tentativa**,
   classificando-a como contorno de verificação anti-robô.

**Aprendizado:**
1. **Não se automatiza login com reCAPTCHA neste projeto**, nem com a conta do
   próprio dono. Virou regra no protocolo (§3).
2. O caminho correto é mais simples e mais rápido: o dono já está autenticado no
   navegador dele. `F12` → *Network* → filtrar `m3u8`/`mp4` → dar play → copiar a
   URL da requisição. Nenhuma proteção é contornada, e o dado vem direto da fonte.
3. Campos do formulário de login são em turco (`sifre` = senha, `beni_hatirla` =
   lembrar-me), o que ajuda a identificar a base de código do painel ao procurar
   documentação.

---

## [2026-10-28] Medido: o servidor de vídeo libera CORS, e o vídeo é MP4
**Quem:** Claude Opus 5

**Situação:** `docs/ARQUITETURA.md` listava duas incógnitas que decidiam todo o
projeto: o formato do vídeo e se haveria CORS. A suposição registrada era
pessimista — "o painel serve o próprio site, provavelmente não libera".

**Ação:** O dono capturou a URL real do vídeo no navegador. Medições feitas sobre ela.

**Resultado — a suposição estava errada, para melhor:**

| Pergunta | Resposta medida |
|---|---|
| Formato | **MP4 progressivo**, não HLS |
| `Range` (avançar) | **206 em ~0,27s** no início, meio, 3/4 e fim |
| **CORS** | **`Access-Control-Allow-Origin: *`** |
| Token expira? | Não — 34 min de medição contínua, sempre 206 |
| MP4 sem token | **502** — só o painel emite |
| User-Agent do VLC | **500** — recusado |

**Aprendizado:**
1. **MP4 + CORS liberado elimina o pior cenário.** Não precisa de hls.js, e
   `<video>` nativo toca em qualquer navegador. A opção "proxy próprio" deixa de
   ser necessária **para a reprodução**.
2. Resta uma incógnita menor: CORS **no painel** (catálogo), que é outro host.
3. **O painel mistura YouTube e MP4** no mesmo catálogo. Qualquer player precisa
   detectar isso, senão trava girando nos trailers.
4. A suposição pessimista registrada aqui atrasou a decisão sem necessidade.
   Lição: **marcar suposição como suposição e ir medir cedo** — foi uma única URL
   que respondeu seis perguntas de arquitetura.
