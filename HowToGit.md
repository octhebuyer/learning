# Guia Completo de Git: Do Repositório ao Merge

## Índice
1. [Criar um Repositório](#1-criar-um-repositório)
2. [Configuração Inicial](#2-configuração-inicial)
3. [Criar um Branch](#3-criar-um-branch)
4. [Fazer Commits](#4-fazer-commits)
5. [Enviar para Remoto](#5-enviar-para-remoto)
6. [Criar um Pull Request](#6-criar-um-pull-request)
7. [Review e Feedback](#7-review-e-feedback)
8. [Merge para Main](#8-merge-para-main)
9. [Limpeza Pós-Merge](#9-limpeza-pós-merge)

---

## 1. Criar um Repositório

### Opção A: Criar um repositório novo localmente

```bash
# Criar uma pasta para o projeto
mkdir meu-projeto
cd meu-projeto

# Inicializar um repositório Git vazio
git init

# Criar o primeiro arquivo (exemplo)
echo "# Meu Projeto" > README.md

# Adicionar o arquivo à staging area
git add README.md

# Fazer o primeiro commit
git commit -m "Initial commit: README"
```

### Opção B: Clonar um repositório existente

```bash
# Clonar um repositório remoto (GitHub, GitLab, etc.)
git clone https://github.com/usuario/projeto.git
cd projeto
```

### Opção C: Criar via GitHub/GitLab

1. Abra https://github.com/new
2. Preencha:
   - **Repository name**: `meu-projeto`
   - **Description**: Uma descrição do projeto
   - **Public/Private**: Escolha a visibilidade
   - ✅ Inicializar com README (opcional)
3. Clique em **Create repository**
4. Clone para seu computador:

```bash
git clone https://github.com/seu-usuario/meu-projeto.git
cd meu-projeto
```

---

## 2. Configuração Inicial

Antes de começar a trabalhar, configure suas credenciais:

```bash
# Configurar nome (aparece em todos os commits)
git config user.name "Seu Nome"
git config user.email "seu.email@exemplo.com"

# Ver a configuração
git config --list

# Configurar globalmente (para todos os projetos)
git config --global user.name "Seu Nome"
git config --global user.email "seu.email@exemplo.com"
```

---

## 3. Criar um Branch

Um **branch** é como uma "cópia paralela" do projeto onde você trabalha sem afetar a `main`.

### Ver branches existentes

```bash
# Ver branches locais
git branch

# Ver todos os branches (incluindo remotos)
git branch -a

# Ver branches remotos
git branch -r
```

### Criar um novo branch

```bash
# Criar um branch novo baseado na main
git branch meu-novo-branch

# Criar e mudar para o novo branch (recomendado)
git checkout -b meu-novo-branch

# Forma mais recente (Git 2.23+)
git switch -c meu-novo-branch
```

### Convenção de nomes para branches

Use nomes descritivos e claros:

```bash
# ✅ Bons nomes
git checkout -b feature/login-usuarios
git checkout -b fix/bug-password-reset
git checkout -b docs/atualizar-readme
git checkout -b refactor/limpar-codigo

# ❌ Evite
git checkout -b teste
git checkout -b novo
git checkout -b branch1
```

### Verificar em qual branch você está

```bash
# Mostra o branch atual
git branch

# Resultado exemplo:
# main
# * meu-novo-branch  ← asterisco indica o branch atual
```

---

## 4. Fazer Commits

Agora que está num branch, você pode fazer alterações:

### Workflow básico

```bash
# 1. Editar um arquivo (use o seu editor favorito)
# vim src/main.py
# ou abra num editor visual (VS Code, etc.)

# 2. Ver o status (ficheiros modificados)
git status

# 3. Ver as mudanças (diff)
git diff src/main.py

# 4. Adicionar ficheiros à staging area (prepare para commit)
git add src/main.py

# 5. Adicionar TODOS os ficheiros modificados
git add .

# 6. Ver o que vai ser commitado
git status
git diff --staged

# 7. Fazer o commit com uma mensagem clara
git commit -m "Adicionar funcionalidade de login"
```

### Boas práticas para mensagens de commit

```bash
# ✅ Bom formato
git commit -m "Adicionar validação de email no formulário"
git commit -m "Fix: corrigir erro de autenticação"
git commit -m "Refactor: simplificar função de parsing"

# ✅ Formato mais descritivo (para mudanças complexas)
git commit -m "Adicionar sistema de cache

- Implementar cache em memória
- Adicionar teste unitário
- Atualizar documentação"

# ❌ Evite
git commit -m "Mudanças"
git commit -m "update"
git commit -m "asdfgh"
```

### Corrigir o último commit

```bash
# Se esqueceu de adicionar um ficheiro
git add ficheiro-esquecido.py
git commit --amend --no-edit

# Se quer alterar a mensagem do último commit
git commit --amend -m "Nova mensagem"
```

### Ver histórico de commits

```bash
# Ver commits recentes
git log

# Ver em linha
git log --oneline

# Ver últimos 5 commits
git log -5

# Ver com visual de branches
git log --graph --oneline --all
```

---

## 5. Enviar para Remoto

Agora envie seu branch para o servidor (GitHub, GitLab, etc.):

```bash
# Fazer push do branch para o repositório remoto
git push origin meu-novo-branch

# Primeira vez? Git pode sugerir:
git push --set-upstream origin meu-novo-branch

# Versão abreviada (após a primeira vez)
git push

# Ver branches remotos
git branch -r
```

### O que acontece

```
Seu Computador          GitHub/GitLab
[meu-novo-branch]  -->  [meu-novo-branch]
                        (no repositório remoto)
```

---

## 6. Criar um Pull Request

Um **Pull Request (PR)** é o pedido oficial para mesclar seu branch na `main`.

### Via GitHub (exemplo mais comum)

1. **Acesse o repositório** no GitHub
2. **Procure pela notificação** "Compare & pull request"
   - Ou clique em **Pull requests** > **New pull request**
3. **Configurar o PR**:
   - **Base branch**: `main` (onde quer enviar)
   - **Compare branch**: `meu-novo-branch` (seu branch)
4. **Clique em "Create pull request"**
5. **Preencha o formulário**:
   - **Título**: Descrição breve
   - **Descrição**: Explique o que fez, por quê, e como testar
6. **Clique em "Create pull request"**

### Exemplo de descrição boa

```markdown
## Descrição
Implementação do sistema de autenticação com JWT.

## O que mudou
- Adicionado endpoint POST /login
- Adicionado middleware de autenticação
- Adicionada validação de tokens

## Como testar
1. npm install
2. npm run dev
3. POST http://localhost:3000/login com { email, password }
4. Deve receber um token JWT

## Screenshots (se relevante)
[Opcional: imagens da interface]

## Closes
Fecha a issue #123
```

### Via GitLab (alternativa)

1. Acesse o projeto no GitLab
2. **Merge Requests** > **New merge request**
3. Configure source e target branches
4. Preencha o título e descrição
5. Clique em **Create merge request**

---

## 7. Review e Feedback

Enquanto o PR está aberto, outras pessoas podem revisar:

### Como um reviewer comenta

Reviewers podem:
- ✅ Comentar linhas de código específicas
- ✅ Sugerir mudanças
- ✅ Aprovar o PR
- ❌ Rejeitar/Bloquear o PR

### Como você responde a feedback

```bash
# 1. Fazer as mudanças sugeridas
# vim src/main.py

# 2. Adicionar e fazer commit
git add src/main.py
git commit -m "Responder ao feedback: validação melhorada"

# 3. Fazer push (atualiza automaticamente o PR)
git push

# 4. Comentar no PR explicando as mudanças
# (via GitHub/GitLab interface)
```

**Dica**: Novos commits aparecem automaticamente no PR. Não precisa de fazer um novo PR.

### Ver comentários no PR

- Via interface GitHub/GitLab: Secção **Conversation**
- Ver diffs: Secção **Files changed**

---

## 8. Merge para Main

Após aprovação, é hora de mesclar o branch na `main`.

### Opção A: Merge via GitHub/GitLab (recomendado)

1. No PR aprovado, veja o botão **Merge pull request**
2. Escolha o tipo de merge:
   - **Create a merge commit** (padrão): Cria um commit de merge
   - **Squash and merge**: Comprime todos os commits num
   - **Rebase and merge**: Reaposta os commits na main
3. Clique em **Confirm merge**
4. **Delete branch** (opcional, mas recomendado)

### Opção B: Merge via linha de comando (manual)

```bash
# 1. Mudar para a branch main
git checkout main

# 2. Atualizar main com as mudanças do servidor
git pull origin main

# 3. Fazer merge do seu branch
git merge meu-novo-branch

# 4. Resolver conflitos (se houver)
# [editar ficheiros com conflito]
git add .
git commit -m "Resolver conflitos de merge"

# 5. Fazer push para atualizar o servidor
git push origin main
```

### Entender conflitos de merge

Se dois branches mudaram a mesma linha:

```bash
<<<<<<< HEAD (main)
versão do main
=======
versão do seu branch
>>>>>>> meu-novo-branch
```

**Resolver**:
1. Escolha qual versão manter
2. Elimine os marcadores `<<<<<<<`, `=======`, `>>>>>>>`
3. `git add .` e `git commit`

---

## 9. Limpeza Pós-Merge

Após o merge bem-sucedido:

```bash
# 1. Mudar para main
git checkout main

# 2. Atualizar seu main local
git pull origin main

# 3. Eliminar o branch localmente
git branch -d meu-novo-branch

# 4. Eliminar no servidor (se não foi via GitHub)
git push origin --delete meu-novo-branch

# 5. Ver branches (deve desaparecer)
git branch -a
```

**GitHub**: Após fazer merge, oferece automaticamente delete branch.

---

## Fluxo Completo em Um Exemplo

```bash
# 1. Começar do main atualizado
git checkout main
git pull origin main

# 2. Criar branch nova
git checkout -b feature/carrinho-compras

# 3. Fazer alterações e commits
# [editar ficheiros]
git add .
git commit -m "Adicionar funcionalidade de carrinho"

# [mais alterações]
git add .
git commit -m "Adicionar validação de preço"

# 4. Enviar para remoto
git push -u origin feature/carrinho-compras

# 5. [No GitHub] Criar Pull Request

# 6. [Reviewers comentam]

# 7. Responder ao feedback (se necessário)
git add .
git commit -m "Corrigir validação de preço"
git push

# 8. [No GitHub] Merge do PR

# 9. Limpeza local
git checkout main
git pull origin main
git branch -d feature/carrinho-compras
git push origin --delete feature/carrinho-compras
```

---

## Comandos Úteis Resumidos

```bash
# Branches
git branch                    # Ver branches
git checkout -b novo-branch   # Criar e trocar de branch
git checkout main             # Mudar de branch

# Trabalho diário
git status                    # Ver estado
git add .                     # Adicionar tudo
git commit -m "Mensagem"      # Fazer commit
git push                      # Enviar para remoto
git pull                      # Trazer do remoto

# Histórico
git log --oneline             # Ver commits
git diff                      # Ver mudanças não commitadas

# Undo
git restore ficheiro.txt      # Desfazer mudanças num ficheiro
git reset HEAD~1              # Desfazer último commit (manter ficheiros)

# Remoto
git remote -v                 # Ver repositórios remotos
git push origin branch        # Enviar branch
git pull origin branch        # Trazer branch
```

---

## Boas Práticas

✅ **Faça**:
- Commits pequenos e focados
- Mensagens descritivas
- Fazer push frequentemente
- Usar branches para tudo (até correções pequenas)
- Revisar seu próprio código antes de pedir review

❌ **Evite**:
- Commits gigantes com muitas mudanças
- "update", "fix", "teste" como mensagens
- Trabalhar diretamente em `main`
- Guardar trabalho por semanas sem push
- Fazer merge de branches com conflitos não resolvidos

---

## Descartar Alterações de um Branch Local

Às vezes precisa descartar tudo e começar do zero. Aqui estão as opções:

### Descartar alterações não commitadas (ficheiros modificados)

```bash
# Descartar tudo e voltar ao último commit
git restore .

# Ou (forma antiga, ainda funciona)
git checkout .

# Descartar um ficheiro específico
git restore src/main.py
```

### Descartar commits locais (mas manter ficheiros modificados)

```bash
# Desfazer o último commit (ficheiros ficam modificados)
git reset HEAD~1

# Desfazer os últimos 3 commits
git reset HEAD~3

# Desfazer até um commit específico (ver hash com git log --oneline)
git reset abc1234
```

### Descartar tudo (commits E ficheiros) ⚠️ CUIDADO!

```bash
# Volta ao último commit e apaga TUDO
git reset --hard HEAD

# Volta a um commit específico e apaga tudo após esse
git reset --hard abc1234

# Volta exatamente ao remoto (se o branch está no servidor)
git reset --hard origin/seu-branch
```

### Limpar ficheiros não rastreados (ficheiros novos nunca adicionados)

```bash
# Ver o que vai ser eliminado (sempre simular primeiro!)
git clean -fd --dry-run

# Eliminar ficheiros não rastreados
git clean -fd

# -f = force (eliminar)
# -d = diretórios também
```

### Tabela de Comparação Rápida

| Comando | O que faz | Muda Ficheiros? | Muda Commits? |
|---------|----------|-----------------|---------------|
| `git restore .` | Desfaz mudanças não commitadas | ✓ Desfaz | ✗ Mantém |
| `git reset HEAD~1` | Desfaz último commit | ✗ Mantém ficheiros | ✓ Desfaz commit |
| `git reset --hard HEAD` | Apaga tudo (IRREVERSÍVEL) | ✓ Apaga | ✓ Apaga |
| `git clean -fd` | Remove ficheiros novos | ✓ Apaga ficheiros novos | ✗ Não afeta |
| `git reset --hard origin/branch` | Volta exatamente ao servidor | ✓ Apaga | ✓ Apaga |

### Cenários Práticos de Descartar

#### Cenário 1: "Estraguei o branch, quero começar do zero"
```bash
# Voltar exatamente ao estado do servidor
git fetch origin
git reset --hard origin/seu-branch
```

#### Cenário 2: "Quero manter os ficheiros mas desfazer os commits"
```bash
# Desfaz último commit, ficheiros continuam modificados
git reset HEAD~1

# Agora pode fazer commits diferentes ou limpar
git restore .
```

#### Cenário 3: "Criei ficheiros que não quero"
```bash
# Ver o que vai apagar (sempre verificar!)
git clean -fd --dry-run

# Depois eliminar
git clean -fd
```

#### Cenário 4: "Tenho mudanças e quero voltar ao último commit"
```bash
# Desfazer todas as mudanças
git restore .
```

---

## Cenários Comuns

### Mudou de branch sem commitar

```bash
# Salvar mudanças temporariamente
git stash

# Mudar de branch
git checkout outra-branch

# Voltar ao branch anterior
git checkout primeira-branch

# Recuperar mudanças
git stash pop
```

### Quer começar do zero (desfazer tudo)

```bash
# Ver commits anteriores
git log --oneline

# Voltar a um commit anterior (mantém ficheiros)
git reset <commit-hash>

# Voltar e apagar mudanças (CUIDADO!)
git reset --hard <commit-hash>
```

### Sincronizar seu branch com main (se main mudou)

```bash
# Ir para main
git checkout main
git pull origin main

# Voltar ao seu branch
git checkout meu-branch

# Trazer mudanças da main para o seu branch
git rebase main

# Ou (mais seguro)
git merge main
```

---

**Bom trabalho! 🚀**
