# Guia Completo: Mapas Mentais Interativos com Google NotebookLM, Markmap e GitHub Pages

> **Tutorial prático:** Como transformar materiais de estudo e anotações geradas por IA (Google NotebookLM / Gemini) em mapas mentais interativos em Markdown e publicá-los automaticamente na web via GitHub Pages com CI/CD.

---

## 📑 Sumário
1. [Visão Geral da Arquitetura](#-visão-geral-da-arquitetura)
2. [Passo 1: Gerando o Conteúdo Estruturado com NotebookLM](#-passo-1-gerando-o-conteúdo-estruturado-com-notebooklm)
3. [Passo 2: Estrutura do Arquivo Markdown com Markmap](#-passo-2-estrutura-do-arquivo-markdown-com-markmap)
4. [Passo 3: Visualização e Edição no VS Code](#-passo-3-visualização-e-edição-no-vs-code)
5. [Passo 4: Pipeline de Build e Deploy Automático (GitHub Actions)](#-passo-4-pipeline-de-build-e-deploy-automático-github-actions)
6. [Passo 5: Ativando o GitHub Pages no Repositório](#-passo-5-ativando-o-github-pages-no-repositório)
7. [Dicas, Armadilhas Comuns e Boas Práticas](#-dicas-armadilhas-comuns-e-boas-práticas)

---

## 🧭 Visão Geral da Arquitetura

O fluxo completo conecta as seguintes ferramentas:

```
[Fontes / PDFs / Slides]
        │
        ▼
[Google NotebookLM / Gemini]  ── (Prompt estruturado) ──>  Arquivo .md hierárquico
        │
        ▼
[Extensão Markmap (VS Code)]  ── (Visualização local & ajustes)
        │
        ▼
[Git Push para o GitHub]
        │
        ▼
[GitHub Actions Workflow]
   ├── Instala markmap-cli (Node.js)
   ├── Compila todos os .md em HTMLs interativos independentes (offline)
   ├── Gera portal responsivo (index.html) com cards para cada mapa
   └── Publica no GitHub Pages
```

---

## 🧠 Passo 1: Gerando o Conteúdo Estruturado com NotebookLM

O **Google NotebookLM** permite carregar fontes (PDFs de aulas, artigos, anotações de aula) e extrair conhecimento fundamentado nelas.

### Prompt Recomendado para o NotebookLM / Gemini

No chat do NotebookLM, após carregar suas fontes, utilize um comando como o abaixo:

```markdown
Com base nas fontes carregadas, estruture um resumo completo em formato hierárquico Markdown para visualização em Mapa Mental (compatível com Markmap).

Regras de formatação:
1. Use `#` para o Título Principal.
2. Use `##` para os Módulos / Grandes Áreas.
3. Use `###` para os Tópicos e Conceitos.
4. Use listas com `-` e sublistas indentadas com 2 espaços para detalhar definições, exemplos práticos, prós e contras.
5. Use negrito (`**termo**`) nas palavras-chave e destaques.
6. Evite blocos de texto longos ou parágrafos extensos; prefira nós objetivos e concisos.
```

---

## 🗺️ Passo 2: Estrutura do Arquivo Markdown com Markmap

O **Markmap** transforma Markdown puro (títulos e listas aninhadas) em diagramas interativos baseados em D3.js e SVG.

Crie seus arquivos dentro de uma pasta dedicada, por exemplo `mapas-mentais/topicos.md`:

```markdown
---
markmap:
  colorFreezeLevel: 2    # Congela a paleta de cores a partir do nível 2 (cada módulo com cor única)
  initialExpandLevel: 2  # Inicia recolhido no 2º nível, evitando poluição visual imediata
  maxWidth: 380          # Limita a largura máxima de cada nó (em pixels)
---

# 🧭 Tópicos em Banco de Dados e Big Data

## 🏛️ Módulo 1: Evolução Histórica
### 📜 Armazenamento Físico
- **Marcos Históricos**
  - Epopeia de Gilgamesh (3.500 a.C.)
  - Papiro de Ani (2.200 a.C.)
  - Biblioteca de Alexandria (283 a.C.)
- **Problemas**
  - Vulnerabilidade física
  - Indexação manual

## 🍃 Módulo 2: Bancos NoSQL
### 🎯 Os 3 Pilares
- **Esquema Flexível**
- **Escalabilidade Horizontal (Scale-Out)**
- **Alta Disponibilidade**
```

### Principais Opções de Configuração no Frontmatter (`---`):

| Configuração | Descrição | Exemplo Recomendado |
|---|---|---|
| `initialExpandLevel` | Nível hierárquico inicial expandido ao carregar a página | `2` (mantém o mapa limpo) |
| `colorFreezeLevel` | Nível em que a cor da ramificação é fixada para seus filhos | `2` (ramos temáticos consistentes) |
| `maxWidth` | Largura máxima dos blocos de texto antes de quebrar linha | `300` a `400` |
| `duration` | Tempo em ms das animações de abrir/fechar nós | `500` |

---

## 💻 Passo 3: Visualização e Edição no VS Code

Para visualizar e navegar no mapa mental em tempo real na sua máquina:

1. Abra o **VS Code**.
2. Abra a aba de Extensões (`Ctrl + Shift + X`).
3. Procure por **`Markmap`** (autor: *gera2ld*) e clique em **Instalar**.
4. Abra qualquer arquivo `.md` e:
   - Clique no ícone do Markmap no canto superior direito do editor, ou
   - Pressione `Ctrl + Shift + P` e execute: `Markmap: Open preview`.

---

## ⚙️ Passo 4: Pipeline de Build e Deploy Automático (GitHub Actions)

Para que qualquer pessoa possa navegar pelos mapas mentais no navegador sem precisar clonar o projeto ou instalar extensões, crie uma Action de CI/CD.

Crie o arquivo [`.github/workflows/deploy-mindmaps.yml`](file:///.github/workflows/deploy-mindmaps.yml):

```yaml
name: Deploy Markmaps to GitHub Pages

on:
  push:
    branches:
      - main # Dispara a action automaticamente no push para a branch main
  workflow_dispatch: # Permite disparar o deploy manualmente

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4.2.2

      - name: Setup Node.js
        uses: actions/setup-node@v4.2.0
        with:
          node-version: 24

      - name: Create Build Directory
        run: mkdir -p dist

      - name: Compile Markmaps to HTML and Generate Portal
        run: |
          # 1. Instala o markmap-cli globalmente
          npm install -g markmap-cli

          # 2. Prepara arquivo de cards
          > cards.html

          # 3. Compila cada arquivo .md dentro de mapas-mentais/
          find mapas-mentais/ -maxdepth 2 -type f -name "*.md" | sort | while IFS= read -r file; do
            filename=$(basename "$file" .md)
            echo "Compilando: $file -> dist/$filename.html"
            
            # Gera HTML autônomo (standalone) e offline
            markmap "$file" --no-open --offline -o "dist/$filename.html"
            
            title=$(grep -m 1 "^# " "$file" | sed 's/^# //' || echo "$filename")
            if [ -z "$title" ]; then
              title="$filename"
            fi
            
            badge="Mapa Conceitual"
            if [[ "$filename" == *"revisao"* ]]; then
              badge="Revisão & Questões"
            fi

            # Constrói o card de navegação (Atenção: 'CARD' deve respeitar a indentação base)
            cat <<CARD >> cards.html
            <a class='card' href='$filename.html'>
              <div class='icon-wrapper'>
                <span class='icon'>🗺️</span>
              </div>
              <div class='info'>
                <div class='card-header'>
                  <h2>$title</h2>
                  <span class='badge'>$badge</span>
                </div>
                <p class='filepath'>Origem: <code>$file</code></p>
              </div>
              <div class='arrow'>→</div>
            </a>
          CARD
          done

          LINKS_HTML=$(cat cards.html)
          rm -f cards.html

          # 4. Gera a página inicial do portal (dist/index.html)
          cat <<EOF > dist/index.html
          <!DOCTYPE html>
          <html lang="pt-BR">
          <head>
            <meta charset="UTF-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>Portal de Mapas Mentais</title>
            <style>
              body { font-family: system-ui, -apple-system, sans-serif; background: #0b0f19; color: #f8fafc; padding: 40px 20px; }
              .container { max-width: 900px; margin: 0 auto; }
              .grid { display: flex; flex-direction: column; gap: 16px; margin-top: 24px; }
              .card { display: flex; align-items: center; justify-content: space-between; background: rgba(30, 41, 59, 0.7); border: 1px solid rgba(148, 163, 184, 0.2); padding: 18px 24px; border-radius: 12px; text-decoration: none; color: inherit; transition: all 0.2s ease; }
              .card:hover { transform: translateY(-2px); border-color: #38bdf8; background: rgba(51, 65, 85, 0.8); }
              .badge { font-size: 0.75rem; background: rgba(56, 189, 248, 0.15); color: #38bdf8; padding: 4px 10px; border-radius: 9999px; }
              .filepath { color: #94a3b8; font-size: 0.85rem; margin: 4px 0 0 0; }
            </style>
          </head>
          <body>
            <div class="container">
              <h1>Portal de Mapas Mentais</h1>
              <p>Selecione um mapa para visualização interativa:</p>
              <main class="grid">
                $LINKS_HTML
              </main>
            </div>
          </body>
          </html>
          EOF

      - name: Setup Pages
        uses: actions/configure-pages@v5

      - name: Upload Pages Artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: 'dist'

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

---

## 🚀 Passo 5: Ativando o GitHub Pages no Repositório

Para que o GitHub permita a publicação via Actions:

1. No seu repositório no GitHub, vá em **Settings** (Configurações).
2. No menu lateral esquerdo, clique em **Pages**.
3. Na seção **Build and deployment**:
   - Em **Source**, mude de `Deploy from a branch` para **`GitHub Actions`**.
4. Faça um push na branch `main`:
   ```bash
   git add .
   git commit -m "feat: adiciona mapas mentais e pipeline de deploy"
   git push origin main
   ```
5. Acompanhe a execução na aba **Actions**. Assim que concluir, a URL pública do seu portal estará disponível no topo do workflow!

---

## 💡 Dicas, Armadilhas Comuns e Boas Práticas

### 1. Cuidados com HereDoc (`cat <<TAG`) dentro do GitHub Actions
- Em blocos `run: |` do YAML, o delimitador final do shell (`CARD` ou `EOF`) deve respeitar a **indentação base do bloco YAML** (geralmente 10 espaços).
- Se ficar na coluna 1 (sem recuo), o YAML falhará com erro de `Implicit keys need to be on a single line`.
- Se tiver espaços a mais que a base do YAML, o Bash não reconhecerá a palavra de fechamento e gerará erro de `syntax error: unexpected end of file`.

### 2. Opção `--offline` do Markmap
- O parâmetro `markmap --offline` embute todas as dependências JavaScript/CSS do D3 e Markmap diretamente no arquivo HTML gerado.
- Isso garante que os mapas funcionem sem falhas de CDN externo, bloqueadores de rastreamento ou redes lentas.

### 3. Organização de Vários Mapas
- Ao adicionar qualquer novo arquivo `.md` dentro da pasta `mapas-mentais/`, a pipeline do GitHub Actions detecta-o automaticamente, gera o HTML correspondente e cria o card na página inicial sem necessidade de alterar o código do workflow!
