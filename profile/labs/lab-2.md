# Все еще разминка
## Задание
содержимое `Lab2.hs` для  `/lab2/app/Lab2.hs`. Комментарии над функциями можно стереть
```haskell
module Lab2 where

import Prelude hiding (length, sum, product, reverse, repeat, zip, zipWith, concat)

-- Написать функцию, которая считает кол-во элементов списка
len :: [a] -> Int
len = undefined

-- Написать полиморфную функцию, которая создаёт список состоящий из n элементов, при этом каждый элемент равен x
-- Напишите сигнатуру функции
repeatN x n = undefined

-- Написать функцию, которая переворачивает список
reverse :: [a] -> [a]
reverse = undefined

-- Написать функцию, которая проверяет, является ли строка палиндромом. 
isPalindrome :: String -> Bool
isPalindrome = undefined

-- Написать функцию, которая принимает на вход два списка и строит список пар.
-- Правило образования пары: k-ый элемент из первого списка, k-ый элемент из второго списка объединяются в пару. 
-- Кол-во элементов в результирующем списке должно совпадать с кол-ом элементов самого короткого списка
zip :: [a] -> [b] -> [(a, b)]
zip = undefined

-- Написать функцию, которая склеивает список списков в единый список. ["ab", "cd"] -> "abcd"
flatten :: [[a]] -> [a]
flatten = undefined


-- Написать функцию, которая считает среднее значение элементов списка. Реализовать через свертку и за один проход по списку.
avgList :: [Double] -> Double
avgList = undefined

-- Написать полиморфную функцию, которая делит список на два по условию. Первый список должен содержать элементы, для которых предикат = True. Второй - для которых False.
-- Написать сигнатуру функции
splitFilter predicate list = undefined

-- Да, это quicksort, который нужно реализовать на haskell
qsort :: [Int] -> [Int]
qsort = undefined

-- Написать функцию, которая находит все положительные делители переданного числа
divisors :: Int -> [Int]
divisors = undefined

-- Написать функцию, которая находит все уникальные пифагоровы тройки, в которой каждый элемент меньше либо равен n. Перестановки в рамках одной тройки не считаются уникальными.
pythagoras :: Int -> [(Int, Int, Int)]
pythagoras = undefined
```

## Пояснения
Нужно заменить `undefined` на рабочие определения.  У некоторых функций отсутствует сигнатура, её нужно объявить. Если вы уже смешарик, то можете заменить сигнатуры функций `isPalidnrome` и `qsort` на более общие, сделав функции полиморфными (если не знаете как, то это нормально - узнаете на лекции № 4).

* Считать, что все функции работают с конечными списками, если явно не указано обратное
* `repeatN` должна работать с отрицательными `n`, возвращая при этом пустой список
* `isPalindrome` должен производить сравнение с учетом регистра. Пустой список считается палиндромом
* Размеры списка до `qsort` и после должны совпадать
* `divisors` возвращает делители в порядке возрастания
* Пифагоровы тройки `(3, 4, 5)` и `(6, 8, 10)` считаются двумя уникальными тройками
* `splitFilter` возвращает пару (aka кортеж), состоящую из двух списков

