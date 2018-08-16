# csv - чтение и запись csv файлов #
Исходный код: [Lib/csv.py](https://github.com/python/cpython/tree/3.7/Lib/csv.py)

Так называемый CSV (Comma Separated Values) формат файлов наиболее часто используется для импорта и экспорта электронных таблиц (табличных данных) и баз данных. Формат CSV в течение многих лет использовался до попытки стандартного описания формата в [RFC 4180](https://tools.ietf.org/html/rfc4180.html). Отсутствие четко определенного стандарта означает, что в данных, полученных и потребляемых различными приложениями, существуют различия. Эти различия могут вызвать проблемы при обработке CSV-файлов из нескольких различных источников. Тем не менее, в то время как символы разделителя и кавычек различны, общность формата достаточно подходит для того, что бы можно было написать один модуль, который может эффективно манипулировать такими данными, скрывая детали реализации чтения и записи данных от программиста. 

Модуль [csv](https://docs.python.org/3/library/csv.html#module-csv) реализует классы для чтения и записи табличных данных в формате CSV. Это позволяет программистам говорить «записать эти данные в формате, предпочитаемом Excel», или «читать данные из файла, который был создан в Excel», не зная специфических особенностей формата CSV, используемом в Excel. 

Объекты [reader](https://docs.python.org/3/library/csv.html#csv.reader) и [writer](https://docs.python.org/3/library/csv.html#csv.writer) модуля [csv](https://docs.python.org/3/library/csv.html#module-csv) читают и записывают последовательности данных. Разработчики также могут читать и записывать данные в виде словаря, используя классы [DictReader](https://docs.python.org/3/library/csv.html#csv.DictReader) и [DictWriter](https://docs.python.org/3/library/csv.html#csv.DictWriter). 

## Содержание модуля ##
В модуле CSV определены следующие функции:

csv.**reader**(*csvfile, dialect='excel', \*\*fmtparams*)   
Возвращает итерируемый объект **reader**, который будет перебирать строки в заданном *csvfile*. *csvfile* может быть любым объектом, который поддерживает протокол [iterator](https://docs.python.org/3/glossary.html#term-iterator) и возвращает строку каждый раз, когда вызывается его метод **\__next\__()**, также в качестве таких объектов могут выступать объекты файлов [file objects](https://docs.python.org/3/glossary.html#term-file-object) и списков. Если *csvfile* является объектом типа файл его следует открывать с использованием `newline='` [[1]](#).   Необязательный аргумент *dialect* используется для определения набора параметров, специфичных для конкретного диалекта CSV (например используемого в *excel*). Это может быть экземпляр подкласса, определенного от класса [Dialect](https://docs.python.org/3/library/csv.html#csv.Dialect) или одной из строк, возвращаемых функцией [list_dialects()](https://docs.python.org/3/library/csv.html#csv.list_dialects). Остальные необязательные аргументы позволяют переопределять отдельные параметры форматирования в текущем диалекте. Подробные сведения о диалектах и параметрах форматирования в них см. в разделе [ Dialects and Formatting Parameters](https://docs.python.org/3/library/csv.html#csv-fmt-params).

Каждая строка (*row*), считанная из CSV файла, возвращается как список строк (*string*). Никакое автоматическое преобразование типов данных не выполняется, если определена опция `QUOTE_NONNUMERIC`(в этом случае не упомянутые поля преобразуются в тип данных *float*).

Пример использования:   

	>>> import csv
	>>> with open('eggs.csv', newline='') as csvfile:
	...     spamreader = csv.reader(csvfile, delimiter=' ', quotechar='|')
	...     for row in spamreader:
	...         print(', '.join(row))
	Spam, Spam, Spam, Spam, Spam, Baked Beans
	Spam, Lovely Spam, Wonderful Spam

csv.**writer**(*csvfile, dialect='excel', \*\*fmtparams*)

Вернуть объект **writer**, ответственный за преобразование данных пользователя в строки с разделителями для указанного объекта типа файл (*csvfile*). *csvfile* может быть любым объектом с методом **write()**. Если *csvfile* является объектом типа файл его следует открывать с использованием `newline='` [[1]](#). Необязательный аргумент *dialect* используется для определения набора параметров, специфичных для конкретного диалекта CSV (например используемого в *excel*). Это может быть экземпляр подкласса, определенного от класса [Dialect](https://docs.python.org/3/library/csv.html#csv.Dialect) или одной из строк, возвращаемых функцией [list_dialects()](https://docs.python.org/3/library/csv.html#csv.list_dialects). Остальные необязательные аргументы позволяют переопределять отдельные параметры форматирования в текущем диалекте. Подробные сведения о диалектах и параметрах форматирования в них см. в разделе [ Dialects and Formatting Parameters](https://docs.python.org/3/library/csv.html#csv-fmt-params). Чтобы упростить взаимодействие с модулями, реализующими DB API, значение [None](https://docs.python.org/3/library/constants.html#None) записывается как пустая строка. Хотя это не является обратимым преобразованием, оно упрощает выгрузку SQL NULL дампа данных в CSV файлы без предварительной обработки, возвращаемых из вызова cursor.fetch*. Все остальные данные не являющиеся строками преобразуются в строки с помощью функции [str()](https://docs.python.org/3/library/stdtypes.html#str) перед записью.

Пример использования: 

	import csv
	with open('eggs.csv', 'w', newline='') as csvfile:
	    spamwriter = csv.writer(csvfile, delimiter=' ',
	                            quotechar='|', quoting=csv.QUOTE_MINIMAL)
	    spamwriter.writerow(['Spam'] * 5 + ['Baked Beans'])
	    spamwriter.writerow(['Spam', 'Lovely Spam', 'Wonderful Spam'])

csv.**register_dialect**(*name*[, *dialect*[, *\*\*fmtparams*]])  
Ассоциирует *диалект* со значением аргумента *name*. Значение *name* должно быть строкой.  