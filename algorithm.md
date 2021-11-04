# Алгоритмы и структуры данных

## Алгоритмы

- бинарный поиск
- алгоритм Диффи-Хеллмана
- фильтры Блума
- алгоритм поиска k-ближайших соседей
- близость косинусов
- наивный классификатор Байеса
- HyperLogLog
- MapReduce
- преобразование Фурье ([статья](https://betterexplained.com/articles/an-interactive-guide-to-the-fourier-transform/))
- симплекс-метод
- алгоритм Дейкстры
- 
## Сложность алгоритма

**O** описывает лишь скорость роста, поэтому **O(2n)** сокращается до **O(n)**.

- **O(1)** — константа, не зависит от количества элементов
- **O(n)** — растет линейно в зависимости от количества элементов
- **O(log n)** — например, когда количество элементов последовательно делится на 2

## Структуры данных

- map (dictionary)
- hash-map (погуглить отличия от map)
- инвертированный индекс
- связный список
- стек
- очередь
- граф
- дерево
    - красно-черные деревья
    - splay-деревья
    - B-деревья
- куча

### Инвертированный индекс

Инвертированный индекс — структура данных, в которой для каждого слова коллекции документов в соответствующем списке перечислены все документы в коллекции, в которых оно встретилось. Инвертированный индекс используется для поиска по текстам.

Есть два варианта инвертированного индекса:

- индекс, содержащий только список документов для каждого слова
- индекс, дополнительно включающий позицию слова в каждом документе

Например, у нас есть 3 текста — **it is what it is**, **what is it** и **it is a banana**. Инвертированный индекс для них будет выглядеть так (цифры обозначают номера текстов):

```json
{
    "a": [2],
    "banana": [2],
    "is": [0, 1, 2],
    "it": [0, 1, 2],
    "what": [0, 1]
}
```

Если мы поищем по **what is it**, то получим $[0, 1] \cap [0, 1, 2] \cap [0, 1, 2] = [0, 1]$ ($\cap$ — пересечение множеств). Т.е. найдутся тексты 1 и 2.