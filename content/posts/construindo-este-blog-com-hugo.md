---
title: "Construindo o blog do Creeptech com Hugo"
date: 2026-06-11
description: "Como montei um portfólio + blog estático, rápido e com cara de terminal neon usando Hugo."
tags: ["Hugo", "Go", "Cyberpunk", "Web", "Tutorial"]
cover: ""
---

`> boot.log iniciado em 2026-06-11`

Sempre quis um espaço pessoal que parecesse menos um currículo e mais um
**terminal de uma corporação distópica**. Neste primeiro registro, conto
como montei este site — portfólio e blog num só lugar — usando
[Hugo](https://gohugo.io), um gerador de sites estáticos absurdamente
rápido.

<!--more-->

## `> o que é Hugo?`

Hugo é um **gerador de sites estáticos** (SSG) escrito em **Go**. Em vez de
um servidor montar a página a cada visita — como faz um WordPress da vida —
o Hugo pega meus arquivos **Markdown** + alguns **templates** e gera todo o
**HTML pronto** de uma vez só, na hora do build. O site final é só um monte
de arquivos estáticos: nada de banco de dados, nada de back-end pra manter.

Na prática isso significa:

- **Velocidade:** o build do site inteiro leva milissegundos.
- **Zero servidor:** a saída é HTML/CSS estático, hospeda em qualquer lugar.
- **Markdown:** escrevo posts como este em texto puro, versionado no git.

## `> por que Hugo e não MkDocs?`

Antes de decidir, considerei o [MkDocs](https://www.mkdocs.org), que é
excelente — principalmente com o tema *Material*. Mas o MkDocs nasceu com
foco em **documentação**: a estrutura é mais engessada e fugir do layout
padrão dá trabalho. Como eu queria um **portfólio + blog com identidade
visual forte** (essa pegada cyberpunk), dois pontos pesaram a favor do Hugo:

1. **Flexibilidade de layout** — o sistema de templates do Hugo me deixa
   montar qualquer página do zero. A home que lista os últimos posts e
   projetos, por exemplo, é um template meu, não algo que o tema impôs.
2. **Aprender uns centavos de Go** — o Hugo usa a linguagem de templates do
   Go (`html/template`). Mexer nos layouts é uma desculpa boa pra ir ganhando
   intimidade com a sintaxe do **Go**, mesmo que por enquanto seja só "uns
   centavos".

> Pra documentação técnica eu ainda escolheria o MkDocs. Mas pra um site
> pessoal com cara própria, o Hugo ganhou.

## `> a estética neon`

O visual partiu do tema `terminal`, que já entrega a vibe monospace de
linha de comando. Em cima dele apliquei uma camada própria de CSS:

```css
:root {
  --background: #0b0a14; /* quase preto, com tom roxo */
  --foreground: #c8f7ff; /* ciano pálido */
  --accent:     #05d9e8; /* ciano neon */
}

h1, h2, h3 {
  text-shadow: 0 0 8px var(--accent); /* brilho neon */
}
```

Adicionei também **scanlines** sutis sobre a tela e um leve *glitch* nos
títulos no hover — detalhes que vendem a fantasia sem atrapalhar a leitura.

## `> estrutura`

```
content/
├── _index.md          # home
├── about.md           # sobre
├── projects/          # portfólio
│   └── neon-grid...md
└── posts/             # blog
    └── este-post.md
```

## `> próximos passos`

1. Escrever mais registros sobre os projetos
2. Publicar com deploy automático (GitHub Pages / Netlify)
3. Adicionar busca e RSS (este último o Hugo já gera de graça)

> Fim da transmissão. Conexão encerrada. `> logout`
