# 🎨 Hello GUI (Go + Fyne)

[![Go Version](https://img.shields.io/badge/Go-1.23-00ADD8?logo=go&logoColor=white)](https://go.dev/)
[![Fyne](https://img.shields.io/badge/GUI-Fyne%20v2-4FC3F7)](https://fyne.io/)
[![Build](https://img.shields.io/badge/CI-GitHub%20Actions-2088FF?logo=githubactions&logoColor=white)](../../actions)
[![Release](https://img.shields.io/badge/Releases-GitHub-181717?logo=github)](../../releases)

Учебный проект по настройке **CI/CD для графических приложений на Go** с автоматической публикацией бинарников в GitHub Releases.

## 📌 О проекте

В отличие от консольных утилит на Go, кросс-компиляция GUI-приложений требует использования CGO и нативных компиляторов под каждую ОС (Linux, macOS, Windows).

Этот проект демонстрирует, как настроить матричную сборку в GitHub Actions для обхода ограничений CGO и автоматизировать релизный цикл.

### 🚀 Чему я научился в этом проекте

* Разработке графического интерфейса с помощью библиотеки **Fyne**.
* Работе с **CGO** и системными зависимостями (GLFW, X11, Wayland, OpenGL).
* Настройке матричных сборок (Matrix Builds) в **GitHub Actions** (3 ОС параллельно).
* Использованию **MSYS2** для компиляции под Windows.
* Изоляции GUI-зависимостей Linux для локальных тестов через `Dockerfile.test`.
* Автоматическому проставлению версии программы из Git-тега (`-ldflags`).
* Публикации артефактов в **GitHub Releases**.

## 🛠 Технологии

| Категория | Инструмент |
|-----------|-----------|
| Язык | Go 1.23 |
| GUI-фреймворк | Fyne v2 |
| CI/CD | GitHub Actions |
| Контейнеризация (для тестов) | Docker |

## ⚙️ Как работает CI/CD

Сборка запускается автоматически при создании нового тега вида `v*` (например, `v1.0.0`).

1. **Matrix build**: GitHub Actions параллельно запускает сборку на трёх ОС: `ubuntu-latest`, `macos-latest`, `windows-latest`.
2. **Системные зависимости**: на Linux устанавливаются библиотеки для X11/OpenGL, на Windows используется **MSYS2** с нативным компилятором, на macOS всё берётся из Xcode Command Line Tools.
3. **Версионирование**: версия приложения берётся из Git-тега и вшивается в бинарник при сборке:
```bash
   go build -ldflags "-X main.version=${GITHUB_REF_NAME}" -o hello-gui .
```
4. **Публикация**: готовые артефакты для каждой ОС автоматически загружаются в раздел **Releases**.

## 📦 Установка и запуск

Готовые исполняемые файлы для **Linux**, **macOS** и **Windows** собираются автоматически при создании нового тега.

1. Перейдите в раздел [Releases](../../releases) этого репозитория.
2. Скачайте архив для вашей операционной системы.
3. Запустите исполняемый файл.

> 💡 **macOS:** при первом запуске может потребоваться разрешение в *Системных настройках → Конфиденциальность и безопасность*.

## 💻 Сборка из исходников

### Через Docker (Linux, без установки X11/Wayland на хост)

Для локальной проверки сборки под Linux:

```bash
docker build -f Dockerfile.test -t hello-gui-test .
docker run --rm -v "$(pwd)":/app -w /app hello-gui-test go test ./...
```

### Локально (без Docker)

Потребуются Go 1.23+ и C-компилятор (CGO). Для Linux также нужны dev-пакеты OpenGL/X11.

```bash
git clone https://github.com/<ваш-username>/<название-репозитория>.git
cd <название-репозитория>
go run .
```

## 🏷 Как выпустить релиз

```bash
git tag v1.0.0
git push origin v1.0.0
```

После пуша тега GitHub Actions соберёт бинарники под все платформы и опубликует их в Releases.

## 📁 Структура проекта

```
.
├── .github/
│   └── workflows/
│       └── release.yml      # Матричная сборка и публикация релиза
├── Dockerfile.test          # Окружение для тестов под Linux
├── main.go                  # Точка входа, GUI на Fyne
├── go.mod
└── README.md
```

## 📄 Лицензия

Проект учебный, распространяется свободно. При желании добавьте файл `LICENSE` (например, MIT).