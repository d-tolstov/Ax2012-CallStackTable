# X++ call stack table for Ax2012
## Зачем это всё
Со стеком вызовов обычно работают как с контейнером или как с текстом. Сохраняют его в каких-нибудь логах для определения места, в котором возникла ошибка или ещё какое-либо действие. Это приводит к чрезмерному расходованию места в логах и к неудобному поиску логов в разрезе стека вызовов.
## Что предлагается
Стек вызовов в логах - это очень повторяющаяся вещь. К одному стеку обычно относится очень много логов. Поэтому предлагается создать справочник стеков вызовов, а в логах сохранять ссылку RecId на этот справочник. Это сильно сэкономит место в логах, позволит в логах создать индекс по стеку вызовов. Собирать статистику станет намного удобнее. И не только по стеку вызовов целиком, но и по любой строке из стека (так как каждая строка в стеке - это отдельная запись в таблице).
## ER-модель
<img src="pictures\er-model.png" alt="схема">

## Как использовать
Для получения ссылки на стек вызовов необходимо вызвать :  
`CallStackTable_CDT::findOrCreate(xSession::xppCallStack()).RecId`  

Чаще всего полный стек не нужен, поэтому обычно у `xSession::xppCallStack()` отсекают первые несколько элементов и передают в функцию.
## Формы для просмотра
### Стек вызовов
<img src="pictures\form-CallStackTable.png" alt="схема">

### Строки стеков вызовов
<img src="pictures\form-CallStackLine.png" alt="схема">

## Особенности функционала
* Ранее созданные стеки вызовов ищутся по hash-коду, формируемому для текстового представления стека вызовов. У каждого стека свой hash-код. Он формируется по алгоритму MD5 с кодировкой UTF8.
* Для создания записей справочника стека вызовов используется отдельный UserConnection
* В xpo-файле нет каких-то таблиц с настройками для выключения\включения функционала, на каждом проекте обычно это реализуется по своему
* В xpo-файле нет меню и привилегий, так как это обычно индивидуально для каждого проекта
