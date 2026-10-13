# Практическая работа №5 Одномерные массивы в C#, класс System.Array и модульная консольная RPG😁
# Разработчики: Гильметднов Роман, Бабыкин Никита
## Игра 🎮
### Выполнена в сеттинге "Древнерусская застава (Богатырский дозор)"

```csharp
using System;

class Program
{
    static void Main()
    {
        // --- ПОДГОТОВКА И ИНИЦИАЛИЗАЦИЯ ДАННЫХ ---
        Console.WriteLine("Начинается проверка боевой готовности пограничной заставы.");
        Console.WriteLine("Загрузка данных о снаряжении, маршрутах и силах противника...\n");

        //  Массив снаряжения богатыря
        string[] equipment = new string[5] {
            "Булатный меч",
            "Кованый щит",
            "Колчан каленых стрел",
            "Горбушка хлеба",
            "Шелковый шатер"
        };

        // Параллельные массивы для вражеских сил:
        // чин супостата, число воинов в сотне, боевая ярость (+ здоровье для боя)
        string[] enemyRank = new string[3] { "Степной дозорный", "Лесной разбойник", "Степной хан" };
        int[] enemyWarriors = new int[3] { 25, 60, 100 };
        int[] enemyFury = new int[3] { 10, 20, 35 };
        int[] enemyHP = new int[3] { 35, 65, 130 };

        // 3. Массив верст патрулирования границы
        int[] verstsCovered = new int[5] { 15, 22, 18, 30, 25 };

        // Лог ударов богатыря для 5 раундов (Пункт 6)
        int[] strikePowerLog = new int[5];

        Random randomGenerator = new Random();
        int heroMaxHP = 100;
        int heroCurrentHP = heroMaxHP;
        int breadCount = 3; // Количество горбушек хлеба

        //вывод амуниции
        Console.WriteLine("Выводим полный список амуниции перед отправлением:");
        int slotNumber = 1;
        foreach (string item in equipment)
        {
            Console.WriteLine($"Слот №{slotNumber}: {item}");
            slotNumber++;
        }
        Console.WriteLine();

        //Исп. щита 
        Console.WriteLine("Вражеские лучники заметили богатыря и начинают обстрел!");
        Console.WriteLine("Требуется срочно найти средство защиты в инвентаре...");

        int shieldIndex = Array.IndexOf(equipment, "Кованый щит");
        bool hasShield = shieldIndex != -1;     // есть ли щит в снаряжении

        if (hasShield)
        {
            Console.WriteLine($"Защита найдена! Предмет 'Кованый щит' расположен под индексом: {shieldIndex}");

            while (true)
            {
                Console.Write("Прикрыться щитом от стрел? (1 - да, 2 - нет): ");
                string shieldChoice = Console.ReadLine();

                if (shieldChoice == "1")
                {
                    Console.WriteLine("Богатырь успевает закрыться щитом. Стрелы не нанесли вреда.\n");
                    break;
                }
                else if (shieldChoice == "2")
                {
                    Console.WriteLine("Богатырь решает рискнуть без щита, и стрела вонзается в плечо (-15 HP).\n");
                    heroCurrentHP -= 15;
                    break;
                }
                else
                {
                    Console.WriteLine("Неверный выбор. Лучники целятся, решайте скорее!");
                }
            }
        }
        else
        {
            Console.WriteLine("Щит не найден! Ситуация критическая, богатырь ранен стрелой (-15 HP).\n");
            heroCurrentHP -= 15;
        }

        Console.WriteLine("Начинается рукопашная сеча!");
        Console.WriteLine("Нажмите любую клавишу для продвижения по раундам боя...");
        Console.ReadKey();
        Console.Clear();

        //  Бой 
        bool heroDefeated = false;

        for (int r = 0; r < 5; r++)
        {
            Console.WriteLine($"\n--- Раунд {r + 1} ---");

            // В первых 3 раундах - уникальные враги, в 4 и 5 - остатки орды
            string currentEnemyName = r < 3 ? enemyRank[r] : "Остатки вражеского арьергарда";
            int currentEnemyHP = r < 3 ? enemyHP[r] : randomGenerator.Next(30, 60);
            int enemyFuryBonus = r < 3 ? enemyFury[r] / 10 : 0; // ярость усиливает удар врага

            Console.WriteLine($"Перед вами враг: {currentEnemyName} | Здоровье врага: [{currentEnemyHP} HP]");
            // Бой с врагом идёт, пока кто то не помрёт
            while (currentEnemyHP > 0 && heroCurrentHP > 0)
            {
                bool shieldRaised = false; // щит действует только на один ответный удар врага
                bool turnDone = false;

                // Ход богатыря
                while (!turnDone)
                {
                    Console.WriteLine($"\nЗдоровье Богатыря: {heroCurrentHP} / {heroMaxHP} HP | Здоровье врага: {currentEnemyHP} HP | Горбушек хлеба: {breadCount}");
                    Console.WriteLine("Выберите действие богатыря:");
                    Console.WriteLine("1. Нанести удар Булатным мечом (Ближний бой: 25-35 урона)");
                    Console.WriteLine("2. Выстрелить из лука калеными стрелами (Дальний бой: 17-26 урона)");
                    Console.WriteLine("3. Съесть горбушку хлеба (+25 HP)");
                    if (hasShield)
                    {
                        Console.WriteLine("4. Прикрыться щитом (урон врага вдвое меньше, но только на этот ход)");
                    }

                    Console.Write(hasShield ? "Ваш выбор (1-4): " : "Ваш выбор (1-3): ");
                    string choice = Console.ReadLine();
                    int damageToEnemy;

                    if (choice == "1")
                    {
                        damageToEnemy = randomGenerator.Next(25, 36);
                        Console.WriteLine($"Богатырь заносит Булатный меч и наносит урон: {damageToEnemy}");
                        currentEnemyHP -= damageToEnemy;
                        strikePowerLog[r] += damageToEnemy; // суммарная сила ударов за раунд
                        turnDone = true;
                    }
                    else if (choice == "2")
                    {
                        damageToEnemy = randomGenerator.Next(17, 27);
                        Console.WriteLine($"Богатырь спускает тетиву, каленые стрелы наносят урон: {damageToEnemy}");
                        currentEnemyHP -= damageToEnemy;
                        strikePowerLog[r] += damageToEnemy;
                        turnDone = true;
                    }
                    else if (choice == "3")
                    {
                        if (breadCount <= 0)
                        {
                            Console.WriteLine("Горбушек больше не осталось! Выберите другое действие.");
                        }
                        else if (heroCurrentHP >= heroMaxHP)
                        {
                            Console.WriteLine("У богатыря полное здоровье! Горбушка хлеба не используется.");
                        }
                        else
                        {
                            heroCurrentHP += 25;
                            if (heroCurrentHP > heroMaxHP) heroCurrentHP = heroMaxHP;
                            breadCount--;
                            Console.WriteLine($"Богатырь съел горбушку хлеба и восстановил здоровье. Осталось горбушек: {breadCount}");
                            turnDone = true;
                        }
                    }
                    else if (choice == "4" && hasShield)
                    {
                        shieldRaised = true;
                        Console.WriteLine("Богатырь поднимает Кованый щит и готовится принять удар.");
                        turnDone = true;
                    }
                    else
                    {
                        Console.WriteLine("Неверный выбор. Сосредоточьтесь, супостат наступает!");
                    }
                }

                // Враг пал - ответной атаки нет
                if (currentEnemyHP <= 0)
                {
                    break;
                }

                // Ответка
                Console.WriteLine("\nВраг устоял на ногах и бежит в контратаку!");
                int enemyAttack = randomGenerator.Next(15, 36) + enemyFuryBonus;
                Console.WriteLine($"Базовый урон супостата: {enemyAttack}");
                if (shieldRaised)
                {
                    enemyAttack /= 2;
                    Console.WriteLine($"Кованый щит блокирует половину натиска! Полученный урон снижен до: {enemyAttack}");
                    shieldRaised = false; // щит опущен, действие закончилось
                }

                heroCurrentHP -= enemyAttack;

                if (heroCurrentHP <= 0)
                {
                    heroCurrentHP = 0;
                    Console.WriteLine("\nПОРАЖЕНИЕ! Враг ранил богатыря насмерть.");
                    heroDefeated = true;
                    break;
                }

                Console.WriteLine($"Враг нанес удар. У богатыря осталось {heroCurrentHP} HP.");
            }

            // Богатырь погиб 
            if (heroDefeated)
            {
                break;
            }

            // Враг повержен
            Console.WriteLine($"\nИсход раунда: ПОБЕДА! Враг {currentEnemyName} пал сраженным.");
            Console.WriteLine("Богатырь построил Шелковый шатер и отдохнул после битвы. Восстановлено +10 HP.");
            heroCurrentHP += 10;
            if (heroCurrentHP > heroMaxHP) heroCurrentHP = heroMaxHP;

            Console.WriteLine("\nНажмите любую клавишу для перехода к следующему этапу...");
            Console.ReadKey();
            Console.Clear();
        }

        // итоги и аналитика
        if (heroDefeated)
        {
            Console.WriteLine("====================================================");
            Console.WriteLine("БОГАТЫРЬ ПАЛ В БОЮ!");
            Console.WriteLine("Врагов оказалось слишком много, здоровье героя иссякло.");
            Console.WriteLine("====================================================\n");
        }
        else
        {
            Console.WriteLine("====================================================");
            Console.WriteLine("СЛАВНАЯ ПОБЕДА! Все рубежи княжества успешно защищены!");
            Console.WriteLine("Богатырь возвращается на заставу с триумфом.");
            Console.WriteLine("====================================================\n");
        }

        Console.WriteLine("Переходим к анализу результатов патрулирования территории.");

        //сумма урона вручную в цикле
        int totalDamage = 0;
        for (int i = 0; i < strikePowerLog.Length; i++)
        {
            totalDamage += strikePowerLog[i];
        }
        Console.WriteLine($"Суммарный богатырский урон за все раунды: {totalDamage} единиц.");

        //максимум верст вручную в цикле
        int maxVersts = verstsCovered[0];
        for (int i = 1; i < verstsCovered.Length; i++)
        {
            if (verstsCovered[i] > maxVersts)
            {
                maxVersts = verstsCovered[i];
            }
        }
        Console.WriteLine($"Самый дальний выезд богатыря (максимум верст): {maxVersts} верст.");

        //сортировка лога ударов через Array.Sort
        Array.Sort(strikePowerLog);
        Console.Write("Список ударов после сортировки (по возрастанию силы): ");
        foreach (int strike in strikePowerLog)
        {
            Console.Write($"{strike} ");
        }
        Console.WriteLine();

        //разворот массива верст через Array.Reverse
        Console.Write("Прямой путь по верстам: ");
        foreach (int v in verstsCovered)
        {
            Console.Write($"{v} ");
        }
        Console.WriteLine();

        Array.Reverse(verstsCovered);

        Console.Write("Обратный путь по верстам (массив развернут): ");
        foreach (int verst in verstsCovered)
        {
            Console.Write($"{verst} ");
        }
        Console.WriteLine("\n");
        // Сводка по вражеским силам
        int totalEnemyHP = 0;
        int totalEnemyWarriors = 0;
        for (int i = 0; i < enemyHP.Length; i++)
        {
            totalEnemyHP += enemyHP[i];
            totalEnemyWarriors += enemyWarriors[i];
        }
        Console.WriteLine($"Общая жизненная сила нападавших супостатов: {totalEnemyHP} HP.");
        Console.WriteLine($"Общее число воинов во вражеских сотнях: {totalEnemyWarriors}.");

        Console.WriteLine("\nВсе запланированные действия программы успешно выполнены.");
        Console.WriteLine("Работа с одномерными массивами завершена.");
        Console.WriteLine("Нажмите любую клавишу для выхода из программы...");
        Console.ReadKey();
    }
}
```
---

