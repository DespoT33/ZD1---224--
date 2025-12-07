using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;

namespace SortingTask_Var11
{
    class Program
    {
        static void Main(string[] args)
        {
            // 1. Чтение из файла
            string[] lines = File.ReadAllLines("input.txt");
            List<int> allNumbers = new List<int>();

            foreach (var line in lines)
            {
                if (int.TryParse(line.Trim(), out int num))
                {
                    allNumbers.Add(num);
                }
            }

            Console.WriteLine("Исходные числа:");
            Console.WriteLine(string.Join(", ", allNumbers));

            // 2. Фильтрация: только отрицательные, состоящие из чётных цифр
            List<int> filtered = allNumbers
                .Where(IsNegativeWithOnlyEvenDigits)
                .ToList();

            Console.WriteLine("\nОтфильтрованные числа:");
            if (filtered.Count == 0)
            {
                Console.WriteLine("Нет подходящих чисел.");
                return;
            }
            Console.WriteLine(string.Join(", ", filtered));

            // 3. Сортировка вставками (по возрастанию) + подсчёт
            List<int> listForInsertion = new List<int>(filtered);
            var (sortedAsc, comparisons, swaps) = InsertionSortWithCount(listForInsertion);

            Console.WriteLine($"\nПосле сортировки ВСТАВКАМИ (по возрастанию):");
            Console.WriteLine(string.Join(", ", sortedAsc));
            Console.WriteLine($"Сравнений: {comparisons}, Перестановок: {swaps}");

            // 4. Пирамидальная сортировка (по убыванию)
            List<int> listForHeap = new List<int>(filtered);
            HeapSortDescending(listForHeap);

            Console.WriteLine("\nПосле ПИРАМИДАЛЬНОЙ сортировки (по убыванию):");
            Console.WriteLine(string.Join(", ", listForHeap));

            // 5. Теоретическая оценка для Heapsort
            int n = filtered.Count;
            Console.WriteLine($"\nТеоретическая оценка пирамидальной сортировки (худший случай):");
            Console.WriteLine($"Сравнений: ~ {2 * n * (int)Math.Log(n, 2)} (O(n log n))");
            Console.WriteLine($"Перестановок: ~ {n * (int)Math.Log(n, 2)} (O(n log n))");

            Console.WriteLine("\nГотово. Нажмите любую клавишу...");
            Console.ReadKey();
        }

        // Проверка: отрицательное число, состоящее только из чётных цифр
        static bool IsNegativeWithOnlyEvenDigits(int number)
        {
            if (number >= 0) return false;
            string digits = Math.Abs(number).ToString();
            foreach (char c in digits)
            {
                if (c != '0' && c != '2' && c != '4' && c != '6' && c != '8')
                    return false;
            }
            return true;
        }

        // Сортировка вставками с подсчётом
        static (List<int> sorted, long comparisons, long swaps) InsertionSortWithCount(List<int> list)
        {
            List<int> arr = new List<int>(list);
            long comparisons = 0;
            long swaps = 0;
            int n = arr.Count;

            for (int i = 1; i < n; i++)
            {
                int key = arr[i];
                swaps++; // считаем как присваивание ключа
                int j = i - 1;

                while (j >= 0)
                {
                    comparisons++;
                    if (arr[j] > key)
                    {
                        arr[j + 1] = arr[j];
                        swaps++;
                        j--;
                    }
                    else break;
                }
                arr[j + 1] = key;
            }

            return (arr, comparisons, swaps);
        }

        // Пирамидальная сортировка (по убыванию)
        static void HeapSortDescending(List<int> arr)
        {
            int n = arr.Count;

            // Построение кучи (макс-куча → для убывания делаем мин-кучу)
            for (int i = n / 2 - 1; i >= 0; i--)
                MinHeapify(arr, n, i);

            // Извлечение элементов из кучи
            for (int i = n - 1; i > 0; i--)
            {
                // Переместить корень в конец
                (arr[0], arr[i]) = (arr[i], arr[0]);
                MinHeapify(arr, i, 0);
            }
        }

        static void MinHeapify(List<int> arr, int heapSize, int rootIndex)
        {
            int smallest = rootIndex;
            int left = 2 * rootIndex + 1;
            int right = 2 * rootIndex + 2;

            if (left < heapSize && arr[left] < arr[smallest])
                smallest = left;

            if (right < heapSize && arr[right] < arr[smallest])
                smallest = right;

            if (smallest != rootIndex)
            {
                (arr[rootIndex], arr[smallest]) = (arr[smallest], arr[rootIndex]);
                MinHeapify(arr, heapSize, smallest);
            }
        }
    }
}
