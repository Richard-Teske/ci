# 🏗️ CI Central – Repositório Central de Pipelines

Repositório central de **reusable workflows** do GitHub Actions para projetos Python. Centraliza as jobs de CI para evitar repetição entre repositórios e facilitar a manutenção.

---

## 📦 Workflows disponíveis

| Workflow | Arquivo | Jobs incluídas |
|---|---|---|
| Segurança | `.github/workflows/security.yml` | `bandit`, `pip-audit`, `semgrep` |
| Testes | `.github/workflows/test.yml` | `pytest` |
| Lint | `.github/workflows/lint.yml` | `ruff` |

---

## 🚀 Como usar em outros repositórios

Nos seus workflows, referencie os workflows deste repositório via `uses`:

```yaml
jobs:
  security:
    uses: <org>/ci/.github/workflows/security.yml@main
    with:
      python-version: "3.11"

  tests:
    uses: <org>/ci/.github/workflows/test.yml@main
    with:
      tests-path: "tests/"

  lint:
    uses: <org>/ci/.github/workflows/lint.yml@main
```

> Substitua `<org>` pelo nome da sua organização ou usuário no GitHub.

---

## 🔒 security.yml

Varredura de segurança com três ferramentas independentes.

### Inputs

| Input | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `python-version` | string | não | `"3.11"` | Versão do Python |
| `bandit-enabled` | boolean | não | `true` | Ativa o job bandit |
| `bandit-config-path` | string | não | `""` | Path do `bandit.yaml` no repo chamador. Se vazio, usa o config central |
| `pip-audit-enabled` | boolean | não | `true` | Ativa o job pip-audit |
| `semgrep-enabled` | boolean | não | `true` | Ativa o job semgrep |

### Exemplo

```yaml
jobs:
  security:
    uses: <org>/ci/.github/workflows/security.yml@main
    with:
      python-version: "3.11"
      bandit-config-path: "config/bandit.yaml"  # usa config do próprio repo
      semgrep-enabled: false                      # desativa semgrep
```

### Config padrão bandit

Localização: [`security/bandit/configs/bandit.yaml`](security/bandit/configs/bandit.yaml)

---

## 🧪 test.yml

Execução de testes com pytest.

### Inputs

| Input | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `python-version` | string | não | `"3.11"` | Versão do Python |
| `tests-path` | string | **sim** | — | Path do diretório de testes |
| `pytest-args` | string | não | `"-v --tb=short"` | Argumentos adicionais para o pytest |
| `install-dependencies` | boolean | não | `true` | Instala dependências antes dos testes |

### Exemplo

```yaml
jobs:
  tests:
    uses: <org>/ci/.github/workflows/test.yml@main
    with:
      tests-path: "tests/"
      pytest-args: "-v --tb=long --cov=src"
```

---

## 🧹 lint.yml

Verificação de lint com ruff.

### Inputs

| Input | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `python-version` | string | não | `"3.11"` | Versão do Python |
| `ruff-enabled` | boolean | não | `true` | Ativa o job ruff |
| `ruff-config-path` | string | não | `""` | Path do `ruff.toml` no repo chamador. Se vazio, usa o config central |

### Exemplo

```yaml
jobs:
  lint:
    uses: <org>/ci/.github/workflows/lint.yml@main
    with:
      ruff-config-path: "ruff.toml"  # usa config do próprio repo
```

### Config padrão ruff

Localização: [`lint/ruff/configs/ruff.toml`](lint/ruff/configs/ruff.toml)

---

## 📁 Estrutura do repositório

```
ci/
├── .github/
│   └── workflows/
│       ├── security.yml     # Reusable workflow – segurança
│       ├── test.yml         # Reusable workflow – testes
│       └── lint.yml         # Reusable workflow – lint
├── security/
│   └── bandit/
│       └── configs/
│           └── bandit.yaml  # Config padrão do bandit
├── lint/
│   └── ruff/
│       └── configs/
│           └── ruff.toml    # Config padrão do ruff
└── README.md
```

---

## 🔑 Permissões necessárias

Para que o checkout do repositório central funcione nos jobs que buscam configs padrão, o repositório `ci` deve ser **público** ou os repositórios chamadores devem ter acesso de leitura via token configurado.
