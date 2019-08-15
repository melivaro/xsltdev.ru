# Thinking in Graphs

## Это Графы идут вниз всё время \*

> С GraphQL, вы проектируете область бизнеса в виде графа

Графы - мощные инструменты для моделирования многих реальных явлений, т. к. они напоминают наши естественные мысленные модели и словесные описания внутренних процессов. С GraphQL, вы моделируете вашу область бизнеса как граф путем определения схемы; внутри этой схемы, вы определяете разные типы узлов и как они соединены/относятся друг-к-другу. На клиенте GraphQL создает паттерн, аналогичный ООП: типы, ссылающиеся на другие типы. На сервере, т. к. GraphQL только определяет интерфейс, вы вольны использовать его с любым бекендом (новым или наследием!).

## Shared Language

> Дать название чему-либо трудно, но это важная часть создания интуитивных интерфейсов API.

Думайте о вашей схеме GraphQL как о выразительном языке, общем для вашей команды и ваших пользователей. Чтобы построить хорошую схему, проанализируйте язык, который вы используете каждый день для описания вашего бизнеса. Например, давайте попробуем описать Email приложение простым языком:

- Пользователь может иметь несколько учетных записей электронной почты
- Каждая учетная запись электронной почты имеет адрес, входящие, черновики, удаленные элементы, а также отправленные элементы
- Каждое сообщение имеет отправителя, дату получения, тему и тело
- Пользователи не могут отправлять электронную почту без адреса получателя

Дать название - сложная, но важная часть в построении интуитивных API, так что уделите время тому, чтобы подумать на тем, что имеет смысл для вашей проблемной области и пользователей. Ваша команда должна разрабатывать, опираясь на общее понимание и согласованность правил вашего бизнеса, потому что вам нужно будет выбрать интуитивные, надежные имена для узлов и связей в схеме GraphQL. Попробуйте представить некоторые запросы, которые вы захотите выполнить:

Получить количество непрочитанных писем в почтовом ящике для всех моих аккаунтов

```graphql
{
  accounts {
    inbox {
      unreadEmailCount
    }
  }
}
```

Получить данные "предварительный просмотр" для первых 20 черновиков в основном аккаунте

```graphql
{
  mainAccount {
    drafts(first: 20) {
      ...previewInfo
    }
  }
}

fragment previewInfo on Email {
  subject
  bodyPreviewSentence
}
```

## Business Logic Layer

> Ваш слой бизнес логики должен выступать как единый источник истины для обеспечения правил бизнеса

Где вы должны определять актуальную бизнес логику? Где вы должны выполнять проверки валидации и авторизации? Ответ: внутни выделенного слоя бизнес логики. Слой вашей бизнес логики должен выступать как единый источник истины для обеспечения правил бизнеса.

В приведенной выше диаграмме, все точки входа (REST, GraphQL и RPC) в систему будут обрабатываться с одинаковыми
правилами проверки, авторизации и обработки ошибок.

## Working with Legacy Data

> Отдавайте предпочтение построению схем GraphQL, которые описывают, как клиенты используют данные, а не делайте просто отражение наследованной схемы БД.

Иногда вы будете работать с унаследованными источниками данных, которые неидеально отражают то, как клиенты используют данные. В этом случае, отдавайте предпочтение построению схем GraphQL, которые описывают, как клиенты используют данные, а не делайте просто отражение наследованной схемы БД.

## One Step at a time

> Чаще выполняйте проверку и обратную связь (Прим.: тут имеется в виду обратная связь о работе схемы, корректна она или требует доработки)

Не пытайтесь смоделировать весь бизнес за один присест. Лучше постройте только часть схемы, которая нужная вам для одного сценария. Постепенно расширяя схему, вы будете получать проверку и обратную связь чаще, что приведет вас к построению правильного решения.