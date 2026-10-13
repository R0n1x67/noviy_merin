# Практическая работа №6 Практическая работа №6: Двумерные прямоугольные массивы (матрицы) в C# и генерация игровых локаций
# Разработчики: Гильметднов Роман, Бабыкин Никита
## Парковка

```csharp
using System;

class Program
{
    static void Main()
    {
        // 1. Создать матрицу городской застройки char[,] city размером 6х6
        int rows = 6;
        int cols = 6;
        char[,] city = new char[rows, cols];

        // 2. Заполнить центральные полосы дорогой ., остальные — зданиями #
        for (int i = 0; i < city.GetLength(0); i++)
        {
            for (int j = 0; j < city.GetLength(1); j++)
            {
                if (i == 2 || j == 2)
                {
                    city[i, j] = '.';
                }
                else
                {
                    city[i, j] = '#';
                }
            }
        }

        // 3. Создать параллельную матрицу плотности трафика trafficIndex от 1 до 10 баллов
        int[,] trafficIndex = new int[rows, cols];
        Random rand = new Random();
        for (int i = 0; i < trafficIndex.GetLength(0); i++)
        {
            for (int j = 0; j < trafficIndex.GetLength(1); j++)
            {
                // Заполняем баллами только там, где есть дороги для реалистичности
                if (city[i, j] == '.')
                {
                    trafficIndex[i, j] = rand.Next(1, 11); // от 1 до 10 включительно
                }
                else
                {
                    trafficIndex[i, j] = 0; // На месте зданий трафика нет
                }
            }
        }

        // 4. Разместить 3 парковочных места P вдоль дорожного полотна
        // Разместим их в фиксированных или свободных дорожных клетках
        if (city.GetLength(0) > 4 && city.GetLength(1) > 4)
        {
            city[2, 1] = 'P';
            city[2, 4] = 'P';
            city[4, 2] = 'P';
        }

        // 5. Вывести карту города, выделив парковки P синим цветом
        Console.WriteLine("--- Карта города (Р - парковка синим) ---");
        for (int i = 0; i < city.GetLength(0); i++)
        {
            for (int j = 0; j < city.GetLength(1); j++)
            {
                if (city[i, j] == 'P')
                {
                    Console.ForegroundColor = ConsoleColor.Blue;
                    Console.Write(city[i, j] + " ");
                    Console.ResetColor();
                }
                else
                {
                    Console.Write(city[i, j] + " ");
                }
            }
            Console.WriteLine();
        }
        Console.WriteLine();

        // Вывод матрицы трафика для наглядности выполнения аналитического блока
        Console.WriteLine("--- Матрица плотности трафика (баллы) ---");
        for (int i = 0; i < trafficIndex.GetLength(0); i++)
        {
            for (int j = 0; j < trafficIndex.GetLength(1); j++)
            {
                Console.Write($"{trafficIndex[i, j],3}");
            }
            Console.WriteLine();
        }
        Console.WriteLine();

        // 6. Вычислить среднюю загруженность дорог на горизонтальной улице
        double sumTrafficRow2 = 0;
        int roadCellsCount = 0;
        for (int j = 0; j < trafficIndex.GetLength(1); j++)
        {
            // Учитываем только элементы, относящиеся к дороге/парковке
            sumTrafficRow2 += trafficIndex[2, j];
            roadCellsCount++;
        }
        double avgTraffic = sumTrafficRow2 / roadCellsCount;
        Console.WriteLine($"6. Средняя загруженность горизонтальной улицы (строка 2): {avgTraffic:F2} баллов");

        // 7. Найти самый загруженный перекресток / участок дороги
        int maxTraffic = -1;
        int maxRow = -1, maxCol = -1;
        for (int i = 0; i < trafficIndex.GetLength(0); i++)
        {
            for (int j = 0; j < trafficIndex.GetLength(1); j++)
            {
                if (trafficIndex[i, j] > maxTraffic)
                {
                    maxTraffic = trafficIndex[i, j];
                    maxRow = i;
                    maxCol = j;
                }
            }
        }
        Console.WriteLine($"7. Самый загруженный дорожный участок: [{maxRow}, {maxCol}] с баллом {maxTraffic}");

        // 8. Подсчитать число свободных парковочных мест P
        int pCount = 0;
        for (int i = 0; i < city.GetLength(0); i++)
        {
            for (int j = 0; j < city.GetLength(1); j++)
            {
                if (city[i, j] == 'P')
                {
                    pCount++;
                }
            }
        }
        Console.WriteLine($"8. Число свободных парковочных мест: {pCount}");
        Console.WriteLine();

        // 9. Принять ввод координат автомобиля C с безопасной проверкой границ
        Console.WriteLine("9. Введите координаты для размещения автомобиля 'C':");
        Console.Write("Введите номер строки (0-5): ");
        int x = int.Parse(Console.ReadLine());
        Console.Write("Введите номер столбца (0-5): ");
        int y = int.Parse(Console.ReadLine());

        // Безопасность индексов
        if (x >= 0 && x < city.GetLength(0) && y >= 0 && y < city.GetLength(1))
        {
            if (city[x, y] == '#')
            {
                Console.WriteLine("Ошибка: В указанной клетке находится здание! Проезд невозможен.");
            }
            else
            {
                city[x, y] = 'C';
                Console.WriteLine("Автомобиль успешно размещен на дороге.");
            }
        }
        else
        {
            Console.WriteLine("Ошибка: Выход за пределы карты города!");
        }
        Console.WriteLine();

        // 10. Заполнить главную диагональ дорожной разметкой /
        for (int i = 0; i < city.GetLength(0); i++)
        {
            // Перезаписываем только если там не стоит наш автомобиль C
            if (city[i, i] != 'C')
            {
                city[i, i] = '/';
            }
        }

        // Финальный вывод обновленной карты
        Console.WriteLine("--- Финальная карта города (с разметкой '/' и машиной 'C') ---");
        for (int i = 0; i < city.GetLength(0); i++)
        {
            for (int j = 0; j < city.GetLength(1); j++)
            {
                if (city[i, j] == 'C')
                {
                    Console.ForegroundColor = ConsoleColor.Red; // Выделим машину красным
                    Console.Write(city[i, j] + " ");
                    Console.ResetColor();
                }
                else if (city[i, j] == 'P')
                {
                    Console.ForegroundColor = ConsoleColor.Blue;
                    Console.Write(city[i, j] + " ");
                    Console.ResetColor();
                }
                else
                {
                    Console.Write(city[i, j] + " ");
                }
            }
            Console.WriteLine();
        }
    }
}
```
---

