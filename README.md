# appFilmesWEB

Versão para o computador do **Filmes Full**. Irmão do
**[appFilmesAPK](https://github.com/Lscigliano/appFilmesAPK)** (Android): mesmo
servidor, mesma busca global, mesma metodologia.

## Como usar

1. Baixe o `index.html` da página de
   [Releases](https://github.com/Lscigliano/appFilmesWEB/releases/latest).
2. Abra o arquivo com **dois cliques** (Chrome, Edge ou Firefox).
3. Entre com usuário e senha da assinatura.

Não precisa instalar nada e não existe servidor nosso. Usuário e senha ficam só
no navegador do computador.

**Atalhos:** `/` vai para a busca · no player, `espaço` pausa, `→` avança 30 s,
`←` volta 10 s, `F` tela cheia, `Esc` volta.

## Por que um arquivo, e não um site publicado

O servidor de streaming **só fala HTTP**. Páginas publicadas (GitHub Pages,
Netlify, Vercel) são HTTPS, e o navegador bloqueia uma página HTTPS que chama
HTTP. Aberto direto do disco, o arquivo funciona, porque a API libera CORS
para a origem dos arquivos locais. As duas coisas foram medidas: ver
[`docs/DIARIO.md`](docs/DIARIO.md).

## O que tem

- **Busca global**: procura pelo nome em todos os 43 mil filmes, 9,7 mil séries e
  3,3 mil canais de uma vez. Não depende de categoria, não precisa de acento e
  aceita as palavras em qualquer ordem.
- TV ao vivo, Filmes e Séries com as categorias do servidor.
- Séries com temporadas e episódios.
- Player do navegador com avanço livre. TV ao vivo usa hls.js.
- **Continua de onde parou** em filmes e episódios.

## Metodologia

1. [`docs/PROTOCOLO.md`](docs/PROTOCOLO.md): as regras
2. [`docs/DIARIO.md`](docs/DIARIO.md): o que foi descoberto, inclusive os erros
3. [`tasks/TASK.md`](tasks/TASK.md): o estado e o próximo item

## Aviso

Cliente alternativo para uma assinatura legítima do próprio autor. Não contorna
proteção de conteúdo, não gera acesso e não redistribui nada. **Credenciais
nunca entram neste repositório.**
