# JAVA REST-сервіс з підтримкою системи «Трембіта»

REST-сервіс, описаний в даній інструкції, розроблений мовою програмування Java з використанням фреймворку Spring, підтримує синхронний та асинхронний режим роботи і є сумісним з системою «Трембіта».

Spring - це сучасний, швидкий (високопродуктивний) фреймворк для Java.

Даний сервіс передбачає отримання з бази даних (реєстру) відомостей про інформаційні об'єкти (користувачів) та управління їх статусом, у тому числі обробку запитів на пошук, отримання, створення, редагування та видалення об'єктів.

Для демонстрації інтеграції з системою «Трембіта» було розроблено [вебклієнт](https://github.com/MadCat-88/Trembita_J_R_SyncCli) для роботи з даним вебсервісом.


## Вимоги до програмного забезпечення 
| ПЗ            |   Версія   | Примітка                                                                                                                                                                                               |
|:--------------|:----------:|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ubuntu Server |   24.04    | Рекомендовані характеристики віртуальної машини:<br/> CPU: 1 <br/> RAM: 512 Мб                                                                                                                         |
| Java        | **17+**  | За допомогою скрипта встановлюється автоматично.<br/> Також можна встановити вручну при виборі відповідного типу інсталяції.<br/>**Важливо!** Якщо версія Java нижче 17, сервіс працювати не буде. |
| PostgreSQL       |   14.12+    | За допомогою скрипта встановлюється автоматично.<br/> Також можна встановити вручну при виборі відповідного типу інсталяції                                                                            |
| Git           |            | Для клонування репозиторію                                                                                                                                                                             |


## Залежності

Залежності програмного забезпечення вебсервісу зазначені в файлі `requirements.txt`.


## Інсталяція сервісу

Сервіс можна інсталювати за допомогою скрипта автоматичного встановлення або вручну.
- [Інсталяція сервісу за допомогою скрипта автоматичного встановлення](./docs/script_installation.md).
- [Інсталяція сервісу вручну](./docs/manual_installation.md).
- [Конфігурація сервісу](./docs/configuration.md).



## Наповнення бази даних тестовими записами

Для забезпечення зручності тестування розроблених REST-вебсервісів потрібно наповнити їх БД тестовими записами.

З цією метою було створено окремий скрипт на мові програмування Python, який використовує бібліотеку `Faker` для генерації тестових записів для бази даних (реєстру) користувачів.

Скрипт генерує тестові дані для записів про користувачів, такі як:

- ім'я, 
- прізвище,
- по батькові,
- дата народження,
- РНОКПП,
- УНЗР,
- номер паспорта (формат для ID-карти, без серії),
- стать.

І надсилає ці дані на локальний сервер за адресою http://127.0.0.1:8080/person.

Запит виконується з використанням бібліотеки `requests`, дані надсилаються у форматі JSON. Успішні запити відображають відповідь сервера, а помилкові — статусний код і текст відповіді.

**Примітка:** При встановленні вебсервісу за допомогою скрипта автоматичного встановлення даний скрипт встановлюється разом з вебсервісом.

Для його запуску на віртуальній машині з розгорнутим та налаштованим вебсервісом необхідно виконати наступні дії:

## Встановлення

1.  Встановити Python:

```bash
sudo apt install python3 python3-pip -y
```

2. Встановити бібліотеку Faker:
 
```bash 
python3 -m pip install faker
```

3. Перейти до директорії зі скриптом:

```bash
cd Trembita_PutFakeData_Rest
```

3.  Запустити скрипт:

```bash
python3 fill_fakerdb.py 100
```
де 100 - кількість об'єктів (користувачів), яка буде згенерована та додана до БД. Можна встановити необхідну кількість записів.


## Адміністрування сервісу

Для адміністрування вебсервісу було створено скрипт `springws-service.sh`.
За допомогою даного скрипта можна:
- створити файл системної служби типу systemd для запуску вебсервісу та додає його до автозапуску;
- запусти вебсервіс;
- зупинити вебсервіс;
- видалити вебсервіс.

Для виконання необхідної дії нобхідно:

1. Перейти в директорію з вебсервісом:

```bash
cd SpringWSrest/
```

2. Запустити скрипт:

```bash
sudo bash springws-service.sh
```
3. Ввести цифру необхідної дії (1,2,3 і тд).

**Примітка:** Після встановлення вебсервісу необхідно, за допомого скрипта, створити створити файл системної служби типу systemd для запуску вебсервісу.

### Ознайомлення з документацією АРІ

Після запуску вебсервісу можна отримати доступ до автоматичної **документації API** за наступними адресами:

- Swagger UI: http://[адреса серверу]:8080//swagger-ui/index.html
- ReDoc: http://[адреса серверу]:8080/

### Перевірка статусу вебсервісу

```bash
sudo systemctl status springws_trembita_service
```

### Перегляд журналу подій

За замовчуванням журнал подій зберігається у файлі `springWSrest.log`.

Конфігурація параметрів журналювання подій виконується в файлі «config.ini». Детальніше з параметрами журналювання в даному файлі можна ознайомитися в [настановах з конфігурації](./docs/configuration.md).

Для того, щоб переглянути журнал подій вебсервісу необхідно виконати команду:

```bash
journalctl -u springws.service
```

### Налаштування HTTPS

Налаштування підключення до сервісу за протоколом HTTPS наведено [в інструкції](./docs/https_nginx_reverse_proxy.md).

## Інтеграція вебсервісу з системою «Трембіта»

Системи «Трембіта» не вимагає особливої спеціалізації вебсервісів для роботи з нею. Для повноцінної інтеграції з системою «Трембіта» вебсервіс повинен підтримувати можливість зберігання заголовків системи «Трембіта», які було передані в запиті від вебкліента через ШБО.
В даному вебсервісі за це відповідає наступний фрагмент коду в файлі `main.py`:

```
# Логування всіх заголовків
headers = dict(request.headers)
logger.info("Заголовки запиту:")
for header_key, header_value in headers.items():
logger.info(f"    {header_key}: {header_value}")

# Логування додаткових параметрів запиту Трембити
if queryId:
    logger.info(f"Значення параметру запиту queryId: {queryId}")
if userId:
    logger.info(f"Значення параметру запиту userId: {userId}")  
```

де:
- header_key – заголовок запиту системи «Трембіта»;
- header_value – значення заголовку.

Більш детальну інформацію про заголовки наведено в описі [роботи із REST-сервісами в системі «Трембіта»](https://github.com/MadCat-88/Services-development-for-Trembita-system/blob/main/REST%20services%20development%20for%20Trembita%20system.md#%D0%B7%D0%B0%D0%B3%D0%BE%D0%BB%D0%BE%D0%B2%D0%BA%D0%B8-%D0%B7%D0%B0%D0%BF%D0%B8%D1%82%D1%96%D0%B2-%D0%B4%D0%BB%D1%8F-rest-%D1%81%D0%B5%D1%80%D0%B2%D1%96%D1%81%D1%96%D0%B2-%D0%BD%D0%B5%D0%BE%D0%B1%D1%85%D1%96%D0%B4%D0%BD%D1%96-%D0%B7%D0%B0%D0%B4%D0%BB%D1%8F-%D0%B7%D0%B0%D0%B1%D0%B5%D0%B7%D0%BF%D0%B5%D1%87%D0%B5%D0%BD%D0%BD%D1%8F-%D1%81%D1%83%D0%BC%D1%96%D1%81%D0%BD%D0%BE%D1%81%D1%82%D1%96-%D0%B7-%D1%81%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D0%BE%D1%8E-%D1%82%D1%80%D0%B5%D0%BC%D0%B1%D1%96%D1%82%D0%B0).
- queryId та userId – додаткові параметри запиту, які передаються в URL.

- Більш детальну інформацію про додаткові параметри наведено в розділі [Використання сервісу](./docs/using.md).

## Використання сервісу

Вебсервіс підтримує синхронний та асинхронний режим роботи.

Синхронний режим роботи вебсервісу представляє собою набір з 5 методів, які дозволяють управляти записами про умовних користувачів (Person) в БД:

- [створення нового запису](./docs/using.md#Post-Person);
- [отримання всіх записів з БД](./docs/using.md#Get-Person-List);
- [оновлення існуючого запису за кодом УНЗР](./docs/using.md#Update-Person);
- [отримання запису по заданому критерію пошуку](./docs/using.md#Get-Person-By-Parameter);
- [видалення існуючого запису за кодом УНЗР](./docs/using.md#person-delete).

Асинхронний режим роботи предствалений одними методом, який виконує [перевірку обраного користувача на протязі 5 хвилин](./docs/using.md#Person-Check).

Також існує окремий метод, за допомогою якого можна [перевірити стан вебсервісу](./docs/using.md#Check-Service-Status)

Після встановлення вебсервісу його база даних порожня. 
Для демонстрації можливостей вебсервісу першим кроком необхідно створити нові записи в БД. Це можна зробити відповідним [вебклієнтом](./README.md#Наповнення-бази-даних-тестовими-записами) з використанням методу [Person Post](./docs/using.md#Post-Person), або за допомогою [скрипта наповнення бази даних](./README.md#наповнення-бази-даних-тестовими-записами).

## Внесок

Якщо ви хочете зробити свій внесок у проєкт, будь ласка, створіть форк репозиторію і відправте Pull Request.

## Ліцензія

Цей проєкт ліцензується відповідно до умов MIT License.

 ##
Матеріали створено за підтримки проєкту міжнародної технічної допомоги «Підтримка ЄС цифрової трансформації України (DT4UA)».