<picture>
  <img src="https://github.com/R0n1x67/noviy_merin/blob/main/%D0%BF%D0%B0%D1%80%D0%BA%D0%BE%D0%B2%D0%BA%D0%B0/1.jpg">
</picture>
<picture>
  <img src="https://github.com/R0n1x67/noviy_merin/blob/main/%D0%BF%D0%B0%D1%80%D0%BA%D0%BE%D0%B2%D0%BA%D0%B0/2.jpg">
</picture>
<picture>
  <img src="https://github.com/R0n1x67/noviy_merin/blob/main/%D0%BF%D0%B0%D1%80%D0%BA%D0%BE%D0%B2%D0%BA%D0%B0/3.jpg">
</picture>
<picture>
  <img src="https://github.com/R0n1x67/noviy_merin/blob/main/%D0%BF%D0%B0%D1%80%D0%BA%D0%BE%D0%B2%D0%BA%D0%B0/4.jpg">
</picture>
<picture>
  <img src="https://github.com/R0n1x67/noviy_merin/blob/main/%D0%BF%D0%B0%D1%80%D0%BA%D0%BE%D0%B2%D0%BA%D0%B0/5.jpg">
</picture>
<picture>
  <img src="https://github.com/R0n1x67/noviy_merin/blob/main/%D0%BF%D0%B0%D1%80%D0%BA%D0%BE%D0%B2%D0%BA%D0%B0/6.jpg">
</picture>
