# TASK.md — plano ativo

Atualizado em 2026-10-29. Itens **verificáveis**: cada um diz como provar que ficou
pronto. Níveis N1..N4 definidos em `docs/PROTOCOLO.md`, §5.

---

## ✅ Fase 0 e 1 — Descoberta e decisão (2026-10-29)

- [x] O servidor de vídeo tem **API Xtream** (`player_api.php`, `auth: 1`)
- [x] A API libera CORS para `file://` (`null`) e `localhost`
- [x] O servidor **não tem HTTPS**, então site publicado está fora
- [x] Decisão: **um `index.html` aberto do disco**, sem servidor

## ✅ Fase 2 — Construída e provada no Chrome (teste automatizado, `file://`)

- [x] **N1** — carrega sem erro no console
- [x] **N2** — filme toca (1920×801, H.264)
- [x] **N3** — catálogo com 43.024 filmes e 40 categorias em ~4 s; busca
      "hobbit" acha a trilogia nas duas versões; série (Bones: 11 temporadas,
      22 episódios na 1ª) e canal ao vivo (HLS, 1080p) tocam
- [x] **N4** — **avançar para 60% do filme** (≈1h49 de 3h02) e seguir tocando
- [x] Recarregar a página entra sozinho com os dados lembrados

## 🔜 Próximos

- [ ] Confirmação do dono no computador dele (Chrome/Edge, dois cliques)
- [ ] Favoritos guardados no navegador
- [ ] Faixa "Continuar assistindo" no topo, a partir das posições salvas
- [ ] Títulos MKV/AVI (7 de 55 mil): o navegador não toca; hoje a página
      avisa e sugere o app

## 📋 Fatos medidos — não re-descubra

| Pergunta | Resposta |
|---|---|
| CORS na API | reflete a `Origin` (inclusive `null`); `OPTIONS` dá 404, então só GET simples, sem cabeçalho extra |
| CORS no vídeo e no HLS | `Access-Control-Allow-Origin: *` no 302 e no destino |
| HTTPS no servidor | **não existe** (TLS falha) |
| Formatos de filme | 55.059 mp4, 4 mkv, 2 avi, 1 MP4 |
| Tamanho das listas | filmes 1,7 MB, séries 2,8 MB, canais 85 KB |

## ⚠️ Armadilhas

1. **Nada de `fetch` com cabeçalho customizado** na API: dispararia um
   *preflight* `OPTIONS`, que o servidor responde com 404.
2. **`<img loading="lazy">` fora da página nunca carrega.** Insira antes.
3. **Não publique por HTTPS** esperando que funcione. Não funciona, e a página
   avisa.
