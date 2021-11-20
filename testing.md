# Тестирование

Суть тестирования - поиск ошибок в работе ПО. Важно понимать разницу между тестами, которые **стремятся выявить ошибки** и тестами, которые **демонстрируют отсутствие ошибок**.

С помощью тестов **невозможно** обнаружить все возможные ошибки.

**Тестирование методом "черного ящика"** - метод, основанный на работе исключительно с внешними интерфейсами тестируемой системы. Мы не имеем представления о структуре и внутреннем устройстве системы. Нужно концентрироваться на том, что программа делает, а не на том, как она это делает.

**Тестирование методом "белого ящика"** - метод, который предполагает, что внутреннее устройство системы известно тестировщику. Мы выбираем входные значения, основываясь на знании кода, который будет их обрабатывать. Точно так же мы знаем, каким должен быть результат этой обработки.