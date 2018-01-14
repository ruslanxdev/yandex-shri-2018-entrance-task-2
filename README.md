# Приложение для создания и редактирования информации о встречах сотрудников. Вёрстка

Написано для Node.js 8 и использует библиотеки:

- babel
- gulp
- postcss
- posthtml

## Задание

Нужно сверстать страницу списка переговрок и форму редактирования встречи. Для каждой страницы дизайнер подготовил макет (отдельно для большого экрана и мобильных телефонов).

- [Макет](https://yandex-shri-msk-2018.github.io/entrance-task-2) (просмотр)
- [Репозиторий на GitHub](https://github.com/yandex-shri-msk-2018/entrance-task-2) (скачивание файлов)

Важно сверстать страницы в точном соответствии с макетами. Если какие-то части макетов покажутся вам непонятными, обязательно задавайте уточняющие вопросы — пишите на адрес frontendschool@yandex-team.ru.

Страница списка переговорок

- В шапке отображается логотип сервиса и кнопка «Создать встречу».
- Слева отображается список переговорок с разбивкой по этажам.
- Справа от списка отображается диаграмма встреч.
- При клике на встречу всплывает тултип с информацией о ней.
- При наведении на свободное время появляется кнопка добавления встречи и подсвечивается название комнаты.
- Названия переговорок, у которых не осталось свободных периодов времени, отображаются менее контрастно.
- На диаграмме вертикальной линией показано текущее время.
- Сверху от списка отображается текущая дата и кнопки перехода к соседним датам.
- При клике на текущую дату отображается календарь на три месяца.
- Макет календаря дизайнер не нарисовал, сверстайте этот блок на своё усмотрение.

Страница редактирования встречи

- Есть возможность ввести название встречи, дату и время начала и окончания встречи, выбрать участников и переговорку.
- Выпадающий календарь для поля выбора даты делать необязательно, но это будет плюсом.
- Участники встречи выбираются из списка, который появляется, когда пользователь переходит на поле поиска сотрудника.
- Выбранные участники отображаются под полем ввода.
- Если переговорка не выбрана, отображается список доступных комнат.
- Если переговорка выбрана, то вместо списка отображается только она (с возможностью отменить выбор).

## Запуск

```
npm i
npm run dev
```

Для использования скриптов в командной строке:

```
npm link
```

## Выполнение задания

### 1. Workflow

Добавил task runner и сборщики на основе [Babel](https://github.com/babel/babel), [Gulp](https://github.com/gulpjs/gulp), [PostCSS](https://github.com/postcss/postcss), [PostHTML](https://github.com/posthtml/posthtml).

#### Локальный пакет posthtml-mixins

Для более удобной работы локально изменил пакет [posthtml-mixins](https://github.com/mrmlnc/posthtml-mixins). Добавил следующие возможности:

- **Частичная передача параметров миксина**. Если передавать не все параметры в миксин, то он не отрендериться — это не очень дружелюбно. Решение с несколькими определениями миксинов мне кажется неудобным. Так как нельзя использовать комбинации параметров с одинаковым количеством но разными ключами, они конфликтуют, и приходиться писать много кода.

- **Вложенность миксинов**. Я хочу использовать миксин как компонент, но один компонент может быть частью другого. Сейчас это нельзя сделать, поиск и разделение `MixinDefinition` и `MixinReference` осуществлаяет именно по наличию в них контента. Добавил ноду `<define-mixin>` (как в [postcss-mixins](https://github.com/postcss/postcss-mixins)) и ноду `<content>` (как в [posthtml-modules](https://github.com/posthtml/posthtml-modules)), которые будут определять шаблон и место вставки контента.

- **Неизменяемость шаблона**. Столкнулся с тем, что если делать вложенные мискины с одним именем, то программа уходит в бесконечный цикл. Причина тому, динамическое изменение дерева шаблона. Один миксин вставляется в шаблон, потом проход по дереву доходит до него и снова вставляет миксин и тд. Для мискинов с разными именами этого не происходит. Решил эту проблему путём клонированием дерева шаблона.

Находится по адресу `packages/posthtml-mixins`.

#### Скрипт для генерации файлов компонентов

Для более удобного создания файлов компонетов написал простой bash-скрипт `block.sh`. Он делает следующие действия:

- Создаёт папку для файлов компонента `app/blocks/block1`.
- Создаёт файл со стилями `app/blocks/block1/block1.post.css`.
- Создаёт файл с миксином разметки `app/blocks/block1/block1.post.html`.
- Создаёт файл с реализацией интерфейса `app/blocks/block1/block1.js`.
- Добавляет импорт в главный файл стилей `app/styles/main.post.css`.
- Добавляет импорт в главный файл шаблона разметки `app/templates/main.post.html`.
- Добавляет импорт в главный файл шаблона разметки `app/templates/main.post.html`.

Использование:

```bash
block <block1> <block2>
```

Находится по адресу `bin/block.sh`.

### 2. Архитектура

В качестве архитектуры использовал компонентый подход. В качетсве методологии — БЭМ.

#### Файловая структура

Компоненты находятся в папке `blocks`, каждый в отдельной папке cо следующей файловой структурой:

```
blocks/
└── block1/
    ├── block1.post.css  // файл со стилями
    ├── block1.post.html // файл с миксином разметки
    └── block1.js        // файл с реализацией интерфейса
```

#### Список компонентов

- button
- calendar
- datepicker
- icon
- input
- image
- link
- menu
- modal
- room-item
- rooms-list
- event-item
- timetable
- tooltip
- user-item
