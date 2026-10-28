# PROTOCOLO DE TRABALHO — appFilmesWEB

> Leia este arquivo inteiro antes de escrever qualquer linha de código.
> Ele existe para que qualquer pessoa — ou qualquer IA — retome o projeto sem
> precisar perguntar nada.

Este repositório segue a **mesma metodologia** do irmão Android,
[appFilmesAPK](https://github.com/Lscigliano/appFilmesAPK). O protocolo completo e
as lições acumuladas estão lá; aqui ficam as regras **próprias da versão web**.

---

## 1. A regra que mais importa: não codar antes de medir

Este projeto nasceu com **duas incógnitas que mudam tudo**:

1. Qual o formato e o endereço do vídeo (HLS? MP4? credencial na URL?)
2. O painel libera CORS?

Enquanto as duas não tiverem resposta registrada no diário, **não escreva camada
de catálogo, nem de login, nem framework nenhum.** O risco não é perder tempo — é
escrever uma arquitetura inteira sobre uma suposição errada e só descobrir no fim.
Foi exatamente o que aconteceu no projeto Android: assumiu-se Xtream Codes e o
painel não é Xtream.

## 2. Segredos

O repositório é **PÚBLICO**.

- ❌ Usuário, senha, token ou cookie de sessão — nunca, em arquivo nenhum.
- ❌ Capturas de tela com credencial visível.
- ✅ O *endereço do painel* é permitido (mesma exceção do projeto Android: é
  público, aparece no próprio app oficial).
- Credenciais de desenvolvimento vão em `credenciais.local.json`, que está no
  `.gitignore`.

> Se o dono colar a senha no chat: **não escreva em arquivo nenhum.** Use só em
> memória e registre no diário apenas o *resultado*, nunca o valor.

## 3. Nada de driblar verificação anti-robô

O login do painel tem **reCAPTCHA**. Automatizar esse login é driblar uma proteção
anti-robô, e **não se faz neste projeto** — nem com a conta do próprio dono.

Quando precisar de dado que está atrás do login, o caminho é o dono obter no
**próprio navegador já autenticado** (DevTools → Network) e passar adiante. É mais
rápido, e não depende de contornar nada.

## 4. Diário

Mesmo formato do projeto Android: entrada nova **no topo** de `docs/DIARIO.md`,
*append-only*, com **Situação / Ação / Resultado / Aprendizado**.

Registre o que te **surpreendeu**, não o que você digitou.

## 5. Verificação

- **N1** — a página carrega sem erro no console.
- **N2** — um vídeo conhecido **toca**.
- **N3** — o catálogo lista de verdade, vindo do painel.
- **N4** — **avançar o filme funciona**, que é o motivo de tudo isto existir.

"Abre sem erro" não é "funciona".

## 6. Se você é uma IA retomando este projeto

1. Leia este arquivo e as entradas do topo de `docs/DIARIO.md`.
2. Abra `tasks/TASK.md` e veja se o item 1 (descobrir a URL do vídeo) já foi
   respondido. **Se não foi, é o único item que importa.**
3. Leia `docs/ARQUITETURA.md` antes de escolher stack — a escolha depende do
   resultado do item 1, não de preferência.
4. Peça ao dono apenas o que ele não registrou. Nada mais.
