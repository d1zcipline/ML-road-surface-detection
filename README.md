# Гибридная система определения дорожного покрытия и дорожных условий

## О проекте

Это проект разработки мультимодальной системы для классификации состояния дорожного покрытия (сухой асфальт, мокрый, лед, снег, гравий) в реальном времени.
Система использует гибридный подход:
1.  **Video (Computer Vision):** Анализ визуального потока с камеры.
2.  **Audio (Acoustic Analysis):** Анализ звука взаимодействия шин с поверхностью.

## Структура проекта

Используется модульная структура "Монорепозиторий", чтобы упорядочить работу разных ML-инженеров.

```
.
├── configs/                 # Конфигурации обучения (параметры моделей, пути)
│   ├── audio_config.yaml
│   └── video_config.yaml
├── data/                    # Датасеты (в .gitignore)
├── docs/                    # Документация и how to
├── notebooks/               # "Песочница" для кода и черновиков разработичков
│   └── username/            # Папка для экспериментов разработчика 1 и т.д.
│       ├── audio
│       └── video
├── src/                     # Чистовой код (Production-ready)
│   ├── common/              # Общие утилиты (seed, метрики, логгер)
│   ├── audio/               # Домен АУДИО (Датасеты, Модели, Обучение)
│   └── video/               # Домен ВИДЕО (Датасеты, Модели, Обучение)
├── README.md                # Этот файл
└── requirements.txt         # Зависимости проекта
```

## Начало работы

### 1. Требования
*   Python 3.10.x
* 	CUDA 11.x / 12.x (для NVIDIA GPU)
*   Git
*   Системные библиотеки (только для Linux/Mac):
    *   Для работы с аудио может потребоваться `libsndfile`.
    *   *Ubuntu:* `sudo apt-get install libsndfile1`
    *   *MacOS:* `brew install libsndfile`

### 2. Установка
Склонируйте репозиторий и установите зависимости:

```
git clone https://github.com/d1zcipline/ML-road-surface-detection.git
cd ML-road-surface-detection

# Рекомендуется создать виртуальное окружение
python -m venv .ml-rsd-venv
# Активация (Windows):
.ml-rsd-venv\Scripts\activate
# Активация (Mac/Linux):
source .ml-rsd-venv/bin/activate

# Обновляем pip (критично для установки PyTorch)
python.exe -m pip install --upgrade pip
```

#### Шаг 2: Установка PyTorch

**Вариант А: NVIDIA GPU (Windows / Linux)**  
Выполните одну из команд в зависимости от свежести видеокарты:

```
# Если видеокарта новая (RTX 30xx, 40xx) - CUDA 12.x
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121

# ИЛИ

# Если видеокарта старая (GTX 10xx, RTX 20xx) - CUDA 11.8
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
```

**Вариант Б: MacBook (M-series) или нет видеокарты NVIDIA**  
*Просто установите стандартную версию:*
```
pip install torch torchvision torchaudio
```

#### Шаг 3: Установка остальных зависимостей
После того как PyTorch установлен, накатываем остальной стек:

```
pip install -r requirements.txt
```

#### Шаг 4: Проверка
Убедитесь, что всё встало корректно:
```
# Должно вывести True (если есть GPU) или False (если CPU/Mac) без ошибок импорта
python -c "import torch; print(f'CUDA available: {torch.cuda.is_available()}')"
```

## Рабочий процесс (Workflow)

### Разработка новых фичей
1.  **Эксперименты:** Используйте папку `notebooks/` для проверки гипотез, Jupyter-ноутбуков и "грязного" кода. Здесь можно все.
2.  **Чистовик:** Когда решение найдено и работает, переносите код в папку `src/` в виде классов и функций.
3.  **Конфиги:** Не зашивайте гиперпараметры (Learning Rate, Batch Size) в код. Выносите их в `configs/`.

### Запуск обучения

**Обучение Аудио модели:**
```
python -m src.audio.train --config configs/audio_config.yaml
```

**Обучение Видео модели:**
```
python -m src.video.train --config configs/video_config.yaml
```

## Правила командной работы
*   **Ветки:** Создавайте отдельные ветки под задачи (например, `feature/audio-spectrograms`, `fix/video-dataloader`). В `main` пушим только рабочий код.
*   **Code Review:** Перед слиянием в `main` желательно, чтобы код посмотрел другой член команды.

## Лицензия
Проект распространяется под лицензией **MIT License**. Подробнее см. файл [LICENSE](LICENSE).
