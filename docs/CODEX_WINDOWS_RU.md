# Установка Playwright CLI для Codex на Windows

Эта инструкция рассчитана на нашу схему: Windows + Codex + проект на GitHub + `open-steps` + Astra/Luna Orchestrator.

## 1. Проверить Node.js

В PowerShell:

```powershell
node --version
npm --version
```

Нужен Node.js 18 или новее.

Если `node` или `npm` не найдены, сначала устанавливаем актуальный LTS Node.js, затем заново открываем PowerShell.

## 2. Установить Playwright CLI

```powershell
npm install -g @playwright/cli@latest
```

Проверка:

```powershell
playwright-cli --help
```

Если справка открылась — CLI установлен.

## 3. Подключить Skill к нужному проекту

Перейди в папку проекта, например:

```powershell
cd C:\Projects\my-web-app
```

Затем:

```powershell
playwright-cli install --skills=agents
```

Для Codex это создаёт проектный skill в:

```text
.agents\skills\playwright-cli\SKILL.md
```

Почему проектно, а не глобально: браузерная автоматизация нужна не каждому репозиторию, а локальный skill уменьшает лишний контекст и делает поведение проекта более предсказуемым.

## 4. Запустить Codex из этого проекта

Codex должен видеть:

- код проекта;
- `.agents/skills/playwright-cli/SKILL.md`;
- наши общие skills `open-steps`, если они установлены глобально;
- роли Astra/Luna Orchestrator, если они подключены глобально или в проекте.

## 5. Быстрая ручная проверка

```powershell
playwright-cli open https://example.com --headed
playwright-cli snapshot
playwright-cli screenshot --filename=playwright-check.png
playwright-cli close
```

Если открылось окно браузера, snapshot вернул структуру страницы, а скриншот сохранился — базовая установка работает.

## 6. Проверка локального приложения

Сначала запускается само приложение обычным способом проекта, например:

```powershell
npm run dev
```

После появления локального адреса, например `http://localhost:3000`:

```powershell
playwright-cli open http://localhost:3000 --headed
playwright-cli snapshot
```

Дальше можно попросить Codex пройти пользовательский сценарий.

Пример задачи:

```text
Проверь через playwright-cli локальное приложение.
Открой главную страницу, создай тестовую запись, проверь сохранение,
затем измени viewport на мобильный и проверь, что основные элементы доступны.
Никаких production-действий не выполнять.
```

## 7. Просмотр сессий

Чтобы видеть активные браузерные сессии:

```powershell
playwright-cli show
```

Это удобно, когда агент проверяет интерфейс сам: можно наблюдать процесс и при необходимости вмешаться вручную.

## Обновление

```powershell
npm install -g @playwright/cli@latest
```

После обновления из каждого проекта, где используется skill:

```powershell
playwright-cli install --skills=agents
```

## Если что-то не работает

Сначала проверить:

```powershell
node --version
npm --version
playwright-cli --help
```

Затем убедиться, что команда `playwright-cli install --skills=agents` запускалась именно из корня нужного проекта.

Не удаляй и не перезаписывай `.agents` целиком: там могут находиться другие наши skills. Установщик Playwright должен добавлять или обновлять только свой skill.
