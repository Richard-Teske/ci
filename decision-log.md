# 2026-03-14 - Criação do Repositório Central de CI

**Contexto**: O projeto precisava de um repositório central para evitar duplicação de pipelines CI entre múltiplos repositórios Python. A abordagem adotada foi criar reusable workflows do GitHub Actions, que permitem que repositórios externos chamem os workflows via `uses:` sem copiar código.

**Decisão**: Utilizar **reusable workflows** (`workflow_call`) em vez de composite actions. Reusable workflows são mais adequados para orquestrar jobs completos (com runners dedicados, steps de checkout, instalação de dependências), enquanto composite actions seriam mais indicados para steps individuais. Como o objetivo é centralizar jobs inteiras, reusable workflows são a escolha natural e nativa do GitHub Actions.

**Impacto**: Qualquer repositório da organização pode importar segurança, testes e lint com 3–5 linhas de YAML. Atualizações nas configs centrais (bandit.yaml, ruff.toml) propagam automaticamente para todos os consumidores que não sobrescreveram o config via input.
