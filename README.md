# paptools - Antigravity Skills & Plugin Module

Модул със скилове и MCP инструменти за Antigravity AI.

## Структура

```text
paptools/
├── plugin.json               # Манифест на модула
├── README.md                 # Документация
├── .gitignore                # Git игнорирани файлове
├── mcp_config.json.example   # Примерен темплейт за MongoDB Atlas MCP сървър
├── mcp_config.json           # Локална конфигурация с реални секрети (в .gitignore)
└── skills/                   # Папка със скилове
```

## Как да инсталирате на нов компютър

При клониране на `paptools` на нов компютър:

1. Клонирайте репозиторията в глобалната папка с плъгини:
   ```bash
   git clone https://github.com/ВАШИЯ-ПОТРЕБИТЕЛ/paptools.git ~/.gemini/config/plugins/paptools
   ```
2. Копирайте темплейта за MCP сървъра:
   ```bash
   cp mcp_config.json.example mcp_config.json
   ```
3. Отворете `mcp_config.json` и попълнете вашия съществуващ **MongoDB Atlas connection string**.
