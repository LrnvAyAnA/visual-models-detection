# Стейкхолдеры проекта

## 1. Разработчики
- **Роль:** Основные участники проекта, отвечающие за разработку прототипа, исследование существующих методов распознавания и внедрение алгоритмов распознавания и сопоставления элементов визуальных моделей.

## 2. Конечные пользователи
- **Роль:** Люди, которые будут использовать инструменты распознавания в своей повседневной работе, например, аналитики, проектировщики процессов, программисты и бизнес-аналитики.
- **Интерес:** Удобство использования, точность распознавания, экономия времени при создании и анализе моделей.

## 3. Научное сообщество
- **Роль:** Исследователи и академические учреждения, заинтересованные в новых методах и подходах автоматического распознавания визуальных моделей.

# Функциональные требования

## 1. Распознавание визуальных моделей
- Прототип должен распознавать элементы различных типов диаграмм (UML, IDEF, BPMN и т.д.).
- Система должна идентифицировать типы элементов (например, процессы, связи, объекты).
- Автоматическое определение структуры модели (связи между элементами).

## 2. Сопоставление элементов
- Система должна поддерживать сопоставление элементов между различными диаграммами.
- Необходимо реализовать метрики схожести для оценки соответствия элементов (например, формы, размера, текста).

## 3. Поддержка форматов визуализации
- Прототип должен работать с популярными форматами хранения моделей (например, XML, JSON, SVG).
- Поддержка импорта из инструментов моделирования (например, Visio, Draw.io).

## 4. Визуализация результатов
- Вывод распознанной модели в виде интерактивной диаграммы.
- Подсветка ошибок и возможных несовпадений между элементами.

## 5. Пользовательский интерфейс
- Интуитивно понятный интерфейс для загрузки визуальных моделей.
- Возможность редактирования элементов после распознавания.
- Отображение статистики распознавания (количество элементов, ошибок, степень соответствия).

## 6. Механизмы обучения и настройки
- Возможность настройки алгоритмов распознавания для улучшения их точности.

## 7. Отчетность и экспорт данных
- Генерация отчетов о результатах распознавания (включая ошибки и статистику).
- Экспорт готовых моделей в поддерживаемых форматах (PDF, PNG, JSON и т.д.).

## 8. Масштабируемость
- Прототип должен обрабатывать модели различного размера и сложности.
- Поддержка многопоточности для работы с большими наборами данных.

## 9. Интеграция с внешними системами
- Возможность интеграции с корпоративными инструментами анализа и моделирования.
- API для взаимодействия с другими приложениями.

# Диаграмма прецедентов на основе функциональных требований

![image](https://github.com/user-attachments/assets/5462a9e5-ef41-4f38-bf12-dd571cd26188)


# Перечень сделанных предположений

## 1. Типы визуальных моделей
- Проект рассчитан на работу с распространёнными типами визуальных моделей.
- В проекте предполагается, что дополнительные типы диаграмм могут быть добавлены в будущем.

## 2. Форматы входных данных
- Входные данные представлены в электронных форматах (например, XML, JSON, SVG, PNG).
- Ручное сканирование бумажных диаграмм или рукописных схем в рамках проекта не рассматривается.

## 3. Обработка ошибок
- В случае некорректного ввода система предполагает наличие встроенных механизмов для уведомления пользователя о проблемах.
- Ошибки и несовпадения в распознавании будут отображаться в виде списка или выделяться визуально.

## 4. Уровень точности
- Проект нацелен на высокий уровень точности распознавания, но абсолютная точность может не быть достигнута в сложных или нестандартных случаях.
- Допускается вмешательство пользователя для корректировки результата.

## 5. Пользовательская аудитория
- Основными пользователями являются специалисты, имеющие базовые знания в области визуального моделирования (например, аналитики, бизнес-архитекторы, программисты).
- Система рассчитана на использование профессионалами, а не на широкую аудиторию.

## 6. Среда работы
- Не рассматриваются сценарии использования на устройствах с ограниченными ресурсами (например, смартфонах).

## 7. Интеграция
- Проект предполагает возможность последующей интеграции с другими системами моделирования и анализа данных через API или стандартные форматы экспорта (не точно).
- Ограничения на взаимодействие с проприетарными системами на данном этапе не учтены.

## 8. Масштабируемость
- Предполагается, что проект сможет работать как с небольшими диаграммами, так и с крупными иерархическими моделями.
- Производительность прототипа оценивается на среднем уровне сложности задач.

# Нефункциональные требования

## 1. Производительность
- Система должна распознавать диаграммы размером до 50 элементов за время не более 10 секунд при стандартных параметрах оборудования.
- Время отклика интерфейса не должно превышать 3 секунд при загрузке или анализе данных.

## 2. Масштабируемость
- Поддержка загрузки файлов размером до 5 MB.
- Система должна корректно работать с диаграммами, содержащими до 5 уровней вложенности.

## 3. Надёжность
- При загрузке файла некорректного формата система должна уведомить пользователя об ошибке без прерывания работы.
- В случае сбоя система должна сохранять промежуточное состояние, чтобы пользователь мог продолжить работу.

## 4. Удобство использования
- Пользовательский интерфейс должен содержать понятные кнопки и уведомления (например, "Загрузить файл", "Распознать").
- Для выполнения основных операций (загрузка файла, анализ, экспорт) должно быть достаточно трёх действий пользователя.

## 5. Совместимость
- Система должна поддерживать форматы JSON, XML и SVG для входных данных.
- Должна быть возможность экспорта результатов анализа в CSV или PNG.

## 6. Логирование
- Система должна вести лог ошибок (например, "не удалось распознать элемент") для анализа и отладки.

## 7. Точность
- Точность распознавания элементов базовых диаграмм должна составлять не менее 80%.
- Пользователь должен иметь возможность вручную корректировать ошибки распознавания.

## 8. Доступность
- Система должна корректно отображаться на экранах с разрешением от 1280x720 пикселей.

## 9. Легкость в обучении
- Новому пользователю должно быть достаточно 10 минут, чтобы разобраться в работе системы, используя инструкцию или всплывающие подсказки.
- Для каждой функции системы должна быть доступна краткая справка.


