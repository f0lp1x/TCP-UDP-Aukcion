## Основные компоненты кода

### 1. Пространства имен и классы

```csharp
using System.ComponentModel;
using System.Numerics;
using Lab5.Network.Common;
using Lab5.Network.Common.UserApi;
```
Эти пространства имен содержат необходимые классы и интерфейсы для работы с сетью и API пользователей.

### 2. Основной класс `Program`

```csharp
internal class Program
{
    private static object _locker = new object();
```
Класс `Program` содержит статический метод `Main`, который является точкой входа в приложение. Переменная `_locker` используется для синхронизации доступа к консоли.

### 3. Метод `Main`

```csharp
public static async Task Main(string[] args)
{
    var serverAdress = new Uri("tcp://127.0.0.1:5555");
    var client = new NetTcpClient(serverAdress);
    Console.WriteLine($"Connect to server at {serverAdress}");
    await client.ConnectAsync();

    var userApi = new UserApiClient(client);
    await ManageUsers(userApi);
    client.Dispose();
}
```
- Создается объект `Uri`, указывающий адрес сервера.
- Создается клиент TCP (`NetTcpClient`), который подключается к серверу.
- Создается экземпляр `UserApiClient`, который будет использоваться для взаимодействия с API пользователей.
- Вызывается метод `ManageUsers`, который управляет пользователями через интерфейс пользователя.

### 4. Метод `ManageUsers`

Этот метод отвечает за взаимодействие с пользователем через консоль:

```csharp
private static async Task ManageUsers(IUserApi userApi)
{
    PrintMenu();

    while (true)
    {
        var key = Console.ReadKey(true);
        PrintMenu();
```
- Выводится меню, и начинается бесконечный цикл, который ожидает ввода от пользователя.

#### Обработка ввода пользователя

Внутри цикла проверяются нажатые клавиши:

- **Клавиша 1**: Выводит всех пользователей (донаты).
  
```csharp
if (key.Key == ConsoleKey.D1) 
{
    var users = await userApi.GetAllAsync();
    // Вывод списка пользователей
}
```

- **Клавиша 2**: Показать донат по ID.
  
```csharp
if (key.Key == ConsoleKey.D2) 
{
    // Получение и вывод данных о донате по ID
}
```

- **Клавиша 3**: Добавить новый донат.
  
```csharp
if (key.Key == ConsoleKey.D3) 
{
    // Считывание данных от пользователя и добавление нового доната
}
```

- **Клавиша 4**: Обновить существующий донат.
  
```csharp
if (key.Key == ConsoleKey.D4) 
{
    // Получение текущих данных и обновление статуса доната
}
```

- **Клавиша 5**: Удалить донат.
  
```csharp
if (key.Key == ConsoleKey.D5) 
{
    // Удаление доната по ID
}
```

- **Клавиша Escape**: Выход из программы.

### 5. Метод `PrintMenu`

```csharp
private static void PrintMenu()
{
    lock (_locker)
    {
        Console.WriteLine("1 - Вывести все донаты");
        Console.WriteLine("2 - Показать донат по id");
        Console.WriteLine("3 - Задонатить");
        Console.WriteLine("4 - Изменить донат");
        Console.WriteLine("5 - Удалить донат");
        Console.WriteLine("-------");
    }
}
```
Этот метод отвечает за вывод меню на экран, обеспечивая синхронизацию доступа к консоли с помощью блокировки.

Код, который вы предоставили, представляет собой консольное приложение на C#, использующее UDP для взаимодействия с сервером. Давайте разберем его по частям.

## Основные компоненты кода

### 1. Пространства имен и класс

```csharp
using Lab5.Network.Common;

internal class Program
{
    private static object _locker = new object();
```
- **`using Lab5.Network.Common;`**: Импортирует пространство имен, необходимое для работы с сетевыми компонентами.
- **`internal class Program`**: Определяет внутренний класс `Program`, который содержит основной метод и другие методы приложения.
- **`private static object _locker`**: Создает объект для синхронизации доступа к консоли, чтобы избежать конфликтов при выводе информации.

### 2. Метод `Main`

```csharp
private static async Task Main(string[] args)
{
    Console.WriteLine("Hello, World!");

    var serverAdress = new Uri("udp://127.0.0.1:7777");
    var client = new NetUdpClient(serverAdress);
    Console.WriteLine($"Connect to server at {serverAdress}");

    var messageApi = new MessageApiClient(client);
    await ManageMessages(messageApi);
    client.Dispose();
}
```
- **`Console.WriteLine("Hello, World!");`**: Выводит приветственное сообщение.
- **`var serverAdress = new Uri("udp://127.0.0.1:7777");`**: Определяет адрес сервера, к которому будет подключаться клиент по протоколу UDP.
- **`var client = new NetUdpClient(serverAdress);`**: Создает экземпляр клиента для работы с UDP.
- **`var messageApi = new MessageApiClient(client);`**: Создает клиент API для отправки и получения сообщений.
- **`await ManageMessages(messageApi);`**: Вызывает метод для управления сообщениями.
- **`client.Dispose();`**: Освобождает ресурсы клиента после завершения работы.

### 3. Метод `ManageMessages`

```csharp
private static async Task ManageMessages(IMessageApi messageApi)
{
    PrintMenu();

    while (true) {
        var key = Console.ReadKey(true);
        PrintMenu();

        if (key.Key == ConsoleKey.D1) 
        {
            Console.Write("Enter message: ");
            var message = Console.ReadLine() ?? string.Empty;
            await messageApi.SendMessage(message);
            Console.WriteLine($"Message sent: {message}");
        }
        if (key.Key == ConsoleKey.D2) 
        {
            Console.Write("Напишите хорошее пожелание: ");
            var pizzaType = Console.ReadLine() ?? string.Empty;
            await messageApi.OrderPizza(pizzaType);
            Console.WriteLine($"Ваше пожелание: {pizzaType}");
        }
        if (key.Key == ConsoleKey.Escape)
        {
            break;
        }
    }
    Console.ReadKey();
}
```
- **Вывод меню**: Метод вызывает `PrintMenu()` для отображения доступных действий.
- **Обработка ввода пользователя**:
  - **Клавиша 1**: Позволяет пользователю ввести сообщение и отправить его на сервер через `messageApi.SendMessage`.
  - **Клавиша 2**: Позволяет пользователю ввести пожелание (например, заказать пиццу) и отправить его через `messageApi.OrderPizza`.
  - **Клавиша Escape**: Завершает работу приложения.

### 4. Метод `PrintMenu`

```csharp
private static void PrintMenu()
{
    lock (_locker)
    {
        Console.WriteLine("1 - Send message");
        Console.WriteLine("2 - Пожелание");
        Console.WriteLine("-------");
    }
}
```
Этот метод отвечает за вывод меню на экран, используя блокировку `_locker`, чтобы избежать конфликтов при одновременном доступе из разных потоков.
# Работа программы
![](https://github.com/f0lp1x/TCP-UDP-Aukcion/blob/master/TCP%D0%A0%D0%B0%D0%B1%D0%BE%D1%82%D0%B0.png)
![](https://github.com/f0lp1x/TCP-UDP-Aukcion/blob/master/UDP%D0%A0%D0%B0%D0%B1%D0%BE%D1%82%D0%B0.png)
![](https://github.com/f0lp1x/TCP-UDP-Aukcion/blob/master/photo_2024-12-06_17-22-09.jpg)

# Мама пока не на порше, это повод вставать с кровати
![](https://github.com/f0lp1x/TCP-UDP-Aukcion/blob/master/photo_5375523416783839440_y.jpg)