Старайтесь не допускать ситуации, когда функция является [частичной](https://wiki.haskell.org/Partial_functions)

## Тесты для самопроверки
> Directed by PaKicek

Содержимое файла `lab2/test/Main.hs`
```
{-# OPTIONS_GHC -Wno-type-defaults #-}

module Main (main) where

import Lab2
import Prelude hiding (length, sum, product, reverse, repeat, zip, zipWith, concat)

main :: IO ()
main = do
  mapM_ (uncurry check) tests
  putStrLn "All tests passed"

check :: String -> Bool -> IO ()
check name ok
  | ok = putStrLn $ "[ok] " ++ name
  | otherwise = error $ "[NOT OK] " ++ name

tests :: [(String, Bool)]
tests =
  [ ("len []", len ([] :: [Int]) == 0)
  , ("len [42]", len [42] == 1)
  , ("len [1, 2, 3, 4]", len [1, 2, 3, 4] == 4)
  , ("len \"hello\"", len "hello" == 5)
  , ("len [True, False, True]", len [True, False, True] == 3)
  , ("repeatN 0 5", repeatN 0 5 == [0, 0, 0, 0, 0])
  , ("repeatN 2 0", repeatN 2 0 == [])
  , ("repeatN 1 (-3)", repeatN 1 (-3) == [])
  , ("repeatN 'x' 3", repeatN 'x' 3 == "xxx")
  , ("repeatN True 2", repeatN True 2 == [True, True])
  , ("reverse []", reverse [] == ([] :: [Int]))
  , ("reverse [1]", reverse [1] == [1])
  , ("reverse [1, 2, 3]", reverse [1, 2, 3] == [3, 2, 1])
  , ("reverse \"hello\"", reverse "hello" == "olleh")
  , ("reverse [True, False]", reverse [True, False] == [False, True])
  , ("isPalindrome \"\"", isPalindrome "" == True)
  , ("isPalindrome \"a\"", isPalindrome "a" == True)
  , ("isPalindrome \"aba\"", isPalindrome "aba" == True)
  , ("isPalindrome \"abba\"", isPalindrome "abba" == True)
  , ("isPalindrome \"abc\"", isPalindrome "abc" == False)
  , ("isPalindrome \"Aba\"", isPalindrome "Aba" == False)
  , ("zip [] []", zip [] [] == ([] :: [(Int, Int)]))
  , ("zip [] [1, 2]", zip [] [1, 2] == ([] :: [(Int, Int)]))
  , ("zip [1, 2] []", zip [1, 2] [] == ([] :: [(Int, Int)]))
  , ("zip [1, 2, 3] ['a', 'b', 'c']", zip [1, 2, 3] ['a', 'b', 'c'] == [(1, 'a'), (2, 'b'), (3, 'c')])
  , ("zip [1, 2, 3] \"abc\"", zip [1, 2, 3] "abc" == [(1, 'a'), (2, 'b'), (3, 'c')])
  , ("zip [1, 2, 3] \"ab\"", zip [1, 2, 3] "ab" == [(1, 'a'), (2, 'b')])
  , ("zip \"ab\" [1, 2, 3]", zip "ab" [1, 2, 3] == [('a', 1), ('b', 2)])
  , ("zip [True, False] [1, 2]", zip [True, False] [1, 2] == [(True, 1), (False, 2)])
  , ("flatten []", flatten [] == ([] :: [Int]))
  , ("flatten [[]]", flatten [[]] == ([] :: [Int]))
  , ("flatten [[1, 2, 3]]", flatten [[1, 2, 3]] == [1, 2, 3])
  , ("flatten [[1], [2], [3]]", flatten [[1], [2], [3]] == [1, 2, 3])
  , ("flatten [[1, 2], [3, 4, 5], [6]]", flatten [[1, 2], [3, 4, 5], [6]] == [1, 2, 3, 4, 5, 6])
  , ("flatten [\"ab\", \"cd\"]", flatten ["ab", "cd"] == "abcd")
  , ("flatten [\"\", \"ab\", \"\"]", flatten ["", "ab", ""] == "ab")
  , ("flatten [[True], [False, True]]", flatten [[True], [False, True]] == [True, False, True])
  , ("avgList []", avgList [] == 0) 
  , ("avgList [5]", avgList [5] == 5)
  , ("avgList [1, 2, 3, 4]", abs (avgList [1, 2, 3, 4] - 2.5) < 1e-9)
  , ("avgList [1, 1, 1]", abs (avgList [1, 1, 1] - 1) < 1e-9)
  , ("avgList [0, 0, 0, 0]", avgList [0, 0, 0, 0] == 0)
  , ("avgList [-1, -2, -3]", abs (avgList [-1, -2, -3] + 2) < 1e-9)
  , ("avgList [1.5, 2.5]", abs (avgList [1.5, 2.5] - 2.0) < 1e-9)
  , ("splitFilter even []", splitFilter even [] == ([] :: [Int], []))
  , ("splitFilter even [1, 2, 3, 4, 5]", splitFilter even [1, 2, 3, 4, 5] == ([2, 4], [1, 3, 5]))
  , ("splitFilter even [2, 4, 6]", splitFilter even [2, 4, 6] == ([2, 4, 6], []))
  , ("splitFilter even [1, 3, 5]", splitFilter even [1, 3, 5] == ([], [1, 3, 5]))
  , ("splitFilter (> 0) [-1, 0, 1, 2, -3]", splitFilter (> 0) [-1, 0, 1, 2, -3] == ([1, 2], [-1, 0, -3]))
  , ("splitFilter id [True, False, True]", splitFilter id [True, False, True] == ([True, True], [False]))
  , ("qsort []", qsort [] == [])
  , ("qsort [1]", qsort [1] == [1])
  , ("qsort [2, 1]", qsort [2, 1] == [1, 2])
  , ("qsort [3, 1, 2]", qsort [3, 1, 2] == [1, 2, 3])
  , ("qsort [1, 2, 3, 4, 5]", qsort [1, 2, 3, 4, 5] == [1, 2, 3, 4, 5])
  , ("qsort [5, 4, 3, 2, 1]", qsort [5, 4, 3, 2, 1] == [1, 2, 3, 4, 5])
  , ("qsort [3, 3, 3, 1, 2]", qsort [3, 3, 3, 1, 2] == [1, 2, 3, 3, 3])
  , ("qsort [3, 1, 4, 1, 5, 9, 2, 6]", qsort [3, 1, 4, 1, 5, 9, 2, 6] == [1, 1, 2, 3, 4, 5, 6, 9])
  , ("qsort [0, -1, 5, -3, 2]", qsort [0, -1, 5, -3, 2] == [-3, -1, 0, 2, 5])
  , ("qsort [5, 5, 5, 5]", qsort [5, 5, 5, 5] == [5, 5, 5, 5])
  , ("divisors 1", divisors 1 == [1])
  , ("divisors 2", divisors 2 == [1, 2])
  , ("divisors 12", divisors 12 == [1, 2, 3, 4, 6, 12])
  , ("divisors 13", divisors 13 == [1, 13])
  , ("divisors 16", divisors 16 == [1, 2, 4, 8, 16])
  , ("divisors 36", divisors 36 == [1, 2, 3, 4, 6, 9, 12, 18, 36])
  , ("divisors 100", divisors 100 == [1, 2, 4, 5, 10, 20, 25, 50, 100])
  , ("divisors (-1)", divisors (-1) == [1])
  , ("divisors (-2)", divisors (-2) == [1, 2])
  , ("divisors (-12)", divisors (-12) == [1, 2, 3, 4, 6, 12])
  , ("divisors (-13)", divisors (-13) == [1, 13])
  , ("divisors (-16)", divisors (-16) == [1, 2, 4, 8, 16])
  , ("divisors (-36)", divisors (-36) == [1, 2, 3, 4, 6, 9, 12, 18, 36])
  , ("divisors (-100)", divisors (-100) == [1, 2, 4, 5, 10, 20, 25, 50, 100])
  , ("pythagoras 0", pythagoras 0 == [])
  , ("pythagoras 4", pythagoras 4 == [])
  , ("pythagoras 5", pythagoras 5 == [(3, 4, 5)])
  , ("pythagoras 10", pythagoras 10 == [(3, 4, 5), (6, 8, 10)])
  , ("pythagoras 13", pythagoras 13 == [(3, 4, 5), (5, 12, 13), (6, 8, 10)])
  , ("pythagoras 15", pythagoras 15 == [(3, 4, 5), (5, 12, 13), (6, 8, 10), (9, 12, 15)])
  , ("pythagoras 17", pythagoras 17 == [(3, 4, 5), (5, 12, 13), (6, 8, 10), (8, 15, 17), (9, 12, 15)])
  ]
```  
Запуск:
```bash
cd lab2
cabal test
```
