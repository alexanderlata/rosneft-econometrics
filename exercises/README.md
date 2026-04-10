# Инструкция по работе с листками задач (.ipynb + Quarto)

Документ предназначен для преподавателей кафедры, работающих с листками задач в формате Jupyter Notebook, собираемых через Quarto.

---

## 1. Установка зависимостей

1. **Python 3** и библиотеки.
2. **[Quarto CLI](https://quarto.org/docs/get-started/)** — для компиляции файлов.
3. **Редактор**: VS Code с расширениями *Jupyter* и *Quarto*.
4. **TexLive** (для сборки PDF) или TinyTex: `quarto install tinytex`

---

## 2. YAML-заголовок файла

Располагается в самом начале `.ipynb`-файла в ячейке типа **Raw**. Управляет метаданными и параметрами сборки для всех форматов одновременно.

```yaml
---
title: "Задачи по Эконометрике-2: LPM-модель"
author: "Н.В. Артамонов, А. Н. Лата (МГИМО МИД России)"
lang: ru
otherlangs: [en]
mainfont: "Times New Roman"
sansfont: "Arial"
monofont: "Courier New"
---
```

`lang: ru` и `otherlangs: [en]` управляют переносами и локализацией через `polyglossia` при сборке в PDF. `mainfont` / `sansfont` / `monofont` применяются только в PDF (XeLaTeX).

### Форматы вывода

Один файл собирается в несколько форматов независимо:

```yaml
format:
  html:
    toc: true          # оглавление
    toc-float: true    # плавающее (прилипает при прокрутке)
    collapsed: false   # оглавление развёрнуто по умолчанию
    number-sections: true
    df-print: paged    # таблицы с пагинацией
    include-in-header:
      text: |
        <style>
        table { width: auto !important; margin-left: auto; margin-right: auto; }
        </style>
  gfm:
    toc: true
  pdf:
    pdf-engine: xelatex   # обязателен для кириллицы
    documentclass: article
    keep-tex: true         # сохранить .tex после сборки (удобно для отладки)
    toc: true
    number-sections: true
```

Блок `include-in-header` позволяет вставить произвольный CSS только в HTML, не затрагивая PDF.

### Поля страницы (только PDF)

```yaml
geometry:
  - top=20mm
  - left=20mm
  - right=20mm
  - bottom=20mm
```

### Глобальные параметры выполнения кода

Применяются ко всем ячейкам, если не переопределены локально:

```yaml
execute:
  echo: false      # не показывать исходный код
  warning: false   # не показывать предупреждения
  message: false   # не показывать сообщения от библиотек
```

Переопределить для отдельной ячейки:

```python
#| echo: true   # показать код именно в этой ячейке
```

---

## 3. Директивы в ячейках с кодом Python

Пишутся первыми строками ячейки и управляют тем, как код и его результаты попадают в итоговый документ.

### Управление выводом

```python
#| echo: false      # скрыть исходный код, показать только результат
#| warning: false   # скрыть предупреждения от библиотек
#| message: false   # скрыть информационные сообщения
#| output: asis     # вставить результат как сырой текст (LaTeX или Markdown)
```

### Настройки графиков

```python
#| label: fig-log-faminc
#| fig-cap: "Гистограмма логарифмированного дохода семьи с наложением нормальной кривой плотности."
#| fig-align: "center"
#| fig-width: 6
#| fig-height: 4
```

`label` используется для перекрёстных ссылок на рисунок в тексте: `@fig-log-faminc`. Префикс `fig-` обязателен.

---

## 4. Команды для сборки

Запускаются в терминале из директории с файлом `.ipynb`.

```bash
# Предпросмотр: открывает документ в браузере и обновляет при каждом сохранении
quarto preview list21-LPM.ipynb

# Базовая сборка в формат по умолчанию (обычно HTML)
quarto render list21-LPM.ipynb

# Сборка в PDF
quarto render list21-LPM.ipynb --to pdf --pdf-engine=xelatex

# Сборка в GitHub Flavored Markdown с принудительным пересчётом всех ячеек
quarto render list21-LPM.ipynb --to gfm --execute
```

Флаг `--execute` игнорирует кэш и пересчитывает все ячейки — используется перед финальной публикацией.