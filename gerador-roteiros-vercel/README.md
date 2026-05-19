# gerador de roteiros · must media
## deploy na vercel — passo a passo

---

### estrutura do projeto

```
gerador-roteiros-vercel/
├── api/
│   └── gerar.js        ← função serverless (proxy da API da Anthropic)
├── public/
│   └── index.html      ← frontend da ferramenta
├── vercel.json         ← configuração de rotas
└── README.md
```

---

### passo 1 — criar conta na vercel

1. acesse https://vercel.com
2. clique em "sign up"
3. escolha "continue with github" (recomendado) ou email

---

### passo 2 — instalar o vercel cli (opcional, mas mais rápido)

se tiver node.js instalado:
```bash
npm install -g vercel
```

---

### passo 3 — pegar sua api key da anthropic

1. acesse https://console.anthropic.com
2. vá em "api keys" no menu lateral
3. clique em "create key"
4. copie a chave (começa com `sk-ant-...`)
5. **guarde bem — ela aparece só uma vez**

---

### opção A — deploy pelo github (mais fácil, sem instalar nada)

1. crie um repositório no github com os 3 arquivos:
   - `api/gerar.js`
   - `public/index.html`
   - `vercel.json`

2. acesse https://vercel.com/new

3. clique em "import git repository" e selecione seu repo

4. na tela de configuração, **não mude nada** — clique em "deploy"

5. depois do deploy, vá em:
   **settings → environment variables**
   
   adicione:
   - **name:** `ANTHROPIC_API_KEY`
   - **value:** `sk-ant-...` (sua chave)
   - marque: production, preview, development
   - clique em "save"

6. vá em **deployments → clique nos três pontos → redeploy**
   (necessário para as variáveis de ambiente entrarem em vigor)

7. sua url estará disponível em: `https://seu-projeto.vercel.app`

---

### opção B — deploy pelo cli (terminal)

```bash
# entre na pasta do projeto
cd gerador-roteiros-vercel

# faça login na vercel
vercel login

# deploy
vercel

# siga as instruções:
# - set up and deploy? → y
# - which scope? → sua conta
# - link to existing project? → n
# - project name? → gerador-roteiros (ou o nome que quiser)
# - in which directory is your code located? → ./
# - want to modify settings? → n

# adicione a api key
vercel env add ANTHROPIC_API_KEY
# cole sua chave quando pedir

# deploy final para produção
vercel --prod
```

---

### passo 4 — testar

acesse a url que a vercel gerou (ex: `https://gerador-roteiros.vercel.app`)

preencha o formulário e clique em "gerar roteiro completo" — deve funcionar sem nenhum erro de cors.

---

### custos estimados

- **vercel:** gratuito no plano hobby (até 100gb de bandwidth, funções serverless ilimitadas)
- **anthropic:** ~$0.003 por roteiro gerado (claude sonnet)
  - 100 roteiros/mês ≈ $0.30

---

### domínio personalizado (opcional)

1. em https://vercel.com, abra seu projeto
2. vá em settings → domains
3. adicione seu domínio (ex: `roteiros.mustmedia.com.br`)
4. siga as instruções de dns que a vercel mostrar

---

### dúvidas frequentes

**"function timeout" no log da vercel**
→ roteiros longos (3min) podem demorar. aumente o timeout em vercel.json:
```json
{
  "functions": {
    "api/gerar.js": { "maxDuration": 30 }
  }
}
```

**"anthropic_api_key não configurada"**
→ a variável de ambiente não foi salva corretamente. refaça o passo 3 e faça redeploy.

**ainda aparece cors error**
→ certifique-se que o fetch no html aponta para `/api/gerar` (relativo), não para `api.anthropic.com` diretamente.
