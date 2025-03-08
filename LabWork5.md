# Порождающие шаблоны

## 1. Singleton

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

## 2. Factory Method

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

## 3. Builder

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

Структурные шаблоны

1. Singleton

### Описание:

### UML-диаграмма:

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

2. Название шаблона

### Описание:

### UML-диаграмма:

### Программный код:

### Результаты:

3. Название шаблона

### Описание:

### UML-диаграмма:

### Программный код:

### Результаты:

4. Название шаблона

### Описание:

### UML-диаграмма:

### Программный код:

### Результаты:

# Поведенческие шаблоны

1. Название шаблона

### Описание:

### UML-диаграмма:

### Программный код:

### Результаты:

2. Название шаблона

### Описание:

### UML-диаграмма:

### Программный код:

### Результаты:

3. Название шаблона

### Описание:

### UML-диаграмма:

### Программный код:

### Результаты:

4. Название шаблона

### Описание:

### UML-диаграмма:

### Программный код:

### Результаты:

5. Название шаблона

### Описание:

### UML-диаграмма:

### Программный код:

### Результаты:

# Шаблоны проектирования GRASP

## Роли (обязанности) классов

### Проблема:

### Решение:

### Программный код:

### Результаты:

Связь с другими паттернами:

Принципы разработки

Проблема:

Решение:

Программный код:

Результаты:

Связь с другими паттернами:

Свойство программы (цель)

Проблема:

Решение:

Программный код:

Результаты:

Связь с другими паттернами:
