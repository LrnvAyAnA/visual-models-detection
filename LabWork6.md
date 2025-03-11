# Порождающие шаблоны

## 1. Одиночка

### Описание:
Шаблон применяется для создания класса DataManager, который управляет данными об изображениях и должен существовать в единственном экземпляре во время работы приложения.
### UML-диаграмма:
![image](https://github.com/user-attachments/assets/9ca68697-d2d9-4691-baf6-dc56a8f7116b)
### Программный код:
```python
class DataManager:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super(DataManager, cls).__new__(cls)
            cls._instance.images_data = {}
        return cls._instance

    def add_image(self, name, path):
        image_id = len(self.images_data) + 1
        self.images_data[image_id] = {"name": name, "path": path}
        return image_id

    def get_image(self, image_id):
        return self.images_data.get(image_id)

    def update_image(self, image_id, name=None, path=None):
        image = self.images_data.get(image_id)
        if image:
            if name:
                image["name"] = name
            if path:
                image["path"] = path
            return image
        return None

    def delete_image(self, image_id):
        return self.images_data.pop(image_id, None)
```
### Результаты:

- Класс **DataManager** создается только один раз.
- Весь доступ к данным идет через один объект.
- Если вызвать **DataManager()** несколько раз, всегда будет возвращаться один и тот же объект.

## 2. Фабричный метод

### Описание:

Используется фабричный метод, чтобы делегировать создание объектов определенному классу-наследнику, который будет определять, какой именно тип объекта (изображения) нужно создать

### UML-диаграмма:

![image](https://github.com/user-attachments/assets/07ff94e1-c0ae-447e-9952-e41a77133e6d)

### Программный код:

```python
from abc import ABC, abstractmethod

class Image(ABC):
    @abstractmethod
    def display(self):
        pass

class JPGImage(Image):
    def display(self):
        return "Displaying JPG image"

class PNGImage(Image):
    def display(self):
        return "Displaying PNG image"

# Создатель (Creator)
class ImageFactory(ABC):
    @abstractmethod
    def create_image(self, image_type: str) -> Image:
        pass

# Конкретный Создатель
class ConcreteImageFactory(ImageFactory):
    def create_image(self, image_type: str) -> Image:
        if image_type == "JPG":
            return JPGImage()
        elif image_type == "PNG":
            return PNGImage()
        else:
            raise ValueError(f"Unsupported image type: {image_type}")

# Клиентский код
def display_image(factory: ImageFactory, image_type: str):
    image = factory.create_image(image_type)
    print(image.display())

```

### Результаты:

- Шаблон Factory Method позволяет нам создавать различные типы объектов без явного указания точного класса в клиентском коде.
- Это упрощает добавление новых типов изображений в будущем. Для этого нужно только добавить новый класс продукта и соответствующую логику в фабричный метод, не меняя клиентский код.
- Паттерн позволяет скрыть детали реализации и сделать код более гибким.

## 3. Строитель

### Описание:
Шаблон позволяет пошагово создавать и настраивать объекты изображений с различными параметрами (формат, размер, фильтры), упрощая их конфигурацию и расширяемость.
### UML-диаграмма:

![image](https://github.com/user-attachments/assets/8a1820cc-cf01-4fa2-88f3-f7025ff619af)

### Программный код:
```python
from abc import ABC, abstractmethod

# Класс, представляющий изображение
class Image:
    def __init__(self, format: str, width: int, height: int, filters: list):
        self.format = format
        self.width = width
        self.height = height
        self.filters = filters

    def __str__(self):
        return f"Image({self.format}, {self.width}x{self.height}, Filters: {', '.join(self.filters)})"

# Абстрактный строитель
class ImageBuilder(ABC):
    @abstractmethod
    def set_format(self, format: str):
        pass

    @abstractmethod
    def set_size(self, width: int, height: int):
        pass

    @abstractmethod
    def add_filter(self, filter_name: str):
        pass

    @abstractmethod
    def get_result(self) -> Image:
        pass

# Конкретный строитель
class ConcreteImageBuilder(ImageBuilder):
    def __init__(self):
        self.reset()

    def reset(self):
        self.format = "JPG"
        self.width = 800
        self.height = 600
        self.filters = []

    def set_format(self, format: str):
        self.format = format
        return self

    def set_size(self, width: int, height: int):
        self.width = width
        self.height = height
        return self

    def add_filter(self, filter_name: str):
        self.filters.append(filter_name)
        return self

    def get_result(self) -> Image:
        return Image(self.format, self.width, self.height, self.filters)

# Директор (управляет процессом создания)
class ImageDirector:
    def __init__(self, builder: ImageBuilder):
        self.builder = builder

    def construct_standard_image(self):
        return self.builder.set_format("PNG").set_size(1024, 768).add_filter("Brightness").get_result()

# Клиентский код
builder = ConcreteImageBuilder()
director = ImageDirector(builder)

# Создание стандартного изображения через директора
image1 = director.construct_standard_image()
print(image1)  # Image(PNG, 1024x768, Filters: Brightness)

# Создание кастомного изображения вручную
image2 = builder.set_format("JPG").set_size(1920, 1080).add_filter("Contrast").add_filter("Sharpen").get_result()
print(image2)  # Image(JPG, 1920x1080, Filters: Contrast, Sharpen)

```
### Результаты:

- Упрощение создания изображений с разными параметрами.
- Обеспечение расширяемости (легко добавить новые параметры или типы изображений).

# Структурные шаблоны

## 1. Адаптер

### Описание:

Вместо того чтобы напрямую работать с DataManager, используется DataManagerAdapter, который реализует интерфейс ImageStorageAdapter.

### UML-диаграмма:

![image](https://github.com/user-attachments/assets/aaa98881-4372-43e7-9bc3-299e7fb71042)

### Программный код:

```python
from abc import ABC, abstractmethod
from singleton import DataManager

# Интерфейс адаптера
class ImageStorageAdapter(ABC):
    @abstractmethod
    def add_image(self, image_id, name, path):
        pass

    @abstractmethod
    def get_image(self, image_id):
        pass

    @abstractmethod
    def update_image(self, image_id, name=None, path=None):
        pass

    @abstractmethod
    def delete_image(self, image_id):
        pass

# Адаптер для использования DataManager
class DataManagerAdapter(ImageStorageAdapter):
    def __init__(self):
        self.manager = DataManager()

    def add_image(self, image_id, name, path):
        self.manager.add_image(image_id, name, path)

    def get_image(self, image_id):
        return self.manager.get_image(image_id)

    def update_image(self, image_id, name=None, path=None):
        return self.manager.update_image(image_id, name, path)

    def delete_image(self, image_id):
        return self.manager.delete_image(image_id)
```
### Результаты:

- Адаптер отделяет логику работы с данными от DataManager.
- Позволяет в будущем легко изменить способ хранения (например, добавить работу с базой данных).

## 2. Декоратор

### Описание:

Расширение функциональности работы с изображениями, логирование операций и измерение времени выполнения операций.

### UML-диаграмма:

![image](https://github.com/user-attachments/assets/b3b8fdc6-19f5-4a47-b9a8-35b189a4452a)

### Программный код:
```python
from abc import ABC, abstractmethod
import time

# Интерфейс хранения изображений
class ImageStorage(ABC):
    @abstractmethod
    def add_image(self, image_id, name, path):
        pass

    @abstractmethod
    def get_image(self, image_id):
        pass

    @abstractmethod
    def update_image(self, image_id, name=None, path=None):
        pass

    @abstractmethod
    def delete_image(self, image_id):
        pass

# Конкретный класс хранения изображений (основной функционал)
class DataManager(ImageStorage):
    _instance = None

    def __new__(cls):
        if not cls._instance:
            cls._instance = super().__new__(cls)
            cls._instance.images_data = {}
        return cls._instance

    def add_image(self, image_id, name, path):
        self.images_data[image_id] = {"name": name, "path": path}

    def get_image(self, image_id):
        return self.images_data.get(image_id)

    def update_image(self, image_id, name=None, path=None):
        image = self.images_data.get(image_id)
        if image:
            if name:
                image["name"] = name
            if path:
                image["path"] = path
            return image
        return None

    def delete_image(self, image_id):
        return self.images_data.pop(image_id, None)

# Базовый декоратор
class ImageStorageDecorator(ImageStorage):
    def __init__(self, storage: ImageStorage):
        self._storage = storage

    def add_image(self, image_id, name, path):
        return self._storage.add_image(image_id, name, path)

    def get_image(self, image_id):
        return self._storage.get_image(image_id)

    def update_image(self, image_id, name=None, path=None):
        return self._storage.update_image(image_id, name, path)

    def delete_image(self, image_id):
        return self._storage.delete_image(image_id)

# Декоратор логирования
class LoggingDecorator(ImageStorageDecorator):
    def add_image(self, image_id, name, path):
        print(f"[LOG] Adding image: {name} (ID: {image_id})")
        super().add_image(image_id, name, path)

    def get_image(self, image_id):
        print(f"[LOG] Fetching image with ID: {image_id}")
        return super().get_image(image_id)

    def update_image(self, image_id, name=None, path=None):
        print(f"[LOG] Updating image ID {image_id}")
        return super().update_image(image_id, name, path)

    def delete_image(self, image_id):
        print(f"[LOG] Deleting image ID {image_id}")
        return super().delete_image(image_id)

# Декоратор для измерения времени выполнения операций
class TimingDecorator(ImageStorageDecorator):
    def add_image(self, image_id, name, path):
        start_time = time.time()
        super().add_image(image_id, name, path)
        print(f"[TIMER] add_image executed in {time.time() - start_time:.4f} seconds")

    def get_image(self, image_id):
        start_time = time.time()
        result = super().get_image(image_id)
        print(f"[TIMER] get_image executed in {time.time() - start_time:.4f} seconds")
        return result

    def update_image(self, image_id, name=None, path=None):
        start_time = time.time()
        result = super().update_image(image_id, name, path)
        print(f"[TIMER] update_image executed in {time.time() - start_time:.4f} seconds")
        return result

    def delete_image(self, image_id):
        start_time = time.time()
        result = super().delete_image(image_id)
        print(f"[TIMER] delete_image executed in {time.time() - start_time:.4f} seconds")
        return result
```
### Результаты:

В коде реализованы два декоратора:

`LoggingDecorator` — добавляет логирование операций с изображениями.

`TimingDecorator` — измеряет время выполнения операций.

Использование этих декораторов позволяет динамически расширять функциональность DataManager, не изменяя его код.

## 3. Фасад

### Описание:
Шаблон Фасад объединяет сложные операции работы с изображениями в единый интерфейс. Вместо того чтобы вызывать DataManager напрямую в каждом месте кода, клиент использует ImageProcessingFacade, который упрощает взаимодействие с системой.
### UML-диаграмма:

![image](https://github.com/user-attachments/assets/8b9a60c1-1a97-4fe2-8118-f8d93e9967c6)

### Программный код:
```python
from singleton import DataManager

class ImageProcessingFacade:
    def __init__(self):
        self.data_manager = DataManager()

    def upload_image(self, image_id, name, path):
        self.data_manager.add_image(image_id, name, path)
        return {"id": image_id, "status": "uploaded"}

    def get_image_info(self, image_id):
        image = self.data_manager.get_image(image_id)
        return image if image else {"error": "Image not found"}

    def update_image_info(self, image_id, name=None, path=None):
        updated_image = self.data_manager.update_image(image_id, name, path)
        return updated_image if updated_image else {"error": "Image not found"}

    def delete_image(self, image_id):
        deleted = self.data_manager.delete_image(image_id)
        return {"status": "deleted"} if deleted else {"error": "Image not found"}

```
### Результаты:

- Клиентский код (main.py) работает только с ImageProcessingFacade, а не с DataManager напрямую.
- Сокрытие сложности работы с изображениями за единым интерфейсом.

## 4. Компоновщик

### Описание:

Шаблон служит для организации иерархической структуры изображений.

### UML-диаграмма:

![image](https://github.com/user-attachments/assets/5247b395-5f76-4a10-b2ea-f2e0d5ea623e)

### Программный код:
```python
from abc import ABC, abstractmethod

# Базовый компонент
class ImageComponent(ABC):
    @abstractmethod
    def display(self):
        pass

# Лист — отдельное изображение
class ImageLeaf(ImageComponent):
    def __init__(self, name):
        self.name = name

    def display(self):
        return f"Displaying image: {self.name}"

# Контейнер — группа изображений
class ImageComposite(ImageComponent):
    def __init__(self, name):
        self.name = name
        self.children = []

    def add(self, component: ImageComponent):
        self.children.append(component)

    def remove(self, component: ImageComponent):
        self.children.remove(component)

    def display(self):
        result = f"Album: {self.name}\n"
        for child in self.children:
            result += f"  {child.display()}\n"
        return result
```
### Результаты:
- Можно хранить отдельные изображения и группы изображений одинаково.
- Добавлять и удалять изображения в группы.

# Поведенческие шаблоны

## 1. Наблюдатель

### Описание:

Объект DataManager уведомляет всех своих подписчиков (в данном случае, ImageProcessor) о добавлении нового изображения.

### UML-диаграмма:

![image](https://github.com/user-attachments/assets/afec427a-78f7-4b44-a6e3-fba0f8d0b3b7)

### Программный код:
```python
# Наблюдатель
class Observer:
    def update(self, message):
        raise NotImplementedError()

class ImageProcessor(Observer):
    def update(self, message):
        print(f"ImageProcessor received message: {message}")

class DataManager:
    _observers = []

    def add_observer(self, observer: Observer):
        self._observers.append(observer)

    def remove_observer(self, observer: Observer):
        self._observers.remove(observer)

    def notify_observers(self, message):
        for observer in self._observers:
            observer.update(message)

    def add_image(self, image_id, name, path):
        # логика добавления изображения
        self.notify_observers(f"New image added with ID: {image_id}")
```
### Результаты:
- `DataManager` уведомляет всех подписчиков (`ImageProcessor`) о добавлении нового изображения.
- Упрощено управление зависимостями: новые наблюдатели можно добавлять без изменения кода DataManager.
## 2. Команда

### Описание:

Создание команды для добавления изображения. Команда инкапсулирует все необходимые данные для выполнения действия.

### UML-диаграмма:

![image](https://github.com/user-attachments/assets/40b53265-d658-40d8-8b40-856c2c50c756)

### Программный код:
```python
class Receiver:
    def action(self, image_id, name, path):
        print(f"Processing image: {image_id}, {name}, {path}")

class Command:
    def execute(self):
        raise NotImplementedError()

class AddImageCommand(Command):
    def __init__(self, receiver: Receiver, image_id, name, path):
        self.receiver = receiver
        self.image_id = image_id
        self.name = name
        self.path = path

    def execute(self):
        self.receiver.action(self.image_id, self.name, self.path)

class Invoker:
    def __init__(self):
        self.command = None

    def set_command(self, command: Command):
        self.command = command

    def invoke(self):
        if self.command:
            self.command.execute()
```
### Результаты:
- Введена абстракция команд, отделяющая запрос (`AddImageCommand`) от его выполнения (`Receiver`).
- `Invoker` позволяет динамически назначать и выполнять команды.
## 3. Стратегия

### Описание:

Меняется стратегия распознавания изображения в зависимости от потребностей (распознавание текста или метаданных).

### UML-диаграмма:

![image](https://github.com/user-attachments/assets/fef1ff0f-844c-4bb0-9715-682ae9db7ce9)

### Программный код:
```python
class RecognitionStrategy:
    def recognize(self, image):
        raise NotImplementedError()

class OCRStrategy(RecognitionStrategy):
    def recognize(self, image):
        return f"Recognizing text in {image}"

class MetadataStrategy(RecognitionStrategy):
    def recognize(self, image):
        return f"Recognizing metadata in {image}"

class ImageRecognition:
    def __init__(self, strategy: RecognitionStrategy):
        self.strategy = strategy

    def set_strategy(self, strategy: RecognitionStrategy):
        self.strategy = strategy

    def recognize(self, image):
        return self.strategy.recognize(image)
```
### Результаты:
- Реализован интерфейс стратегий и разные способы обработки изображений (`UMLProcessing`, `BPMNProcessing`).
- Можно динамически переключать алгоритмы обработки без изменения основного кода.
## 4. Цепочка обязанностей

### Описание:

Запросы проходят через цепочку обработчиков: сначала проверка на наличие `image_id`, затем обработка изображения.

### UML-диаграмма:

![image](https://github.com/user-attachments/assets/23619c5e-22b8-4df4-a1cb-6a9b4c1a58cf)

### Программный код:
```python
class Handler:
    def __init__(self, next_handler=None):
        self.next_handler = next_handler

    def handle(self, request):
        if self.next_handler:
            self.next_handler.handle(request)

class ImageValidationHandler(Handler):
    def handle(self, request):
        if not request.get("image_id"):
            print("Error: Missing image_id")
            return
        super().handle(request)

class ImageRecognitionHandler(Handler):
    def handle(self, request):
        print(f"Processing image: {request.get('image_id')}")
        super().handle(request)
```
### Результаты:
- `ImageValidationHandler` проверяет, есть ли `image_id`, и останавливает цепочку при ошибке.
- `ImageRecognitionHandler` выполняет обработку изображения, а затем передает управление следующему обработчику.
- Позволяет добавлять новые обработчики без изменения существующих.

## 5. Шаблонный метод

### Описание:

В процессе обработки изображения, алгоритм всегда будет включать шаги: загрузка, обработка и сохранение.

### UML-диаграмма:

![image](https://github.com/user-attachments/assets/df49ceac-c510-4794-805f-600f41efb914)

### Программный код:
```python
class AbstractRecognizer:
    def recognize(self, image_path):
        """Шаблонный метод, описывающий последовательность шагов"""
        self.load_image(image_path)
        self.process_image()
        self.analyze_structure()
        self.save_results()

    def load_image(self, image_path):
        print(f"Загрузка изображения: {image_path}")

    def process_image(self):
        raise NotImplementedError("Метод process_image() должен быть переопределён")

    def analyze_structure(self):
        raise NotImplementedError("Метод analyze_structure() должен быть переопределён")

    def save_results(self):
        print("Результаты сохранены в базу данных")

class UMLRecognizer(AbstractRecognizer):
    def process_image(self):
        print("Обработка изображения: применение фильтров для UML")

    def analyze_structure(self):
        print("Анализ структуры: поиск UML-диаграмм")

class BPMNRecognizer(AbstractRecognizer):
    def process_image(self):
        print("Обработка изображения: применение фильтров для BPMN")

    def analyze_structure(self):
        print("Анализ структуры: поиск BPMN-диаграмм")

```
### Результаты:
- Разработаны `UMLRecognizer`, `BPMNRecognizer`, которые переопределяют конкретные шаги обработки.
