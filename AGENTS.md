# AGENTS.md — IASolution Cloud

Guia de referência para agentes de IA (e humanos) que trabalham neste repositório.
Leia tudo antes de editar qualquer coisa.

---

## 1. O que é este projeto

Site estático da **IASolution Cloud** (Fortaleza, CE), empresa de inteligência
artificial e automação. É 100% **HTML + CSS + JavaScript puro** — sem framework,
sem build, sem dependências. O que está no repositório é o que vai para o ar.

Hospedado em **VPS Hostinger** (Docker + Nginx). O domínio é `iasolution.cloud`.

O repositório contém **três produtos distintos**:

| Produto | Arquivo(s) | URL pública |
|---|---|---|
| Site institucional | `index.html` | `https://iasolution.cloud/` |
| Cartão digital virtual | `card/index.html` | `https://iasolution.cloud/card/` |
| Cartão de visita imprimível | `cartao.html` + `cartao.pdf` | não publicado (arquivo local/gráfica) |

---

## 2. Mapa de arquivos

```
├── AGENTS.md          ← este guia
├── README.md          ← passo a passo de deploy na VPS
├── index.html         ← site institucional (EN/PT/ES)
├── card/
│   └── index.html     ← cartão digital (link clicável por contato)
├── cartao.html        ← frente/verso do cartão físico (para imprimir)
├── cartao.pdf         ← PDF exportado do cartao.html (imprimir)
├── contato.vcf        ← vCard: botão "Salvar Contato" do cartão digital
├── logo-whatsapp.png  ← logo WhatsApp (arquivo do .vcf / recursos)
├── logo-whatsapp.svg  ← logo WhatsApp em vetor
└── CNAME              ← usado em GitHub Pages (não afeta a VPS)
```

---

## 3. Identidade visual (padrão em todo o projeto)

NUNCA mude essas cores sem ser pedido explicitamente:

- **Ciano (primário):** `#00d4ff`
- **Roxo (acento):** `#7000ff`
- **Fundo escuro:** `#0a0a0a` / `#050608`
- **Fonte:** `Inter` (Google Fonts)
- **Gradiente decorativo:** `linear-gradient(to right, #00d4ff, #7000ff)` (e variações)
- **Logo "AI":** duas letras sobrepostas em SVG (`A` roxo + `I` ciano), fonte `Arial Black`

---

## 4. Site institucional — `index.html`

- Seleção de idioma **EN / PT / ES** (botões no topo).
- As traduções ficam num objeto `translations` no `<script>` (linha ~220).
- Cada texto traduzível tem um `id` único no HTML e é atualizado via `setLang()`.
- **Para adicionar um texto:** adicione a chave nos 3 idiomas (`en`, `pt`, `es`)
  e, se for um elemento novo, mapeie-o dentro de `setLang()`.
- **Para adicionar um projeto:** duplique um bloco `.project-card` no HTML e,
  se o texto variar por idioma, crie as chaves `p6`, `p7`, ... nos 3 idiomas.
- O botão "Our Projects" mostra/oculta a lista (`toggleProjects()`).
- Bloqueio de `contextmenu` e `dragstart` no fim do script (proteção contra cópia).

---

## 5. Cartão digital — `card/index.html`

Cartão virtual acessível em `https://iasolution.cloud/card/`.

Contatos exibidos (todos devem estar consistentes nos demais arquivos):

- **WhatsApp:** `https://wa.me/5585992736922` → `(85) 99273-6922`
- **E-mail:** `contato@iasolution.cloud`
- **Site:** `https://iasolution.cloud`
- **Localização:** Fortaleza, CE — Brasil
- **Salvar Contato:** baixa `contato.vcf` (`href="/contato.vcf" download`)

Estrutura dos botões: cada item é um `<a class="contato-item">` com
`<div class="icon">` (SVG inline) + `<div class="texto">` (label + valor).
O WhatsApp usa a classe extra `btn-whatsapp`; o "Salvar Contato", `btn-salvar`.

---

## 6. Cartão físico — `cartao.html` e `cartao.pdf`

O `cartao.html` desenha **frente e verso** do cartão de visita (90×50mm),
lado a lado, para impressão.

- **Frente:** logo, nome **Jonas Angelo**, cargo "Fundador & Desenvolvedor",
  e 4 contatos (e-mail, site, telefone, cidade).
- **Verso:** logo grande, tagline "Soluções Inteligentes", site e **QR code**.
- O **QR code** é gerado pela API externa
  `https://api.qrserver.com/v1/create-qr-code/?...` e aponta para
  `https://iasolution.cloud/card/`.

