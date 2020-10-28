# Kode
Задание 1:
- Проблем с выполнением не возникло (решение заняло около 30 минут)

Задание 2:
- Экран авторизации:
Т.к. авторизация не серверная, сделал следующим образом: валидные логин и пароль хранятся в компоненте Auth в хэшированном виде (использовал hash md5), пользователь вводит логин и пароль, от них вычисляется hash md5 и сравнивается в валидным hash, если равно, то идёт перенаправление на ввод OTP. (Б - безопасность :) )
- Экран подтверждения одноразового пароля (OTP): 
Одноразовый код генерируется на клиенте и выводится alert'ом, как будто он пришёл из СМС, в компоненте AuthOTP так же хранится вычисленный hash md5 валидного пароля. Введённый клиентом OTP хешируется и сравнивается с валидным hash. Если код введён верно, то двухфакторная аутентификация успешно пройдена, вызывается функция setSessionKeyInLocalStorage(), которая устанавливает 'sessionKey' в Local Storage (является заглушкой, т.к. в идеале после прохождения авторизации sessionKey должен приходить с сервера).
- Экран категорий и просмотра карточек:
    1. При переходе на данную страницу вызывается функция checkSessionKeyInLocalStorage(), которая проверяет наличие sessionKey в Local Storage и сравнивает его с валидным значением (является заглушкой, т.к. валидность sessionKey должна проверяться на сервере). Если установлено валидное значение sessionKey, то рендерится компонент Home, иначе идёт перенаправление на /login.
    2. В компоненте Header имеется кнопка Logout, по клику на которую происходит выход из приложения, вызывается функция deleteSessionKeyFromLocalStorage().
    3. Компонент Cards отвечает за отображение набора карточек в зависимости от текущего состояния (типа, подтипа карточек, страницы пагинации).
    4. Компонент NavbarLeft позволяет отфильтровать карточки по типу и подтипу: например, при клике на определённый тип карточек отработает переданный callback setType(), который изменит состояние компонента Cards => подгрузятся карточки выбранного типа и вызовется повторный рендер карточек.
    5. Компонент Pagination позволяет отображать выбранную страницу с карточкамиs: при клике на номер страницы вызовется переданный callback setPaginationPage(), который изменит состояние компонента Cards => подгрузятся карточки выбранной страницы и вызовется повторный рендер карточек.
- Просмотр карточки Покемона:
Компонент ExtendedCard отображает расширенную информацию о выбранной карточке: в хуке useEffect() происходит запрос за данными выбранной карточки, как только данные пришли, вызовом setCard() изменяется состояние компонента, и происходи рендер расширенной карточки. Так же имеется кнопка Back для возврата по истории к основному компоненту Home с карточками.

Возникшие проблемы:
1. Проблема с API:
    Сначала поставил пакет pokemontcgsdk для работы с API, но при запросе карточек параметр page не проставляется в запросе, ставится дефолтное значение page=1

    // Get cards on a specific page / pageSize
    pokemon.card.where({ page: 50, pageSize: 500})
    .then(cards => {
        console.log(cards[0].name)
    })

    Решение:
    Использовал вместо пакет pokemontcgsdk, обычный fetch запрос. Написал function Query, которая делает запрос на 'https://api.pokemontcg.io/v1/' и выставляет верные значения параметров.

2. Структура JSON ответа с расширенной информацией о покемоне.
    Для упрощения получения соответствующего свойства покемона из JSON ответа использовал функцию getObjectProperty() из первого задания, не зря же писал)

Время выполнения второго задания:
    1), 2): Двухфакторная аутентифкация - около 1 дня;
    3): Основная страница с карточками + фильтрация + pagination - около 4 дней;
    4): Просмотр расширенной карточки - меньше дня;
    Сопутствующий функционал: Local Storage, разбор React Router, анимации интерфейса - около 1 дня;
    Суммарное время выполнения: +- 7 дней;



