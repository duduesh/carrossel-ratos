# Carrossel Ratos

Skill de Claude Code pra criar carrosséis completos pro Instagram e TikTok. Gera texto, cria os slides em HTML, renderiza em PNG via Playwright. Setup guiado na primeira vez.

## O que faz

- Setup conversacional: pergunta sobre marca, tom de voz e estilo de design
- 3 estilos prontos: **minimalista**, **elaborado** (texturas, composições ousadas) e **tweet** (simula post do Twitter)
- Texto editorial com regras anti-AI slop
- Renderiza em PNG (1080x1350 Instagram, 1080x1920 TikTok)
- Suporta imagens do usuário ou design visual sem foto

## Instalação

```bash
git clone https://github.com/duduesh/carrossel-ratos ~/.claude/skills/carrossel
```

## Como usar

Depois de instalar, abre teu projeto no Claude Code e pede:

```
faz um carrossel sobre [tema]
```

Na primeira vez, a skill vai fazer algumas perguntas pra configurar (cores, tom de voz, estilo). Depois disso, vai direto pro carrossel.

## Pré-requisitos

- **Playwright** pra renderizar os HTMLs em PNG. Se não tiver, a skill instala automaticamente:
  ```bash
  npx playwright install chromium
  ```

## Estilos disponíveis

| Estilo | Visual | Quando usar |
|--------|--------|-------------|
| **Minimalista** | Clean, espaço em branco, layouts simples | Marcas elegantes, conteúdo educativo |
| **Elaborado** | Texturas, noise, split layouts, composições ousadas | Marcas bold, conteúdo de impacto |
| **Tweet** | Simula tweet do Twitter, fundo branco, avatar + @handle | Conteúdo opinativo, threads visuais |

Tu pode trocar o estilo a qualquer momento:
```
muda o estilo do carrossel pra tweet
```

## Estrutura

```
carrossel/
├── SKILL.md                          <- fluxo de criação
└── references/
    ├── design-carrossel.md           <- estilo ativo (substituído no setup)
    ├── design-minimalista.md
    ├── design-elaborado.md
    ├── design-tweet.md
    └── badge-verificado.svg          <- badge azul pro estilo tweet
```

## Customização

As regras de design ficam em `references/design-carrossel.md`. Tu pode editar direto ou pedir pro Claude:

```
muda a regra de barra de progresso no design do carrossel
tira o noise dos slides
aumenta o tamanho da fonte do corpo
```

## Licença

CC BY 4.0
