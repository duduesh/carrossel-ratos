# Regras de Design — Carrossel

> Este arquivo controla como os slides do carrossel são criados visualmente.
> Tu pode editar qualquer regra aqui e o Claude vai seguir na próxima vez que criar um carrossel.
> Se algo não tá ficando do teu jeito, muda aqui ou pede pro Claude mudar.

---

## Dimensões

- **Instagram:** 1080x1350px (proporção 4:5)
- **TikTok:** 1080x1920px (proporção 9:16)
- **Safe area:** 56px nas laterais, 80px embaixo

---

## Ritmo visual

Variar os layouts entre slides pra manter interesse visual. Não fazer todos os slides com o mesmo layout. Usar os diferentes layouts da seção abaixo pra criar ritmo.

O fundo pode ser consistente (todos claros ou todos escuros, conforme o design guide). O ritmo vem da variação de layout, não de trocar cor de fundo a cada slide.

---

## Hierarquia de leitura

Cada slide deve ter uma ordem clara do que o olho vê primeiro:

1. **Elemento principal:** headline grande, número em destaque, ou imagem
2. **Texto de corpo:** o conteúdo do slide (tamanho sugerido: 36-40px)
3. **Elementos menores:** tag do slide, barra de progresso, branding

---

## Layouts disponíveis

Usar pelo menos 3 layouts diferentes entre os slides pra manter o interesse visual:

- **Texto puro:** headline + body, alinhado à esquerda. O mais simples
- **Número em destaque:** um stat grande (72-108px) com contexto menor embaixo. Bom pra dados
- **Card com borda:** texto dentro de card com borda na cor de destaque. Bom pra citações ou destaques
- **Citação:** texto em itálico com aspas, fundo levemente diferente do padrão do slide
- **Lista visual:** 2-3 pontos com ícone ou bullet estilizado. Máximo 3 itens, senão vira lista genérica

---

## Tratamento de imagens

As imagens ficam na pasta `conteudo/carrosseis/[tema]/imagens/`.
Referenciar no HTML com caminho relativo: `<img src="imagens/foto-capa.jpg">`

### Capa — duas opções conforme o tipo de imagem

**REGRA GERAL: nunca cortar ou redimensionar uma imagem de forma que perca informação. Se a imagem não cabe no layout escolhido, ajustar o layout, não a imagem.**

**Opção 1: Imagem de fundo (fotos grandes, paisagens, ambientes)**

Usar quando a imagem é uma foto bonita que funciona como fundo. CSS exato:

```css
.capa-bg {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: left center; /* alinha à esquerda pra mostrar o conteúdo principal */
}
.capa-overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(180deg,
    rgba(0,0,0,0.2) 0%,
    rgba(0,0,0,0.5) 50%,
    rgba(0,0,0,0.88) 100%
  );
}
.capa-content {
  position: relative;
  z-index: 5;
  /* texto no terço inferior, alinhado à esquerda */
}
```

- Texto sempre branco, posicionado no terço inferior
- `object-position: left center` por padrão (não `center center`, que corta os lados)
- Se o conteúdo principal da foto tá no centro ou à direita, ajustar o object-position

**Opção 2: Print/screenshot em box (prints de tela, interfaces, tweets, imagens focadas)**

Usar quando a imagem precisa ser vista inteira, sem cortar. CSS exato:

```css
body {
  display: flex;
  flex-direction: column;
  justify-content: flex-end; /* conteúdo alinhado embaixo, respiro em cima */
}
.print-box {
  width: 100%;           /* largura total (dentro do padding do slide) */
  max-height: 500px;     /* limite de altura pra sobrar espaço pro texto */
  object-fit: contain;   /* NUNCA cover — contain preserva o print inteiro */
  border-radius: 16px;
  background: rgba(255,255,255,0.05); /* moldura sutil atrás */
  margin-bottom: 48px;   /* espaço entre imagem e título */
}
```

- Imagem e texto alinhados embaixo do slide, respiro visual em cima
- `object-fit: contain` obrigatório (nunca cover, pra não cortar o print)
- Se a imagem é pequena, não esticar. Deixar no tamanho natural com a moldura de fundo
- Se a imagem é muito larga e pouco alta (ex: print widescreen), usar `max-height: 400px` e deixar o contain ajustar
- Se a imagem é muito alta e estreita, reduzir `max-height` e centralizar horizontalmente

**Prioridade de escolha:**
1. **Se o usuário especificou explicitamente** qual opção quer ("quero como fundo", "coloca em box", "imagem de fundo"), usar essa opção. Ponto final — não reclassificar.
2. **Se o usuário não especificou**, escolher automaticamente: foto/paisagem/pessoa = opção 1. Print/screenshot/interface/tweet = opção 2.
3. **Na dúvida**, perguntar ao usuário antes de gerar.

### Slide escuro com foto
Imagem de fundo com overlay escuro (80-90% opacity).
Legibilidade sempre vem primeiro. Se a foto atrapalha a leitura, aumentar o overlay.

### Slide claro com foto
Imagem em box retangular no topo do slide:
- width: 100% (respeitando padding lateral)
- height: ~360px
- border-radius: 16-20px
- object-fit: cover

### Regra geral
TODAS as imagens fornecidas devem ser usadas. Nunca ignorar uma imagem que o usuário mandou.

---

## Design sem foto

Quando o usuário não tem imagem, o design precisa funcionar bem sozinho. Não pode parecer que "faltou a foto".

Recursos visuais pra compensar:
- Números grandes decorativos (um dado importante do slide em tamanho grande)
- Cards com borda colorida (cor de destaque)
- Gradientes sutis entre duas cores da paleta
- Blocos de destaque com cor de fundo diferente do slide
- Emoji como ícone pequeno inline (nunca como decoração grande)

---

## Elementos fixos

### Barra de progresso (rodapé)
- Altura: 3px
- Preenchimento proporcional: (slide_atual / total_slides) * 100%
- Cor em slides claros: cor de destaque do design guide
- Cor em slides escuros: branco

### Logo no slide final
Se o design guide (`marca/design-guide.md`) tiver logo definido na seção **Logo**:
- Incluir no slide final (CTA)
- Largura: 120-200px
- Usar versão correta pra fundo claro ou escuro

---

## HTML técnico

- Inline CSS only (nada externo, exceto Google Fonts)
- Google Fonts via `<link>` no `<head>` (Playwright carrega normalmente)
- Cor de destaque usada com moderação: headlines de capa, palavras-chave (via `<em>`), bordas, barra de progresso. Nunca como fundo de texto corrido

---

## O que ajustar aqui

Se tu não tá gostando do resultado visual, as coisas mais comuns de mudar são:

- **Slides muito escuros/claros:** muda a seção "Ritmo visual"
- **Layout repetitivo:** adiciona ou remove layouts na seção "Layouts disponíveis"
- **Barra de progresso incomoda:** remove da seção "Elementos fixos"
- **Quer mais/menos espaço:** ajusta a safe area na seção "Dimensões"
- **Imagens não ficam boas:** ajusta os overlays na seção "Tratamento de imagens"
- **Quer um elemento novo** (ex: ícones, separadores, bordas arredondadas): adiciona como novo item em "Layouts disponíveis"

Qualquer mudança aqui vale pro próximo carrossel. Pede pro Claude: "muda a regra X no design do carrossel" e ele edita este arquivo.