<picture>
  <img src="https://github.com/v1skarr/practic-5/blob/main/ConsoleRPG2/1.png">
</picture>
<picture>
  <img src="https://github.com/v1skarr/practic-5/blob/main/ConsoleRPG2/2.png">
</picture>
<picture>
  <img src="https://github.com/v1skarr/practic-5/blob/main/ConsoleRPG2/3.png">
</picture>
<picture>
  <img src="https://github.com/v1skarr/practic-5/blob/main/ConsoleRPG2/4.png">
</picture>
<picture>
  <img src="https://github.com/v1skarr/practic-5/blob/main/ConsoleRPG2/5.png">
</picture>
<picture>
  <img src="https://github.com/v1skarr/practic-5/blob/main/ConsoleRPG2/6.png">
</picture>
<picture>
  <img src="https://github.com/v1skarr/practic-5/blob/main/ConsoleRPG2/7.png">
</picture>
<picture>
  <img src="https://github.com/v1skarr/practic-5/blob/main/ConsoleRPG2/8.png">
</picture>
<picture>
  <img src="https://github.com/v1skarr/practic-5/blob/main/ConsoleRPG2/9.png">
</picture>
<picture>
  <img src="https://github.com/v1skarr/practic-5/blob/main/ConsoleRPG2/10.png">
</picture>
