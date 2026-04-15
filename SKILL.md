---
name: carrossel
description: >
  Cria carrosséis completos para Instagram e TikTok com a identidade visual do usuário.
  Inclui setup guiado na primeira vez (checagem de marca, tom de voz, Playwright).
  Gera texto editorial, HTMLs estilizados, renderiza em PNG via Playwright.
  Suporta imagens do usuário, geração por IA ou design visual sem foto.
  Use quando o usuário mencionar "carrossel", "carousel", "slides instagram",
  "slides tiktok", "faz um carrossel", ou pedir pra transformar um tema em carrossel.
---

# /carrossel — Criação de Carrossel

## Setup (primeira vez)

Antes de criar qualquer carrossel, checar 5 coisas. Se tudo estiver OK, pular direto pro workflow. Se faltar algo, resolver na hora de forma conversacional.

### 1. Design guide

Ler `marca/design-guide.md`. Se os campos estiverem vazios (template padrão):

> "Pra criar o carrossel com a tua cara, preciso de algumas coisas. Me conta:
> 1. Qual a cor principal da tua marca? (hex tipo #FF5C35, ou descreve: "azul escuro", "laranja quente")
> 2. Tem preferência de fonte? (Se não, eu escolho uma boa pro teu estilo)
> 3. Estilo geral: clean/minimalista, bold/impactante, editorial/elegante, ou outro?
> 4. Tem logo? Se sim, joga o arquivo na pasta `marca/` e me diz o nome"

Com as respostas, preencher `marca/design-guide.md`. A partir da cor principal, gerar variações claras e escuras pra usar nos slides (uma versão mais clara pro destaque sutil, uma mais escura pra fundos). Escolher um fundo escuro e um fundo claro que combinem com o tom da cor (cores quentes pedem fundos mais aconchegantes, cores frias pedem fundos mais neutros).

Se o usuário disser "não sei" ou "escolhe pra mim": usar padrão limpo (fundo escuro #0D0D0D, destaque amarelo #FFD600, Bricolage Grotesque + Instrument Serif).

### 2. Estilo de design dos slides

Ler `references/design-carrossel.md`. Se o arquivo contiver a linha `<!-- estilo: pendente -->`, significa que o usuário ainda não escolheu o estilo. Perguntar:

> "Como tu quer o visual dos slides?
> 1. **Minimalista:** limpo, muito espaço em branco, layouts simples. Elegante e clean
> 2. **Elaborado:** texturas, composições ousadas, layouts variados. Impactante e marcante no feed
> 3. **Tweet:** simula um tweet/post do Twitter. Fundo branco, foto de perfil e @handle no topo, texto grande embaixo. Ultra-limpo e familiar"

Conforme a resposta, **substituir o conteúdo inteiro** de `references/design-carrossel.md` pelo conteúdo do arquivo de estilo escolhido:
- Minimalista: copiar conteúdo de `references/design-minimalista.md`
- Elaborado: copiar conteúdo de `references/design-elaborado.md`
- Tweet: copiar conteúdo de `references/design-tweet.md`

Se `design-carrossel.md` NÃO contiver `<!-- estilo: pendente -->`, o estilo já foi escolhido. Não perguntar de novo.

Se escolher **tweet**, perguntar também:
> "Pra o estilo tweet, preciso de algumas coisas:
> 1. Teu nome (como quer que apareça no slide)
> 2. Teu @ do Instagram
> 3. Uma foto de perfil (joga na pasta `marca/foto-perfil.jpg`). Se não tiver agora, uso as iniciais do teu nome
> 4. Quer o badge azul de verificado ao lado do nome? (sim/não)"

Salvar essas informações na seção `## Perfil do autor` em `marca/design-guide.md` pra não perguntar de novo:
```markdown
## Perfil do autor
- **Nome:** [nome]
- **Handle:** @[handle]
- **Foto:** marca/foto-perfil.jpg (ou "iniciais" se não tiver)
- **Badge verificado:** sim/não
```

Se o design guide já tiver a seção "Perfil do autor" preenchida, não perguntar de novo.

Se o usuário não souber ou não tiver preferência, usar **elaborado** como padrão.

O usuário pode trocar depois a qualquer momento: "muda o estilo do carrossel pra tweet" e o Claude copia o outro arquivo.

### 3. Tom de voz

Ler `_contexto/preferencias.md`. Se estiver vazio:

> "Como tu prefere que eu escreva os textos dos slides?
> - Informal e direto (papo de colega)?
> - Profissional mas acessível?
> - Técnico e denso?
> E tem algo que te incomoda em texto de IA? (ex: travessões, emojis, frases curtas demais)"

Salvar em `_contexto/preferencias.md`.

### 4. Contexto do negócio

Ler `_contexto/empresa.md`. Se estiver vazio, avisar:

> "Ajuda se eu souber sobre o teu negócio e público pra escrever melhor. Roda /setup pra configurar, ou me conta rápido: o que tu faz e pra quem."

Se o usuário responder rápido (sem rodar /setup), anotar o essencial em `_contexto/empresa.md`.

### 5. Playwright

Verificar se Playwright tá instalado:

```bash
npx playwright screenshot --help 2>/dev/null && echo "OK" || echo "INSTALAR"
```

Se precisar instalar:

```bash
npx playwright install chromium
```

Avisar o usuário que tá instalando (demora uns 30s na primeira vez).

---

## Dependências

- **Identidade visual:** `marca/design-guide.md` (preenchido no setup ou antes)
- **Regras de design:** `references/design-carrossel.md` (dentro desta skill)
- **Contexto:** `_contexto/empresa.md`
- **Tom de voz:** `_contexto/preferencias.md`
- **Playwright CLI:** `npx playwright screenshot`

## Input

O usuário fornece:
- Tema, ideia, texto livre, link ou arquivo de referência
- Imagens (opcional): se anexar fotos, usar nos slides. Se não, criar design visual sem foto
- Número do episódio ou série (se aplicável)

---

## Workflow em 3 Fases

### Fase 1 — Texto

1. Ler `_contexto/preferencias.md` pra calibrar tom
2. Ler `_contexto/empresa.md` pra entender contexto e público
3. Se o input for um link, buscar o conteúdo com os fallbacks corretos:
   - **Links normais (artigos, blogs, docs):** tentar WebFetch direto. Se falhar ou retornar pouco conteúdo, usar Jina Reader: prefixar a URL com `https://r.jina.ai/` (ex: `https://r.jina.ai/https://exemplo.com/artigo`)
   - **Links do X/Twitter (x.com ou twitter.com):** substituir o domínio por `api.fxtwitter.com` e usar WebFetch (ex: `https://x.com/user/status/123` vira `https://api.fxtwitter.com/user/status/123`). Funciona também pra `x.com/i/status/ID`. Retorna texto, mídia e links do tweet sem precisar de login
   - **Links do Instagram:** WebFetch não funciona. Avisar o usuário pra copiar o texto do post e colar direto
   - **Links de YouTube:** se tiver a skill `/yt-transcript` ou `/transcribe`, usar. Se não, tentar WebFetch na página
4. Definir o ângulo do carrossel: educacional, oportunidade, contrário, provocativo ou inspiracional

5. **Briefing rápido** — perguntar ao usuário (numa mensagem só):
   > "Antes de escrever, me confirma:
   > - Quantos slides? (padrão: 8-10)
   > - Vai querer imagem na capa ou dentro dos slides? Se sim, quantas imagens tu tem?
   >   - Se tiver imagens: joga na pasta `conteudo/carrosseis/[tema]/imagens/` e me diz os nomes
   >   - Se não tiver: faço um design visual que funciona bem sem foto
   >   - Se quiser, posso gerar imagens por IA (ver seção Geração de Imagens abaixo)
   > - CTA do último slide? (ex: 'segue pra mais', 'link na bio', 'comenta X')
   > - Tipo: dica prática, tendência, opinião forte, bastidores, ou outro?"

   Se o usuário responder tudo junto ("faz sobre X com 8 slides"), não ficar perguntando mais. Usar bom senso pros campos que faltaram.

   Se o usuário fornecer imagens, criar a pasta `conteudo/carrosseis/[tema]/imagens/` e confirmar que as imagens estão lá antes de seguir.

6. Escrever os slides seguindo o fluxo:

   **Slide 1 (Capa):** 3 opções de título (max 8 palavras cada) + subtítulo. O usuário escolhe antes de continuar.

   **Slide 2 (Hook/Tensão):** Contextualizar o tema e criar curiosidade. Duas frases que fazem a pessoa querer passar pro próximo.

   **Slides 3-4 (Contexto):** O que é, por que importa, o que mudou. Dados concretos quando possível (número + fonte + ano, não "cada vez mais" ou "nos últimos tempos").

   **Slides 5-7 (Desenvolvimento):** Um insight por slide, opinião clara. Cada slide aprofunda um ponto diferente, não repete o anterior com palavras diferentes.

   **Slide 8-9 (Implicação):** O que isso muda pra quem tá lendo? Conexão prática, não genérica.

   **Slide final (CTA):** Chamada pra ação + menção ao canal/marca. Curto.

**Regras de escrita:**
- Frases longas e naturais (2-4 frases por slide), não bullet points disfarçados
- Cada frase deve funcionar lida em voz alta, como parágrafo de reportagem
- Conectivos naturais: porque, só que, por isso, enquanto, mas, então. Evitar lista solta
- Curiosity gap entre slides (cada slide termina com algo que puxa pro próximo)
- Dentro de cada slide, o texto flui como um bloco coeso
- Sem travessões (—) a menos que o preferencias.md diga o contrário
- Sem estruturas binárias tipo "não é X, é Y" ou "X diminui, Y acelera" (parece IA)
- Sem cacoetes: "e isso muda tudo", "no fim das contas", "a pergunta que fica", "de forma X", "cada vez mais"
- Sem jargão corporativo: "ecossistema", "mindset", "sinergia", "potencializar"
- Se soar como template ou texto gerado, reescrever
- Dados concretos quando possível: "cresceu 34% em 2024 (Statista)" em vez de "cresceu muito nos últimos anos"
- Se não tiver dado verificável, não inventar. Melhor uma opinião honesta do que um número falso

7. Gerar legenda Instagram:
   - Gancho nos primeiros 125 caracteres (é o que aparece antes do "...mais")
   - 2-3 parágrafos curtos de desenvolvimento
   - CTA no final
   - 5-10 hashtags relevantes

8. Salvar tudo em `conteudo/carrosseis/[tema]/carousel-text.md`

**CHECKPOINT:** Mostrar o texto completo + as 3 opções de capa + legenda sugerida. Esperar o usuário escolher a capa e aprovar o texto antes de seguir.

---

### Fase 2 — Visual (HTMLs + PNGs)

1. Ler `marca/design-guide.md` pra identidade visual (cores, fontes, logo)
2. Ler `references/design-carrossel.md` pra regras de design (layouts, ritmo, imagens, elementos fixos)
3. Criar HTMLs seguindo as regras do arquivo de design
4. Salvar HTMLs em `conteudo/carrosseis/[tema]/instagram/`
5. Renderizar via CLI:
   ```bash
   npx playwright screenshot --viewport-size=1080,1350 --full-page "file:///caminho/absoluto/slide-01.html" "slide-01.png"
   ```
   Renderizar **slide 1 primeiro** e mostrar pro usuário.

**CHECKPOINT:** Mostrar slide 1 renderizado. Se aprovado, renderizar os demais. Se pedir ajuste, editar o HTML e re-renderizar só aquele slide.

Salvar PNGs em `conteudo/carrosseis/[tema]/instagram/`.

> **Dica:** se o usuário não gostar do visual, as regras de design ficam em `references/design-carrossel.md`. Pode editar direto ou pedir: "muda a regra X no design do carrossel".

---

### Fase 3 — Versão TikTok (opcional)

Após finalizar o Instagram, perguntar:
> "Quer a versão TikTok também? (1080x1920, formato vertical)"

Se sim:
- Adaptar os HTMLs: height 1920px, aumentar padding, ajustar espaçamento
- **Bottom safe zone:** deixar 230px livres embaixo (UI do TikTok sobrepõe)
- Renderizar via CLI:
  ```bash
  npx playwright screenshot --viewport-size=1080,1920 --full-page "file:///caminho/absoluto/slide-01.html" "slide-01.png"
  ```
- Salvar em `conteudo/carrosseis/[tema]/tiktok/`

---

## Output final

```
conteudo/carrosseis/[tema]/
  carousel-text.md          <- texto aprovado + legenda
  imagens/                  <- fotos do usuário ou geradas (se houver)
  instagram/
    slide-01.html -> slide-01.png
    slide-02.html -> slide-02.png
    ...
  tiktok/ (se solicitado)
    slide-01.html -> slide-01.png
    ...
```

## Geração de imagens (opcional)

Se o usuário quiser imagens mas não tiver nenhuma, oferecer gerar por IA. A opção mais simples:

### Nano Banana 2 (recomendado, gratis)

Se a skill `nanobanana-ratos` estiver instalada em `~/.claude/skills/nanobanana-ratos/`, usar ela pra gerar imagens. Checar se o `.env` existe nessa pasta.

Se estiver instalada e configurada:
1. Carregar a chave: `source ~/.claude/skills/nanobanana-ratos/.env`
2. Gerar a imagem com o mesmo curl da skill nanobanana-ratos
3. Salvar em `conteudo/carrosseis/[tema]/imagens/`
4. Mostrar pro usuario aprovar antes de usar no slide

Se NAO estiver instalada, sugerir:

> "Pra gerar imagens por IA, tu pode instalar a skill nanobanana-ratos. E gratis e leva 2 minutos. Quer que eu te ajude?"

**Dica pro prompt:** ser especifico e adicionar "no text, clean background, professional" pra resultados mais limpos.

### Alternativa sem skill

Se o usuario nao quiser instalar a skill, orientar a gerar a imagem em outra ferramenta (Canva, ChatGPT, Midjourney) e jogar na pasta `imagens/`.

---

## Regras

- Texto aprovado na Fase 1 não muda na Fase 2 (visual fiel ao texto)
- Sempre mostrar slide 1 antes de renderizar os demais
- Se o usuário pedir ajuste no visual, editar o HTML e re-renderizar apenas o slide alterado
- Sem travessões no texto por padrão, a menos que preferencias.md diga o contrário
- Se o setup já foi feito antes, não repetir as perguntas. Ir direto pro workflow
- Se faltar algo no meio do caminho (ex: Playwright não instalado), resolver na hora sem travar
- Regras de design vivem em `references/design-carrossel.md`. Se o usuário quiser mudar algo visual, editar lá
