### Домашнее задание к занятию "5.1. Введение в виртуализацию. Типы и функции гипервизоров. Обзор рынка вендоров и областей применения."
---
## Задача 1
Опишите кратко, как вы поняли: в чем основное отличие полной (аппаратной) виртуализации, паравиртуализации и виртуализации на основе ОС.

### Ответ

```
паравиртуализации виртуализация, в которой гостевая операционная система (виртуализируемая) знает, что она является гостевой и, соответственно, имеет драйверы, которые вместо выдачи аппаратных команд просто выдают команды непосредственно в хост-операционную систему. Это также включает в себя управление памятью и потоками, которые обычно требуют недоступных привилегированных инструкций в системе. процессор.

Полная Виртуализация виртуализация, в которой гостевая операционная система не знает, что она находится в виртуализированной среде, и поэтому аппаратное обеспечение виртуализируется операционной системой хоста, чтобы гость мог выдавать команды тому, что он считает фактическим оборудованием, но на самом деле это просто имитированные аппаратные устройства, созданные хостом.

Аппаратная Виртуализация тип полной виртуализации где архитектура микропроцессора имеет особенные инструкции помочь виртуализации оборудования. Эти инструкции могли бы позволить виртуальному контексту быть настроенным так, чтобы гость мог выполнить привилегированные инструкции непосредственно на процессоре, не затрагивая хост. Такой набор функций часто называют гипервизор. Если указанные инструкции не существуют, полная виртуализация все еще возможна, однако это необходимо сделать с помощью программных методов, таких как Динамическая Перекомпиляция где хост перекомпилирует на лету привилегированные инструкции в ГОСТе, чтобы иметь возможность работать непривилегированным способом на хосте.

Существует также сочетание пара виртуализации и полной виртуализации под названием Гибридный Виртуализации где части гостевой операционной системы паравиртуализации для некоторых драйверов, а хост использует полную виртуализацию для других особенности. Это часто производит превосходную производительность на гостя без необходимости для оценки, чтобы быть полностью паравиртуализированной. Пример этого: гость использует полную виртуализацию для привилегированных инструкций в ядре, но паравиртуализацию для запросов ввода-вывода с использованием специального драйвера в ГОСТе. Таким образом, гостевая операционная система не должна быть полностью паравиртуализирована, так как это иногда недоступно, но все еще может пользоваться некоторыми паравиртуализированными функциями путем реализации специальных водители для гостя.

Другими словами апаратная виртуализация это когда гипервизор ставится на само железо.
Паравиртуализация это когда сначала ставится операционная система, а в ней уже ставится приложение для создания виртуальных машин.
Виртуализация на основе ОС это когда на железо ставится операционка, а потом сразу виртуалка. Т.е. нет промежуточного гипервизора в котором разворачиваются виртуалки как в паравиртуализации. В таком случае поддерживается только одна операционная система.
```
## Задача 2
Выберите один из вариантов использования организации физических серверов, в зависимости от условий использования.

Организация серверов:

физические сервера,
паравиртуализация,
виртуализация уровня ОС.
Условия использования:

Высоконагруженная база данных, чувствительная к отказу.
Различные web-приложения.
Windows системы для использования бухгалтерским отделом.
Системы, выполняющие высокопроизводительные расчеты на GPU.
Опишите, почему вы выбрали к каждому целевому использованию такую организацию.

### Ответ
```
Высоконагруженная база данных, чувствительная к отказу
    Физический сервер 
        требуется более высокая производительность, аппаратное размещение дает более высокий отклик, 
        и сокращает точки отказа в виде гипервизора хостовой машны.
        хотя возможно (и используется) использование полной  аппаратной виртуализации при использовании 
        кластеризации для повышения доступности (такиесервера БД имеются у нас организации)
         
Различные Java-приложения
    Виртуализация уровня ОС (контейнеры)
        Требуется меньше ресурсов, выше скорость масштабирования при необходимости расширения. 
        нет жестких требований к аппаратнымм ресурсам, требет меньше ресурсов на администрирование

Windows системы для использования Бухгалтерским отделом
    Паравиртуализация 
        эффективнее делать бэкаприрование -  клонирование всей вирт. машины, 
        возможность расширени ресурсов на уровне вирт. машины. 
        нет критичных требований к доступу к аппаратной составляющей сервера.
        
Системы, выполняющие высокопроизводительные расчеты на GPU
    Физические сервера 
        как мне кажется, для аппаратнх расчетов требуется максимальный доступ к аппаратным ресурсам,
        который физический сервер даетболее эффективно, и а в 2х других, доступ осуществляется черех хостовую ОС 
```
## Задача 3
Выберите подходящую систему управления виртуализацией для предложенного сценария. Детально опишите ваш выбор.
Сценарии:

1. 100 виртуальных машин на базе Linux и Windows, общие задачи, нет особых требований. Преимущественно Windows based инфраструктура, требуется реализация программных балансировщиков нагрузки, репликации данных и автоматизированного механизма создания резервных копий.
2. Требуется наиболее производительное бесплатное open source решение для виртуализации небольшой (20-30 серверов) инфраструктуры на базе Linux и Windows виртуальных машин.
3. Необходимо бесплатное, максимально совместимое и производительное решение для виртуализации Windows инфраструктуры.
4. Необходимо рабочее окружение для тестирования программного продукта на нескольких дистрибутивах Linux.
### Ответ
```
1. VSphere, удобно настравивать, делать копии, нет промежуточной ОС
2. KVM, бесплатный, хорошо развивается
3. Hyper-V, продукт Microsoft и нацелен на Windows
4. Virlual Box, удобно разворачивать машины через Vagrant
```		
## Задача 4
Опишите возможные проблемы и недостатки гетерогенной среды виртуализации (использования нескольких систем управления виртуализацией одновременно) и что необходимо сделать для минимизации этих рисков и проблем. Если бы у вас был выбор, то создавали бы вы гетерогенную среду или нет? Мотивируйте ваш ответ примерами.
### Ответ
```
В гетерогенной среде сокращена возможность автоматического распределения вычислительных ресурсов, что увеличит вероятность появления дефицита вычислительных ресурсов на одном из участков виртуальной среды
Трудно управлять виртуалками в такой среде, особенно когда их надо обслуживать, перемещать, давать дополнительные ресурсы, когда возможно на одном сервере еще есть ресурсы, а на другом уже мало, а поделить не получится.
```