# Arquitetura

> Este arquivo foi reescrito em 2026-10-29. A versão anterior avaliava extensão,
> proxy e página estática partindo de "o painel não é Xtream" e "não há CORS".
> As duas premissas caíram com medição (ver `docs/DIARIO.md`); o histórico do git
> guarda o texto antigo.

## A ideia central

```
  index.html (aberto do disco)                SERVIDOR (Xtream / XUI, só HTTP)
  ────────────────────────────                ────────────────────────────────
  login             ── player_api.php ──────▶ auth: 1 / 0
  catálogo + busca  ── get_*_categories ────▶ categorias
                       get_vod_streams ─────▶ 43 mil filmes   ┐ CORS liberado
                       get_series ──────────▶ 9,7 mil séries  │ para a origem
                       get_live_streams ────▶ 3,3 mil canais  ┘ do arquivo
  série             ── get_series_info ─────▶ episódios
  <video>           ── /movie|series/... ───▶ 302 → MP4 com token (Range ok)
  hls.js            ── /live/....m3u8 ──────▶ 302 → HLS (CORS *)
```

Mesmo servidor e mesmas chamadas do app Android (`xtream/ApiXtream.kt` lá).

## Decisões e seus motivos

### Um único `index.html`, aberto do disco
- **HTTPS está fora:** o servidor não tem TLS, e página HTTPS que chama HTTP é
  bloqueada pelo navegador. Isso elimina GitHub Pages, Netlify e similares.
- **Arquivo local funciona:** a API devolve `Access-Control-Allow-Origin: null`
  para a origem de `file://` e também libera `localhost`.
- **Sem build, sem dependência instalada:** HTML, CSS e JS num arquivo. O único
  recurso externo é o hls.js, baixado do jsDelivr **só quando um canal ao vivo é
  aberto** (Chrome e Firefox não tocam HLS nativo).

Se a página for aberta por `https:`, ela avisa na tela em vez de falhar calada.

### Proxy foi descartado
Resolveria o HTTPS, mas usuário, senha e **todo o vídeo** passariam por um
servidor nosso. Isso contraria a premissa de privacidade e custa banda. Não há
necessidade enquanto o arquivo local funcionar.

### Catálogo inteiro na memória
São seis chamadas em paralelo, ~6 MB, cerca de 4 s. A busca roda no navegador
sobre todos os títulos: sem acento, todas as palavras em qualquer ordem, com
quem começa pelo texto digitado primeiro. A API repete o filme uma vez por
categoria; os títulos são juntados por id.

### A grade desenha 120 capas por vez
Desenhar 43 mil cartazes trava a aba. Um `IntersectionObserver` desenha o
próximo lote quando a rolagem chega perto do fim.

⚠️ A `<img loading="lazy">` precisa estar **inserida na página** desde o início.
O Chrome nunca baixa uma imagem lazy que ainda está fora dela, e as capas
sumiam por isso.

### Posição salva por título
`localStorage`, chave `filmes.pos.<filme|ep>-<id>`, gravada a cada 5 s e ao
fechar. Some quando faltam menos de 60 s para o fim.

## Segurança

- A URL do Xtream leva usuário e senha. Nada é escrito no console, e as
  mensagens de erro são textos nossos.
- As credenciais ficam em `localStorage` ("lembrar") ou em `sessionStorage`.
  **Atenção:** no Chrome, todos os arquivos `file://` dividem o mesmo
  `localStorage`. É aceitável num computador pessoal; num computador
  compartilhado, desmarque "lembrar".
