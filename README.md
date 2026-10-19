Plataforma de gestão de equipes com quadros Kanban, tabela, calendário, cronograma (Gantt) e dashboard, automações, comentários, equipe, integração com Slack/webhooks e **fontes de dados para Power BI e Grafana**.

## Como rodar

```bash
pip install -r requirements.txt
python app.py
```

Abra http://localhost:5000. Os dados ficam em `teamflow.db` (SQLite) na mesma pasta. Na primeira execução são criados dois quadros de exemplo; para começar vazio use `TEAMFLOW_SEED=0`.

Variáveis opcionais: `PORT` (padrão 5000), `HOST` (padrão 127.0.0.1; use `0.0.0.0` para acessar de outras máquinas, como um Grafana em outro servidor).

## Funcionalidades

- **Quadros** personalizáveis (nome, cor, colunas/status).
- **Visões**: Kanban (arrastar e soltar), Tabela (com exportar CSV), Calendário, Cronograma e Dashboard por quadro.
- **Tarefas**: responsável, prioridade, datas, progresso, horas estimadas, etiquetas, link e comentários.
- **Automações** por quadro. Gatilhos: tarefa criada, status alterado, prioridade alterada, responsável alterado e tarefa atrasada. Ações: mover de coluna, definir prioridade, atribuir pessoa, definir progresso, comentar e avisar no Slack.
- **Equipe** com carga de trabalho, **atividade** e **visão geral** entre quadros.
- **Integrações**: Slack (webhook), webhook de saída, criação de tarefas por API externa (ex.: Jira, formulários, scripts).

## Power BI

Em **Integrações** (no menu) estão as URLs prontas e o código M. No Power BI Desktop: **Obter dados → Web**, cole a URL, acesso **Anônimo**.

| Dados | URL |
|---|---|
| Tarefas | `/api/bi/tasks.csv?token=SEU_TOKEN` (ou `.json`) |
| Pessoas | `/api/bi/members.csv?token=SEU_TOKEN` |
| Quadros | `/api/bi/boards.csv?token=SEU_TOKEN` |

Acrescente `&board_id=1` para filtrar um quadro. Para atualização agendada no Power BI Service (nuvem), o TeamFlow precisa estar acessível pela internet ou por um gateway de dados local.

## Grafana

- **Plugin Infinity (recomendado)**: fonte de dados Infinity, tipo JSON, método GET, URL `/api/bi/tasks.json?token=SEU_TOKEN`.
- **Plugin SimpleJSON**: URL `/api/grafana/SEU_TOKEN`. Métricas: `resumo`, `tarefas_por_status`, `tarefas_por_prioridade`, `tarefas_por_responsavel`, `tarefas_atrasadas`, `tarefas_criadas_por_dia`, `tarefas_concluidas_por_dia`.

## Exibir painéis dentro do TeamFlow

Em **Painéis Power BI e Grafana** você cola o link de incorporação de um relatório e ele aparece dentro do TeamFlow. No Grafana, ative `allow_embedding = true` no `grafana.ini`.

## Segurança

O token de API (em **Integrações**) protege as URLs de dados e a entrada de tarefas. Quem tem o token lê os dados; não o compartilhe e gere outro se vazar. O TeamFlow **não tem login de usuários**: use-o em rede interna ou atrás de um proxy/VPN com autenticação antes de expor na internet. O servidor embutido do Flask é de desenvolvimento; para produção use `gunicorn app:app` (ou `waitress` no Windows).
