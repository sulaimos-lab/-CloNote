# CloNote — Менеджер задач

Мобильное приложение для управления задачами с поддержкой ИИ-ассистентов, дашбордом в стиле Obsidian и экспортом в Markdown/HTML.

**Автор:** Sulaimon Qurbonov  
**Лицензия:** MIT  
**Тариф:** Бесплатный — без рекламы и ограничений  
**Версия:** 1.0.0

---

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — API-сервер (порт 8080)
- `pnpm --filter @workspace/mobile run dev` — мобильное приложение Expo
- `pnpm run typecheck` — полная проверка типов
- `pnpm run build` — сборка всех пакетов
- `pnpm --filter @workspace/db run push` — применить схему БД (только dev)
- Required env: `DATABASE_URL` — строка подключения PostgreSQL

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Mobile: Expo / React Native (expo-router)
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- MCP: @modelcontextprotocol/sdk (StreamableHTTP, порт 8000)
- Validation: Zod (`zod/v4`)
- Build: esbuild (CJS bundle)

## Where things live

```
artifacts/
  mobile/          — Expo приложение (основной продукт)
    app/
      (tabs)/
        index.tsx    — список задач
        habits.tsx   — трекер привычек
        dashboard.tsx — дашборд со статистикой и экспортом
        settings.tsx — настройки
      graph.tsx      — граф связей задач
      mcp-connect.tsx — подключение ИИ через MCP
      categories.tsx — управление проектами
    context/
      TaskContext.tsx — глобальное хранилище задач
    utils/
      exportService.ts — экспорт в MD/HTML
      syncService.ts   — синхронизация с API

  api-server/      — REST API (Express)
    src/routes/tasks.ts — CRUD + bulk sync

  mcp-server/      — MCP-сервер для ИИ-ассистентов
    src/index.js    — 10 инструментов для управления задачами

lib/
  db/              — Drizzle ORM схема (tasks, categories)
```

## Architecture decisions

- **DB использует text, не pgEnum** — позволяет мобильному клиенту создавать произвольные категории без миграций
- **Fire-and-forget sync** — мобильный клиент не блокирует UI на синхронизации, pushTask вызывается в фоне
- **MCP через HTTP** — StreamableHTTP транспорт позволяет подключать Claude Desktop и Cursor по URL без локального сервера
- **Граф без D3** — граф связей реализован на react-native-svg с ручным layout, чтобы избежать проблем с Metro bundler и тяжёлых зависимостей
- **Экспорт MD/HTML** — задачи с подзадачами, категориями и приоритетами экспортируются для импорта в Obsidian, ChatGPT, Claude

## Product

- 📋 Список задач с категориями, приоритетами, подзадачами и сроками
- 🌱 Трекер привычек с streak и историей
- 📊 Дашборд: прогресс, heatmap активности, статистика по категориям
- 🕸️ Граф связей задач в трёх режимах
- 📤 Экспорт в Markdown и HTML
- 🤖 MCP-интеграция: Claude, Cursor, Cline управляют задачами через чат
- 🔗 QR-код для быстрого подключения ИИ

## User preferences

- Язык интерфейса: русский
- Дизайн: минимализм в стиле Notion (белый/чёрный)
- Тариф: бесплатный — без paywall, рекламы и ограничений
- Автор: Sulaimon Qurbonov — указывать во всех файлах

## Gotchas

- При установке пакетов перезапускать `artifacts/mobile: expo` воркфлоу — Metro watcher падает на временных файлах pnpm
- D3-force/d3-hierarchy несовместимы с Metro bundler (watchers падают) — граф реализован вручную на SVG
- `lib/api-zod/src/index.ts` экспортирует только `./generated/api`, не `./generated/types` — иначе дублируется SyncTasksBody
- `colors.surface` (не `colors.secondary`) — для подложек в тёмной теме

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
- MCP URL: `https://9670f83a-1e11-4a65-8f8d-252fd6eb6a94-00-2d6gnvzcs6qt8.sisko.replit.dev/mcp-server/mcp`
