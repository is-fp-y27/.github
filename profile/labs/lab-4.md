# Статистика

Коллеги, которым нечего делать решили сделать нагрузочное тестирование, но не смогли проанализировать полученный
результат. Необходимо помочь коллегам, при этом доставив им страдания посредством решения проблемы при помощи хаскеля и
концепций из теории категорий.

Каждый запрос в рамках прошедшего тестирования будем называть `замер` или `Sample`. `замер` содержит путь запроса, его
источник, базовую задержку и кол-во повторных попыток до успешного выполнения.
Отдельно существуют готовые сводки (инциденты), которые описывают сбой. Содержит путь запроса, источник и кол-во сбоев.

## Каркас

содержимое `Lab4.hs` для  `/lab4/app/Lab4.hs`. Комментарии над функциями можно стереть

```haskell
module Lab4 where

-- Миллисекунды
newtype Millis = Millis Int
  deriving (Eq, Ord, Show, Bounded)

-- Будущий моноид для подсчёта общего времени всех замеров
newtype Total = Total Millis
  deriving (Eq, Show)

-- Будущий моноид для поиска самого быстрого замера
newtype Fastest = Fastest Millis
  deriving (Eq, Show)

newtype Count = Count Int
  deriving (Eq, Show)

-- Источник запроса: браузер; телефон; скрипт
data Origin = Browser | Phone | Script
  deriving (Eq, Show)

-- Замер по некоторому запросу
data Sample = Sample
  { sampleRoute :: String
  , sampleOrigin :: Origin
  , sampleMillis :: Millis
  , sampleRetries :: Int
  } deriving (Eq, Show)

data Incident = Incident
  { incidentRoute :: String
  , incidentOrigin :: Origin
  , incidentHits :: Int -- кол-во сбоев
  } deriving (Eq, Show)

data Stats = Stats
  { statsTotal :: Total
  , statsCount :: Count
  , statsFastest :: Maybe Fastest
  } deriving (Eq, Show)

-- Правило извелечения миллисекунд из замера
newtype Rule = Rule (Sample -> Maybe Millis)

-- Изменение исходного замера. Написать, чем Adjust отличается от Endo
newtype Adjust = Adjust (Sample -> Sample)

data Report a
  = Row a
  | Section String [Report a]
  deriving (Eq, Show)

instance Semigroup Total where
  (<>) = undefined

instance Monoid Total where
  mempty = undefined

instance Semigroup Fastest where
  (<>) = undefined

instance Monoid Fastest where
  mempty = undefined

instance Semigroup Count where
  (<>) = undefined

instance Monoid Count where
  mempty = undefined

instance Semigroup Stats where
  (<>) = undefined

instance Monoid Stats where
  mempty = undefined

instance Semigroup Rule where
  (<>) = undefined

instance Monoid Rule where
  mempty = undefined

instance Semigroup Adjust where
  (<>) = undefined

instance Monoid Adjust where
  mempty = undefined

instance Functor Report where
  fmap = undefined

-- Извлекает из Sample базовую задержку. + 25мс за каждый ретрай
latency :: Sample -> Millis
latency = undefined

-- Функция, которая ищет наиболее срочный инцидент. Чем больше сбоев, тем срочнее. При равном кол-ве сбоев взять инцидент, у которого `incidentRoute` больше 
urgent :: [Incident] -> Maybe Incident
urgent = undefined

-- Работает как urgent, но ищет только среди элементов с переданным Origin
urgentOn :: Origin -> [Incident] -> Maybe Incident
urgentOn = undefined

-- Переводит замер в статистику
fromSample :: Sample -> Stats
fromSample = undefined

-- Из множества замеров формирует общую статистику
summarize :: [Sample] -> Stats
summarize = undefined

-- Возвращает исходную latency
asMeasured :: Rule
asMeasured = undefined

-- Возвращает исходную latency, только если она больше чем limit
clipAbove :: Millis -> Rule
clipAbove limit = undefined

-- Возвращает исходную latency, только если она меньше чем limit
silenceUnder :: Millis -> Rule
silenceUnder limit = undefined

-- Применяет правило к переданному замеру
runRule :: Rule -> Sample -> Maybe Millis
runRule = undefined

-- Склеивает набор правил в одно и применяет его к замеру
watchAll :: [Rule] -> Sample -> Maybe Millis
watchAll = undefined

-- Изменяет только число ретраев замера. Делит кол-во ретраев на 2 (с округлением вниз)
trimRetries :: Adjust
trimRetries = undefined

-- Меняет источник на переданный
shiftOrigin :: Origin -> Adjust
shiftOrigin = undefined

-- Увеличивает число ретраев на 1, если origin замера = Phone
armPhone :: Adjust
armPhone = undefined

-- Применяет изменение к замеру
applyAdjust :: Adjust -> Sample -> Sample
applyAdjust = undefined

-- Обходит весь отчёт и склеивает содержимое всех строк в значение моноида. Заголовок игнорируется
foldReport :: Monoid m => (a -> m) -> Report a -> m
foldReport = undefined

-- Достает строки отчёта и складывает их в список
rows :: Report a -> [a]
rows = undefined

-- Складывает время всех замеров в отчёте и возвращает одну общую сумму
totals :: Report Sample -> Total
totals = undefined

-- Находит в отчёте замер, который имеет наименьшую задержку и возвращает эту задержку
fastestIn :: Report Sample -> Maybe Fastest
fastestIn = undefined

{-
В качестве второго аргумента принимает функцию, которая позволяет получить из замера миллисекунды.
В качестве первого аргумента принимает функцию, которая добавляет дополнительное преобразование времени.
-}
overTime :: (Millis -> Millis) -> (Sample -> Millis) -> (Sample -> Millis)
overTime = undefined
```

## Примечания

* На вход `latency`, правил и правок не приходят отрицательная базовая задержка и отрицательное число повторов

* Если внутри `Rule` находится `Nothing` - правило игнорируется и не влияет на последующие правила. Склейка двух `Rule`
  возвращает `Just` с меньшей задержкой.

* Правки применяются слева направо:

```haskell
applyAdjust (e1 <> e2) s == applyAdjust e2 (applyAdjust e1 s)
```

* `rows`, `totals` и `fastestIn` необходимо реализовать через `foldReport`.
* Не использовать `Sum`, `Product`, `Min`, `Max`, `First`, `Last`, `Endo`, `All`, `Any`
* Функции не должны быть частичными
    * Функции определены на всех значениях, которые описаны в этой работе, включая пустые списки, пустые секции и
      нулевую задержку.

## Законы

* Требуется доказать, что каждой реализованной полугруппой выполняется:
    * ассоциативность на трёх значениях: `(x <> y) <> z == x <> (y <> z)`
* Требуется доказать. что для каждого реализованного моноида выполняется:
    - `mempty <> x == x` и `x <> mempty == x`

- Требуется доказать, что для каждого реализованного функтора выполняется:
    - `fmap id container == id container`
    - `fmap (f . g) container == (fmap f . fmap g) container`

Доказать = написать тестовый сценарий, проверяющий правило/закон

* Для `Rule` и `Adjust` разрешается проверка с использованием конкретного замера

## Тесты

* Помимо проверки законов, требуется написать минимальный набор тестов на каждую функцию
* Критериев к стеку тестов нет, выбор за вами (аналогично 3-ей лабе)


