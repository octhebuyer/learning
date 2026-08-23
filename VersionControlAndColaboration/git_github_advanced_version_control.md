# Controlo de Versões Avançado & Colaboração no Git e GitHub

O tópico **Controlo de Versões Avançado & Colaboração** representa o núcleo técnico da engenharia de software no ecossistema Git e GitHub. Dominar esta camada permite manipular o histórico de código de forma não destrutiva, resolver conflitos complexos e manter um fluxo de integração contínua limpo.

---

## 1. Estratégias de Branching e Fluxos de Trabalho

Os fluxos de trabalho definem como a equipa organiza o trabalho em paralelo e faz a integração de alterações no repositório.

* **GitHub Flow:** Modelo leve e focado em *Continuous Deployment*. Existe uma ramificação principal única (`main`) sempre pronta para produção. Toda a alteração é feita numa ramificação temporária (*feature branch*), submetida via *Pull Request*, revista e integrada após aprovação.
  * [Documentação Oficial do GitHub Flow](https://docs.github.com/get-started/using-github/github-flow)
* **Trunk-Based Development:** Modelo em que os programadores fundem pequenas alterações com frequência diária numa única ramificação principal (*trunk* ou `main`). Utiliza *feature flags* para ocultar código incompleto em produção, reduzindo a complexidade de *merges* demorados.
* **Git Flow:** Modelo tradicional baseado em ramificações de longa duração (`main`, `develop`, `feature/*`, `release/*`, `hotfix/*`). Indicado para software com ciclos formais de lançamento e versões legadas paralelas.

---

## 2. Operações Avançadas de Git

Operações essenciais para refatorar o histórico de commits e diagnosticar problemas em repositórios de grande escala.

* **Interactive Rebase (`git rebase -i`):** Reescreve a sequência de *commits* de uma ramificação sobre uma nova base. Permite alterar a ordem, fundir múltiplos *commits* num só (*squash*), editar mensagens ou eliminar alterações antigas antes da integração.
  * [Documentação Oficial do Git - Rebase](https://git-scm.com/docs/git-rebase)
* **Git Bisect (`git bisect`):** Ferramenta de depuração que utiliza **pesquisa binária** no histórico de *commits* para identificar o *commit* exato que introduziu uma regressão ou *bug*.
  * [Documentação Oficial do Git - Bisect](https://git-scm.com/docs/git-bisect)
* **Git Reflog (`git reflog`):** Registo local de todas as alterações feitas às referências (`HEAD`, *branches*). Permite recuperar *commits* ou ramificações eliminadas acidentalmente após execuções incorretas de `git reset` ou `git rebase`.
  * [Documentação Oficial do Git - Reflog](https://git-scm.com/docs/git-reflog)
* **Git Cherry-Pick (`git cherry-pick`):** Aplica as alterações introduzidas por um *commit* específico de outra ramificação na ramificação atual, sem necessidade de efetuar a fusão completa da ramificação de origem.
  * [Documentação Oficial do Git - Cherry-Pick](https://git-scm.com/docs/git-cherry-pick)
* **3-Way Merge e Resolução de Conflitos:** Algoritmo de fusão do Git que utiliza o *commit* comum mais recente (*merge base*) entre duas ramificações para calcular as diferenças em relação às duas pontas (*heads*), isolando apenas as linhas onde houve alterações concorrentes.
  * [Documentação Oficial do Git - Basic Merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)

---

## 3. Review de Código e Revisão Colaborativa no GitHub

A camada de colaboração que garante a qualidade do código através de revisão por pares e automação de regras.

* **Pull Requests (PR):** Proposta formal de integração de código de uma ramificação para outra no GitHub. Serve como ponto central para discussão, execução automática de testes (CI/CD) e aprovação de alterações.
  * [Documentação Oficial do GitHub - About Pull Requests](https://docs.github.com/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)
* **Draft Pull Requests:** PRs criados em estado de rascunho. Impedem a fusão acidental e não disparam revisões automáticas ou notificações a revisores até que o rascunho seja convertido para o estado final (*Ready for Review*).
  * [Documentação Oficial do GitHub - Draft Pull Requests](https://docs.github.com/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-draft-pull-requests)
* **PR Templates:** Ficheiros em formato Markdown (`.github/PULL_REQUEST_TEMPLATE.md`) que pré-preenchem o corpo do PR com listas de verificação, contexto e requisitos que o autor deve fornecer antes da revisão.
  * [Documentação Oficial do GitHub - Creating a PR Template](https://docs.github.com/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository)
* **Review Threads & Suggested Changes:** Funcionalidade de revisão no GitHub que permite fazer comentários linha a linha e sugerir alterações de código diretamente na interface. O autor do PR pode aceitar a sugestão com um único clique, gerando um novo *commit* automático.
  * [Documentação Oficial do GitHub - Incorporating Suggested Changes](https://docs.github.com/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/incorporating-feedback-in-your-pull-request)
