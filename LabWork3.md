# Диаграмма контейнеров

![Диаг контейнеров drawio](https://github.com/user-attachments/assets/0feb7747-7fda-4130-a151-f5a274895376)

# Диаграмма компонентов контейнера Сервер анализа и распознавания

![Диаг компонентов контейнера drawio](https://github.com/user-attachments/assets/9107da3a-c04e-40a8-8c8d-92a278654555)

# Диаграмма последовательностей

## Описание диаграммы

1. Пользователь загружает визуальную модель через клиентское приложение.
2. API-шлюз передает запрос в модуль обработки данных.
3. Данные проходят через предварительную обработку.
4. Если данные успешно прошли проверку, то движок распознавания выполняет анализ, предварительно запросив шаблоны для проверки.
5. Результаты передаются в менеджер результатов для сохранения в БД.
6. Менеджер результатов возвращает ответ пользователю.

![Диаг последовательностей drawio](https://github.com/user-attachments/assets/4f3d470d-d062-433d-90c2-e96efcc3b5e3)


# Диаграмма классов
## Описание диаграммы
Диаграмма содержит 6 классов:

- Метрики анализа
- Модель
- Пользователь
- Тип элемента
- Элемент модели
- Атрибут элемента

Связи:

- Элемент модели и Атрибут элемента (Композиция)
- Модель и Элемент модели (Композиция)
- Остальные связи – Ассоциация

![Diagrams-Classes drawio](https://github.com/user-attachments/assets/f4b27042-83fe-4c04-8cd8-a79cc1d95210)

# Реализация клиентского и серверного кода с учётом принципов KISS, YAGNI, DRY и SOLID

## Основные компоненты системы

1. **Шлюз API**  
   Обрабатывает входящие запросы от клиентских приложений и направляет их к модулям внутри системы.

2. **Менеджер данных**  
   Управляет процессами получения, хранения и предоставления данных. Осуществляет взаимодействие с базой данных.

3. **Модуль распознавания**  
   Выполняет алгоритмы распознавания визуальных моделей.

4. **Модуль предварительной обработки**  
   Проверяет и подготавливает входные данные перед их анализом (нормализация данных, удаление ошибок).

5. **Модуль результатов**  
   Сохраняет и предоставляет результаты работы системы клиентским приложениям.

---

## Клиентский код

Код для взаимодействия с API:

```python
import requests

API_URL = "http://localhost:8000/api/process_model"

def upload_model(file_path):
    try:
        with open(file_path, "rb") as file:
            response = requests.post(API_URL, files={"file": file})
        if response.status_code == 200:
            print("Результат анализа:", response.json())
        else:
            print("Ошибка:", response.text)
    except Exception as e:
        print("Ошибка при отправке запроса:", str(e))

if __name__ == "__main__":
    model_path = "model_example.json"
    upload_model(model_path)
```
### Принципы
- KISS: Простая и понятная функция.
- YAGNI: Реализована только загрузка модели и получение результата.
- DRY: Повторяющаяся логика обработки ошибок находится в одном месте.

## Серверный код

```python
from abc import ABC
from fastapi import FastAPI, UploadFile

# Интерфейсы для внедрения зависимостей
class IPreprocessor(ABC):
    @abstractmethod
    def process(self, data: bytes) -> bytes:
        pass

class IRecognitionEngine(ABC):
    @abstractmethod
    def analyze(self, data: bytes) -> dict:
        pass

class IResultManager(ABC):
    @abstractmethod
    def save_result(self, result: dict) -> None:
        pass

# Реализация компонентов
class Preprocessor(IPreprocessor):
    def process(self, data: bytes) -> bytes:
        # Реализация предобработки
        return data

class RecognitionEngine(IRecognitionEngine):
    def analyze(self, data: bytes) -> dict:
        # Реализация распознавания
        return {"result": "success"}

class ResultManager(IResultManager):
    def save_result(self, result: dict) -> None:
        # Реализация сохранения результата
        print("Result saved:", result)

# Основной процесс обработки
class ModelProcessor:
    def __init__(self, preprocessor: IPreprocessor, recognition_engine: IRecognitionEngine, result_manager: IResultManager):
        self.preprocessor = preprocessor
        self.recognition_engine = recognition_engine
        self.result_manager = result_manager

    def process(self, data: bytes) -> dict:
        preprocessed_data = self.preprocessor.process(data)
        recognition_result = self.recognition_engine.analyze(preprocessed_data)
        self.result_manager.save_result(recognition_result)
        return recognition_result

# FastAPI приложение
app = FastAPI()

# Внедрение зависимостей
preprocessor = Preprocessor()
recognition_engine = RecognitionEngine()
result_manager = ResultManager()
model_processor = ModelProcessor(preprocessor, recognition_engine, result_manager)

@app.post("/api/process_model")
async def process_model(file: UploadFile):
    try:
        data = await file.read()
        recognition_result = model_processor.process(data)
        return {"status": "success", "result": recognition_result}
    except Exception as e:
        return {"status": "error", "message": str(e)}
```
### Принципы:
- **S**
    
    Каждый класс отвечает за одну конкретную задачу:
    
    - `Preprocessor`: только предобработка данных.
    - `RecognitionEngine`: только анализ данных.
    - `ResultManager`: только управление результатами.
    - `ModelProcessor`: координирует работу компонентов, разделяя их обязанности.
- **O**
    
    Код легко расширить, не изменяя существующие классы:
    
    - Можно добавить новый `IPreprocessor` или `IRecognitionEngine` без изменения основного процесса.
    - Для этого достаточно реализовать новый класс, соответствующий интерфейсу.
- **L**
    
    Все классы, реализующие интерфейсы (`IPreprocessor`, `IRecognitionEngine`, `IResultManager`), могут заменять друг друга без изменений в `ModelProcessor`.
    
    - Например, `Preprocessor` можно заменить на другой, который работает с изображениями, а не текстом.
- **I**
    
    Интерфейсы разделены на небольшие и конкретные (`IPreprocessor`, `IRecognitionEngine`, `IResultManager`), чтобы не навязывать неиспользуемые методы.
    
    - Классы зависят только от нужных интерфейсов.
- **D**
    
    Компоненты зависят от абстракций (интерфейсов), а не от конкретных реализаций.
    
    - В `ModelProcessor` используются интерфейсы `IPreprocessor`, `IRecognitionEngine`, `IResultManager`, что упрощает замену зависимостей.
- KISS: Простой метод API для обработки входных данных.
- YAGNI: Реализована только базовая логика.