> ⚠️ **IMPORTANTE — barra final nas URLs:**
> O Nginx da VPS **não redireciona** `/card` → `/card/`. Sem a barra, dá **404**
> (foi exatamente o que aconteceu no link do Instagram). Regra de ouro:
> **sempre use `https://iasolution.cloud/card/` (com a barra) em qualquer URL
> que apareça em arquivos, QR codes ou redes sociais.** O navegador/leitor de QR
> nunca adiciona barra extra quando a URL já termina em `/`.

### Regenerar o `cartao.pdf` depois de editar `cartao.html`

O PDF é gerado pelo Chrome headless (ele reproduz o HTML com fonte e layout reais):

```powershell
# 1. Copiar o HTML para um caminho SEM espaços (o Chrome falha com espaços na URL)
Copy-Item "cartao.html" "$env:TEMP\cartao.html"

# 2. Gerar o PDF (Letter, sem margens e sem cabeçalho de data)
& "C:\Program Files\Google\Chrome\Application\chrome.exe" `
  "--headless=new" "--disable-gpu" "--no-sandbox" `
  "--user-data-dir=$env:TEMP\chrome_profile" `
  "--print-to-pdf=$env:TEMP\cartao_final.pdf" `
  "--no-margins" "--no-pdf-header-footer" `
  "file:///C:/Users/JONAS/AppData/Local/Temp/cartao.html"

# 3. Substituir o PDF do projeto
Copy-Item "$env:TEMP\cartao_final.pdf" "cartao.pdf" -Force
```

Regras:
- Nunca edite o `.pdf` manualmente — sempre regenere a partir do `.html`.
- O `cartao.pdf` tem **1 página**, formato **Letter (612×792 pt)** e **1 imagem** (o QR).
- Ao regerar, o QR passa a conter a URL mais recente do `cartao.html`.

---

## 7. vCard — `contato.vcf`

Formato `VCARD 3.0`, baixado pelo botão "Salvar Contato" do cartão digital.

Campos:
- `FN`: Jonas Angelo
- `ORG`: IASolution Forge
- `TITLE`: Fundador & Desenvolvedor
- `TEL`: +55 85 99273-6922
- `EMAIL`: contato@iasolution.cloud
- `URL`: https://iasolution.cloud
- `ADR`: Fortaleza, CE, Brasil
- `NOTE`: Soluções Inteligentes — https://iasolution.cloud/card/  (com barra!)

Se alterar algum contato (telefone/e-mail/site), atualize **simultaneamente** em:
`contato.vcf`, `cartao.html`, `card/index.html` (e regenere o PDF).

---

## 8. Fluxo de trabalho e deploy

### Desenvolvimento local
Nenhum servidor ou build é necessário. Abra os `.html` direto no navegador.
O `.vscode/settings.json` define a porta **5501** para o Live Server.

### Publicar (VPS Hostinger)

```bash
# 1. Na pasta do projeto (local): enviar as mudanças
git add .
git commit -m "descrição curta do que mudou"
git push origin main

# 2. Na VPS (via SSH)
cd /root/iasolution-website/
git pull origin main
docker compose up -d --build --force-recreate iasolution-website
```

### Regras de commit
- Mensagens curtas em inglês (padrão do histórico, ex.: `fix: ...`, `feat: ...`).
- Só faça commit/push quando o usuário pedir.
- NUNCA commite segredos, chaves ou tokens.

---

## 9. Armadilhas comuns (resumo)

1. **URLs sem a barra final** → 404. Use sempre `/card/`.
2. **Editar `cartao.pdf` à mão** → impossível; regenere do HTML.
3. **Alterar contato só num arquivo** → o resto fica desatualizado. Procure em
   `contato.vcf`, `cartao.html` e `card/index.html` (e regenere o PDF).
4. **Espaços no caminho ao chamar o Chrome headless** → o print falha; use um
   caminho sem espaços (ex.: `%TEMP%`).
5. **QR code**: a imagem vem da API do qrserver — precisa de internet no momento
   do print/abertura. O conteúdo (o que o QR codifica) é o `data=` da URL.

---

## 10. Dados de contato oficiais (fonte da verdade)

- Nome: **Jonas Angelo** — Fundador & Desenvolvedor
- Telefone/WhatsApp: **(85) 99273-6922** → `wa.me/5585992736922`
- E-mail: **contato@iasolution.cloud**
- Site: **iasolution.cloud**
- Cartão digital: **iasolution.cloud/card/**
