# Пошаговый дискриминантный анализ (LDA) — main.ipynb

Короткий ноутбук для линейного дискриминантного анализа (LDA) с пошаговым отбором признаков (включение/исключение), расчетом ковариационных матриц и расстояний Махаланобиса. Вход — Excel `data.xlsx`, выход — `output.xlsx` с результатами классификации.

## Что делает ноутбук
- Читает Excel (`INPUT_PATH`, по умолчанию `data.xlsx`) и берет первые 85 строк.
- Стандартизирует числовые признаки X1..X9 (все колонки, кроме первой).
- Строит LDA, вычисляет ковариационную матрицу, средние по классам, расстояния Махаланобиса.
- Делает пошаговый отбор признаков: с включением (forward) и с исключением (backward).
- Сохраняет результаты в `OUTPUT_PATH` (по умолчанию `output.xlsx`):
  - Train sample (класс из обучающей выборки)
  - Result Lda (полная модель)
  - Result forward (модель с включением)
  - Result backward (модель с исключением)

## Требования
- Python 3.10+
- Зависимости: pandas, numpy, scipy, scikit-learn, openpyxl

Рекомендуется установить из `requirements.txt`.

## Быстрый старт локально (Linux/macOS, sh)
```sh
# 1) Создать и активировать виртуальное окружение
python3 -m venv .venv
source .venv/bin/activate

# 2) Обновить pip и установить зависимости
pip install --upgrade pip
pip install -r requirements.txt

# 3) Запустить ноутбук (любой способ)
# Вариант A: открыть main.ipynb в VS Code и выполнить ячейки
# Вариант B (если нужно):
# pip install jupyterlab
# jupyter lab
```

### Windows
```powershell
# 1) Создать и активировать виртуальное окружение (PowerShell)
py -m venv .venv
.venv\Scripts\Activate.ps1

# 2) Обновить pip и установить зависимости
python -m pip install --upgrade pip
pip install -r requirements.txt

# 3) Запуск ноутбука
# Откройте main.ipynb в VS Code и выполните ячейки
# или установите JupyterLab и запустите его:
# pip install jupyterlab
# jupyter lab
```

Примечание: в классической CMD активация окружения — `.venv\Scripts\activate.bat`.

## Настройка переменных (первая ячейка ноутбука)
- `INPUT_PATH`: путь к входному Excel (по умолчанию `data.xlsx`).
- `OUTPUT_PATH`: путь для сохранения результатов (по умолчанию `output.xlsx`).
- `CNT_ENTER`: число признаков в forward-модели.
- `CNT_REMOVE`: число признаков, исключаемых в backward-процедуре.
- `TRAIN_SAMPLE`: словарь вида `{класс: [индексы_строк]}` — обучающая выборка.
  - Индексы — это индексы DataFrame после чтения файла (0-based). Убедитесь, что указанные номера существуют.

## Формат входных данных (Excel)
- Первая колонка — идентификатор/метка (не стандартизируется).
- Далее должны идти числовые признаки `X1`..`X9`.
- Используются первые 85 строк файла.

Минимальный пример структуры (заголовки):
```
<ID-or-Name>  X1  X2  ...  X9
...
```

## Результаты
В `output.xlsx` попадут исходные признаки X1..X9 и дополнительные столбцы:
- `Train sample` — класс для строк, попавших в обучающую выборку.
- `Result Lda` — класс по полной LDA-модели.
- `Result forward` — класс по модели с включением признаков.
- `Result backward` — класс по модели с исключением признаков.

## Запуск в Google Colab
1) Откройте Colab → File → Upload notebook и выберите `main.ipynb`.
2) Загрузите `data.xlsx` в среду:
   - Слева вкладка Files → Upload → выберите `data.xlsx`, он окажется в `/content/data.xlsx`; или
   - Смонтируйте Google Drive:
     ```python
     from google.colab import drive
     drive.mount('/content/drive')
     # затем используйте путь вида '/content/drive/MyDrive/путь/к/data.xlsx'
     ```
3) Установите (при необходимости) зависимости в первой ячейке Colab:
   ```python
   # Выполните только если библиотек не хватает
   !pip -q install openpyxl scikit-learn scipy pandas
   ```
4) В первой ячейке ноутбука обновите пути:
   - `INPUT_PATH = '/content/data.xlsx'` (или путь в Google Drive)
   - `OUTPUT_PATH = '/content/output.xlsx'`
5) Выполните все ячейки.

## Частые вопросы
- `FileNotFoundError`: проверьте `INPUT_PATH` и расположение `data.xlsx`.
- `ModuleNotFoundError`: установите зависимости (см. выше). В Colab выполните ячейку с `!pip`.
- Индексы `TRAIN_SAMPLE` не найдены: проверьте, что они соответствуют индексам DataFrame (0-based) и попадают в первые 85 строк.
