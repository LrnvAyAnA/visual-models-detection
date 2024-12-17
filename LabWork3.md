# Диаграмма контейнеров

![Container drawio](https://github.com/user-attachments/assets/a98a6bf6-4f56-42da-b902-1c1728c26c0c)

# Диаграмма компонентов контейнера Сервер анализа и распознавания

![Untitled Diagram-Page-4 drawio](https://github.com/user-attachments/assets/7c9bba4b-ed15-40e3-81bf-2a86a53ced42)

# Диаграмма последовательностей
## Описание диаграммы
1. Пользователь загружает визуальную модель через клиентское приложение.
2. API-шлюз передает запрос в модуль обработки данных.
3. Данные проходят через предварительную обработку.
4. Если данные успешно прошли проверку, то движок распознавания выполняет анализ, предварительно запросив шаблоны для проверки.
5. Результаты передаются в менеджер результатов для сохранения в БД.
6. Менеджер результатов возвращает ответ пользователю.

![Sequence drawio (2)](https://github.com/user-attachments/assets/c1d824a3-b4fb-4206-a371-ba19fd876841)

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

![Untitled Diagram-Classes drawio](https://github.com/user-attachments/assets/e3987672-49ac-47f6-b4ba-ea028f464150)

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

1. Шлюз API:
```python
from fastapi import FastAPI, UploadFile
from modules.preprocessor import Preprocessor
from modules.recognition import RecognitionEngine
from modules.result_manager import ResultManager

app = FastAPI()

preprocessor = Preprocessor()
recognition_engine = RecognitionEngine()
result_manager = ResultManager()

@app.post("/api/process_model")
async def process_model(file: UploadFile):
    try:
        data = await file.read()
        preprocessed_data = preprocessor.process(data)
        recognition_result = recognition_engine.analyze(preprocessed_data)
        result_manager.save_result(recognition_result)
        return {"status": "success", "result": recognition_result}
    except Exception as e:
        return {"status": "error", "message": str(e)}
```
### Принципы:
- SOLID: Каждый модуль выполняет свою ответственность.
- KISS: Простой метод API для обработки входных данных.
- YAGNI: Реализована только базовая логика.

2. Модуль предварительной обработки: 
```python
class Preprocessor:
    def process(self, raw_data):
        # KISS: Нормализация данных
        normalized_data = raw_data.decode("utf-8").strip()
        if not normalized_data:
            raise ValueError("Данные пусты или некорректны.")
        return normalized_data
```
3. Модуль распознавания
```python
class RecognitionEngine:
    def analyze(self, data):
        # KISS: Простая логика распознавания
        if "model" in data:
            return {"status": "recognized", "details": "Модель успешно распознана"}
        else:
            raise ValueError("Модель не распознана")
```
4. Модуль результатов
```python
class ResultManager:
    def __init__(self):
        self.results = []

    def save_result(self, result):
        # KISS: Сохранение результата
        self.results.append(result)
        print("Результат сохранён:", result)
```
