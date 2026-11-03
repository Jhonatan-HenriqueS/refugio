# Refuge — como publicar

Site estático: HTML, CSS e JS puros. **A pasta `site/` já está pronta para publicar, sem etapa de build.**
Nada de `tools/`, `_briefing/` ou `node_modules` vai para a hospedagem.

## Antes de publicar (obrigatório)

1. **Domínio:** troque `{{DOMINIO}}` pelo domínio comprado (ex.: `refugeestetica.com.br`, sem `https://` e sem barra no final).
2. **CRBM:** troque `{{CRBM_ROSE}}` pelo número do registro da Dra. Rose.
3. **Data das páginas legais:** confira "Atualizada em 2 de novembro de 2026" em `politica-de-privacidade.html` e `termos-de-uso.html`. Troque pela data real de publicação, se for diferente.

Onde estão os placeholders:

- **`{{DOMINIO}}`**
  - `index.html`: canonical, Open Graph, Twitter e JSON-LD
  - `politica-de-privacidade.html` e `termos-de-uso.html`: canonical
- **`{{CRBM_ROSE}}`**
  - `index.html`: seção Fundadora e rodapé
  - `politica-de-privacidade.html` e `termos-de-uso.html`: rodapé

### Caminho recomendado: pelo template

Edite os templates em `tools/templates/` e rode o build:

```bash
cd tools && npm install          # só na primeira vez
grep -rl '{{DOMINIO}}'   templates | xargs sed -i 's/{{DOMINIO}}/refugeestetica.com.br/g'
grep -rl '{{CRBM_ROSE}}' templates | xargs sed -i 's/{{CRBM_ROSE}}/00000/g'
node build.mjs                   # regenera site/*.html, minifica CSS/JS e atualiza o ?v=hash
```

### Caminho rápido: direto no site/

Use este caminho quando não houver Node disponível:

```bash
sed -i 's/{{DOMINIO}}/refugeestetica.com.br/g; s/{{CRBM_ROSE}}/00000/g' site/*.html
```

Nesse caso, faça a mesma troca nos templates depois, para não perder a alteração no próximo build.

## Opção A — Cloudflare Pages (recomendada)

1. Acesse Cloudflare → *Workers & Pages* → *Create* → *Pages*.
2. Escolha uma forma de envio:
   - **Direct Upload:** envie o conteúdo da pasta `site/`.
   - **Git:** em *Build command*, deixe **vazio**. Em *Build output directory*, coloque `site`.
3. O arquivo `_headers` é aplicado automaticamente:
   - CSP, HSTS e demais cabeçalhos de segurança;
   - cache imutável em `/assets/*`.
4. A `404.html` é usada automaticamente.
5. Ligue o domínio próprio em *Custom domains*. O HTTPS é automático.

## Opção B — Vercel

1. Importe o projeto.
2. Em *Root Directory*, coloque `site`. Em *Framework Preset*, escolha **Other**.
3. Deixe *Build Command* e *Output Directory* vazios.
4. O `vercel.json` aplica os mesmos cabeçalhos do `_headers`.
5. A `404.html` é usada automaticamente.

## Depois de publicar — conferir em 5 minutos

- Abra o site no celular em 4G e toque em **Agendar minha avaliação**. O WhatsApp deve abrir com "Olá! Vim pelo site da Refuge…".
- Confira os cabeçalhos em https://securityheaders.com.
- Teste o compartilhamento (OG) no WhatsApp ou em https://www.opengraph.xyz.
- No console do navegador (F12), não deve haver erros de CSP.
- Antes de clicar em "Ver mapa aqui", nenhuma requisição deve ir ao Google.

## Atualizar conteúdo

| O quê | Onde |
|---|---|
| Textos e HTML | `tools/templates/*.html` (rodapé e cabeçalho das páginas legais em `tools/templates/_partials/`) |
| Estilos | `tools/src/main.css` |
| Scripts | `tools/src/main.js` |
| Mensagens do WhatsApp | `tools/build.mjs` (objeto `WA`) |
| Fotos | `tools/process-images.mjs` (gera AVIF/WebP e `manifest.json`) |

Depois de qualquer mudança, rode `node tools/build.mjs`.
Os assets em `/assets/*` têm cache de 1 ano. O CSS e o JS são versionados por `?v=hash`. Uma foto trocada precisa de **nome novo**.

## Verificação (opcional, para quem for mexer)

```bash
node tools/serve.mjs 4173        # servidor local com os cabeçalhos do _headers
node tools/qa.mjs                # CSP, terceiros, axe, links wa.me, teclado
node tools/fullpage.mjs          # screenshots + densidade das imagens + contraste
npx --prefix tools html-validate site/*.html
```

<!-- v2: sitemap/robots -->
## Próxima versão (v2)

Ainda não feito, de propósito:

- `sitemap.xml` e `robots.txt`;
- Google Search Console;
- link do domínio no Perfil da Empresa no Google.
