# 2026-03-14 - Criação do Repositório Central de CI

**Contexto**: O projeto precisava de um repositório central para evitar duplicação de pipelines CI entre múltiplos repositórios Python. A abordagem adotada foi criar reusable workflows do GitHub Actions, que permitem que repositórios externos chamem os workflows via `uses:` sem copiar código.

**Decisão**: Utilizar **reusable workflows** (`workflow_call`) em vez de composite actions. Reusable workflows são mais adequados para orquestrar jobs completos (com runners dedicados, steps de checkout, instalação de dependências), enquanto composite actions seriam mais indicados para steps individuais. Como o objetivo é centralizar jobs inteiras, reusable workflows são a escolha natural e nativa do GitHub Actions.

**Impacto**: Qualquer repositório da organização pode importar segurança, testes e lint com 3–5 linhas de YAML. Atualizações nas configs centrais (bandit.yaml, ruff.toml) propagam automaticamente para todos os consumidores que não sobrescreveram o config via input.

---

# 2026-03-15 - Workflow de Build de Imagens Docker

**Contexto**: Necessidade de padronizar a construção e publicação de imagens Docker no GHCR entre os repositórios da organização, garantindo que a tag da imagem seja sempre derivada da versão declarada no `pyproject.toml` e que não haja sobrescrita acidental de tags já publicadas.

**Decisão**:
- **`workflow_call`** como único trigger, seguindo o padrão do repositório. O acionamento em push para `main` é responsabilidade do repositório chamador.
- **Versão extraída via `grep`/`sed`** diretamente do `pyproject.toml`, sem dependências externas (Python `tomllib` não está disponível em todas as versões do runner).
- **Nomes de imagem em lowercase**: GHCR exige nomes de imagem em letras minúsculas; o workflow aplica `${VAR,,}` para normalizar `owner` e `repo`.
- **Verificação de tag duplicada via `docker manifest inspect`**: executado após login no GHCR, utiliza o Docker CLI já disponível no runner sem precisar de ferramentas adicionais (`skopeo`, `crane`). Falha com erro descritivo se a tag já existir.
- **`docker/login-action@v3`** e **`docker/build-push-action@v6`**: actions oficiais do Docker mantidas pela equipe Docker, seguindo a diretriz de usar a ação oficial do GitHub para login e push no GHCR.
- **Permissões declaradas no job** (`contents: read`, `packages: write`): seguindo o princípio de menor privilégio, as permissões são limitadas ao mínimo necessário para o push no GHCR.

**Impacto**: Qualquer repositório com `pyproject.toml` versionado e um `Dockerfile` pode publicar imagens no GHCR com 5 linhas de YAML, com garantia de rastreabilidade por versão e proteção contra sobrescrita de tags já existentes.

