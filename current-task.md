# 📝 Plano de Execução

## Criação do Repositório Central de CI
- **Descrição**: Estruturar o repositório como um catálogo de reusable workflows do GitHub Actions para projetos Python, centralizando jobs de segurança, testes e lint.
- **Status**: ✅ Concluído

### Criar configs e estrutura de pastas
- **Descrição**: Criar a estrutura de pastas para armazenar configs de cada biblioteca e os arquivos de configuração padrão (`bandit.yaml` e `ruff.toml`).
- **Resultado esperado**: Pastas `security/bandit/configs/`, `security/semgrep/configs/` e `lint/ruff/configs/` criadas com os arquivos de configuração prontos.
- **Status**: ✅ Concluído
- **Arquivos Relacionados**:
  - `security/bandit/configs/bandit.yaml`
  - `lint/ruff/configs/ruff.toml`
- **Dependencias**: Nenhuma

### Criar workflow security.yml
- **Descrição**: Reusable workflow com três jobs independentes: `bandit` (análise estática de segurança), `pip-audit` (vulnerabilidades em pacotes) e `semgrep` (análise por padrões). Cada job pode ser ativado/desativado individualmente. O bandit suporta config dinâmica via input.
- **Resultado esperado**: Workflow funcional que pode ser chamado via `uses: <org>/ci/.github/workflows/security.yml@main`.
- **Status**: ✅ Concluído
- **Arquivos Relacionados**: `.github/workflows/security.yml`
- **Dependencias**: Criação do `bandit.yaml`

### Criar workflow test.yml
- **Descrição**: Reusable workflow com job `pytest`. O path do diretório de testes é obrigatório via input. Suporte a argumentos customizados do pytest e controle de instalação de dependências.
- **Resultado esperado**: Workflow funcional chamável via `uses: <org>/ci/.github/workflows/test.yml@main`.
- **Status**: ✅ Concluído
- **Arquivos Relacionados**: `.github/workflows/test.yml`
- **Dependencias**: Nenhuma

### Criar workflow lint.yml
- **Descrição**: Reusable workflow com job `ruff`. Suporta config dinâmica via input; quando não fornecida, usa o `ruff.toml` do repositório central.
- **Resultado esperado**: Workflow funcional chamável via `uses: <org>/ci/.github/workflows/lint.yml@main`.
- **Status**: ✅ Concluído
- **Arquivos Relacionados**: `.github/workflows/lint.yml`
- **Dependencias**: Criação do `ruff.toml`

### Atualizar documentação
- **Descrição**: Atualizar `README.md` com documentação completa dos workflows, inputs disponíveis, exemplos de uso e estrutura do repositório.
- **Resultado esperado**: README completo e funcional como referência para usuários do catálogo de CI.
- **Status**: ✅ Concluído
- **Arquivos Relacionados**: `README.md`, `current-task.md`
- **Dependencias**: Todos os workflows criados

---

## Workflow de Build de Imagens Docker
- **Descrição**: Criar reusable workflow `build.yml` para construir imagens Docker e fazer push para o GitHub Container Registry (GHCR), com versionamento extraído do `pyproject.toml` e verificação de tag duplicada.
- **Status**: ✅ Concluído

### Criar workflow build.yml
- **Descrição**: Reusable workflow com job `build-and-push`. Aceita `dockerfile-path` como input opcional (padrão: `Dockerfile` na raiz). Extrai a versão do `pyproject.toml`, monta a tag `<repo>:<version>`, verifica se a tag já existe no GHCR antes do push, e constrói/publica a imagem usando as actions oficiais do Docker.
- **Resultado esperado**: Workflow funcional chamável via `uses: <org>/ci/.github/workflows/build.yml@main` que falha explicitamente se: Dockerfile não encontrado, `pyproject.toml` ausente ou sem versão, ou tag já existir no GHCR.
- **Status**: ✅ Concluído
- **Arquivos Relacionados**: `.github/workflows/build.yml`
- **Dependencias**: Nenhuma

### Atualizar documentação do build.yml
- **Descrição**: Atualizar `README.md` com seção do novo workflow de Docker Build & Push, incluindo tabela de inputs, exemplo de uso e permissões necessárias. Registrar decisões no `decision-log.md`.
- **Resultado esperado**: Documentação atualizada e consistente com os demais workflows.
- **Status**: ✅ Concluído
- **Arquivos Relacionados**: `README.md`, `decision-log.md`
- **Dependencias**: Criação do `build.yml`
