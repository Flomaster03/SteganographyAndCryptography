1_4

Описание
Программа стеганографии/криптографии универсальна: она может выполнять самые разные задачи в зависимости от пожеланий пользователя. Возможно, вы захотите скрыть сообщение внутри изображения, зашифровать текстовое сообщение для дополнительной безопасности или расшифровать скрытое сообщение, которое вы получили от кого-то другого.

На этом этапе вы создадите пользовательский интерфейс для своей программы. Пользователь сможет выбирать задачу, подавая команды через стандартный ввод.

Цели
Программа должна читать входные строки (команды) в цикле.

Ваша программа должна:

Распечатайте сообщение Task (hide, show, exit):и прочитайте стандартный ввод в цикле.

Если введено exit, распечатайте Bye!и выйдите.

Если на входе hide, выведите Hiding message in image.и вернитесь к циклу ввода.

Если на входе show, выведите Obtaining message from image.и вернитесь к циклу ввода.

Если введена какая-либо другая строка, выведите ее Wrong task: [input String]и вернитесь в цикл ввода.

Пример
Символ «больше», за которым следует пробел ( > ), представляет ввод пользователя. Обратите внимание, что это не часть ввода.

Пример: как должен работать пользовательский интерфейс.

Task (hide, show, exit):
> hide
Hiding message in image.
Task (hide, show, exit):
> show
Obtaining message from image.
Task (hide, show, exit):
> task
Wrong task: task
Task (hide, show, exit):
> exit
Bye!

2_4

Описание
Прежде чем мы сможем фактически скрыть сообщение в изображении, нам нужно научиться обращаться с изображениями. На этом этапе мы будем работать над командой hide.

Для наших целей изображение должно быть в формате без потерь. «Без потерь» означает, что значения пикселей не изменяются при сохранении и сжатии изображения. Мы будем работать с форматом изображений PNG . Другие известные форматы, такие как JPG, используют формат изображения со сжатием с потерями, что может повредить скрытое сообщение.

Изображения можно рассматривать как двумерные массивы. На рисунке ниже вы можете увидеть систему координат изображения:

координаты пикселей изображения

24-битное изображение предлагает более 16 миллионов цветов для каждого пикселя. Если используется цветовая схема RGB, то 8 бит (значения 0–255) представляют красный, зеленый и синий цвета. Если младшие биты этих 8-битных значений изменятся, разница в изображении останется незамеченной. Это именно то, что мы будем использовать для сокрытия нашего сообщения на следующих этапах. На этом этапе нашей программе необходимо установить значение младшего бита для каждого цвета каждого пикселя входного изображения 1.

24-битные цветовые биты RGB

Классы Java BufferedImage, ImageIOи Color( java.awt.image.BufferedImage, javax.imageio.ImageIOи java.awt.Color) следует использовать для чтения/записи и изменения изображений.

С помощью BufferedImageметодов класса к пикселям изображения можно получить доступ как к двумерному массиву . Также обратите внимание, что этот тип BufferedImage.TYPE_INT_RGBследует использовать для 24-битного цвета с BufferedImageклассом.

Возможно, вам будет полезно взглянуть на некоторые примеры классов BufferedImage , ImageIO и Color .

Наконец, операции ввода-вывода могут легко выйти из строя. Однако программа не должна останавливаться при возникновении исключения ввода-вывода, например, если задано неправильное имя входного файла, поэтому все исключения ввода-вывода должны обрабатываться.

Цели
Когда пользователь вводит команду hide, программа должна запросить у него имя входного файла изображения с сообщением Input image file:и имя файла выходного изображения с сообщением Output image file:. Их следует использовать для чтения файла входного изображения и записи файла выходного изображения соответственно.

После прочтения имен файлов программа должна вывести следующие сообщения: Input Image: [input filename]и Output Image: [output filename].

Когда входное изображение считывается, младшему биту для каждого цвета (красного, зеленого и синего) присваивается значение1. Полученное изображение будет сохранено с указанным именем файла выходного изображения в формате PNG.

Следует применить правильный метод, чтобы исключения ввода-вывода не завершали работу программы. В таких случаях должно быть выведено сообщение об исключении и программа должна вернуться в командный цикл.

Примеры
Символ «больше», за которым следует пробел ( > ), представляет ввод пользователя. Обратите внимание, что это не часть ввода.

Пример 1: Нормальное выполнение .

Task (hide, show, exit):
> hide
Input image file:
> winter.png
Output image file:
> image.png
Input Image: winter.png
Output Image: image.png
Image image.png is saved.
Task (hide, show, exit):
> exit
Bye!

Пример 2: Входной файл не существует, обнаружено исключение.

Task (hide, show, exit):
> hide
Input image file:
> notexist.png
Output image file:
> image.png
Can't read input file!
Task (hide, show, exit):
> exit
Bye!

3_4

Скрыть сообщение на изображении
Описание
Стеганография – это сокрытие информации таким образом, чтобы никто даже не догадался, что прямо у него на глазах спрятано секретное сообщение. Метод, который мы собираемся использовать для сокрытия сообщения на изображении, основан на небольших изменениях цвета, которые невозможно обнаружить.

Как вы уже знаете, данные сообщения могут быть вставлены в позиции младших битов каждого значения цвета каждого пикселя. Что делает3бит на пиксель и всего3*[ширина изображения]*[высота изображения]бит для всего изображения. Конечно, нет необходимости использовать их все: было бы более эффективно иметь алгоритм, который выбирает, какие биты использовать. Мы могли бы сделать это еще более сложным: выбор битов может быть основан на пароле, так что конфигурация будет разной при каждом изменении пароля.

Однако для простоты в этом проекте мы будем использовать только младшие биты синего цвета каждого пикселя. Таким образом, каждое изображение может содержать до[ширина изображения]*[высота изображения]биты.

Как видно из предыдущего этапа, изображения можно обрабатывать как двумерные массивы. На этом этапе порядок пикселей должен быть слева направо в первой строке, а затем одинаковый для каждой последующей строки. На рисунке ниже показан порядок:

строки пикселей изображения

Сообщение, которое нужно скрыть, имеет тип String и кодировку UTF-8. В результате сообщение может быть на любом языке. Чтобы скрыть секретное сообщение, нам нужно сначала преобразовать его в массив байтов: вы можете сделать это с помощью функции encodeToByteArray(). Соответственно, массив байтов можно восстановить к типу String, применив toString(Charsets.UTF_8).

Когда программа считывает биты изображения, чтобы восстановить сообщение, она должна знать, когда остановиться, то есть когда она прочитала все сообщение. Для этого определенные байты должны быть помещены в конец массива байтов. В частности, мы добавим три байта со значениями0,0,3(или00000000 00000000 00000011в двоичном формате). Когда программа встретит эти байты, она поймет, что достигла конца сообщения.

Цели
Когда hideкоманда задана, программа получает имена файлов входного и выходного изображения, как и на предыдущем этапе. Затем он запрашивает у пользователя секретное сообщение, печатая Message to hide:.

Сообщение должно быть преобразовано в массив байтов. Затем 3 байта со значениями0,0,3следует добавить в конец массива.

Программа должна проверить, что размер изображения достаточен для хранения массива Bytes. Если нет, он должен напечатать сообщение об ошибке с текстом The input image is not large enough to hold this message.и вернуться в меню.

Каждый бит этого массива байтов будет сохранен в позиции младшего бита синего цвета каждого пикселя, как показано на рисунке ниже. Выходное изображение должно быть сохранено в формате PNG.

Стеганография изображений RGB

При showвводе команды программа запрашивает имя файла изображения (ранее сохраненное со скрытым сообщением), печатая Input image file:. Изображение откроется, и массив байтов будет восстановлен по крупицам; программа прекратит его читать, когда байты со значениями0,0,3встречаются.

Последние 3 байта (значения0,0,3) следует удалить из конца массива байтов. Затем сообщение должно быть восстановлено как строка из массива байтов (или00000000 00000000 00000011биты).

Программа должна распечатать Message:, а затем и само сообщение с новой строки.

Примеры
Символ «больше», за которым следует пробел ( > ), представляет ввод пользователя. Обратите внимание, что это не часть ввода.

Пример 1: Скрытие и извлечение сообщения .

Task (hide, show, exit):
> hide
Input image file:
> sky.png
Output image file:
> hide.png
Message to hide:
> Hello World!
Message saved in hide.png image.
Task (hide, show, exit):
> show
Input image file:
> hide.png
Message:
Hello World!
Task (hide, show, exit):
> exit
Bye!

Пример 2: Скрытие сообщений в очень маленьком изображении.

Task (hide, show, exit):
> hide
Input image file:
> small.png
Output image file:
> out1.png
Message to hide:
> 123
Message saved in out1.png image.
Task (hide, show, exit):
> hide
Input image file:
> small.png
Output image file:
> out2.png
Message to hide:
> abcdefghijk
The input image is not large enough to hold this message.
Task (hide, show, exit):
> exit
Bye!


4_4

Зашифровать сообщение
Описание
Мы хорошо спрятали сообщение, но перестраховаться нельзя, поэтому зашифруем само сообщение.

Исключающее ИЛИ (XOR) — это логическая операция, вывод которой истинен только в том случае, если ее входные данные различаются. XOR также может использоваться с битами данных, где 1означает trueи 0означает false. Ниже вы можете найти таблицу истинности XOR (слева) с входными и выходными значениями операции XOR, а также побитовую операцию XOR (справа).

таблица истинности xor

У XOR есть интересная математическая особенность: A XOR B = C и C XOR B = A. Если A — это сообщение, а B — пароль, то C — зашифрованное сообщение. Используя B и C, мы можем восстановить A.

Ниже приведен пример, в котором мы используем Bytes в качестве сообщения, пароля и зашифрованного сообщения:

шифрование сообщения xor

Таким образом, для шифрования и дешифрования может использоваться не только один и тот же пароль ( симметричное шифрование ), но и один и тот же простой метод. Это шифрование очень надежное, если пароль случайный и имеет тот же размер, что и сообщение.

На этом этапе у пользователя должен быть запрошен пароль, а затем сообщение шифруется, прежде чем оно будет скрыто на изображении.

Цели
Когда hideдана команда и введено секретное сообщение, пользователю будет предложено ввести пароль вместе с сообщением Password:.

Программа считывает строку пароля и преобразует ее в массив байтов. Первый байт сообщения будет зашифрован XOR с использованием первого байта пароля, второй байт сообщения будет зашифрован XOR с использованием второго байта пароля и так далее. Если пароль короче сообщения, то после последнего байта пароля следует снова использовать первый байт пароля.

Три байта со значениями0,0,3следует добавить в зашифрованный массив байтов. Если размер изображения достаточен для хранения массива Bytes, результат скрывается в изображении, как и на предыдущем этапе.

Когда showдана команда и введено имя файла, пользователю будет предложено ввести пароль с сообщением Password:. Изображение должно открыться и зашифрованный массив байтов должен быть восстановлен, как и на предыдущем этапе; программа перестает его читать, когда байты со значениями0,0,3найдены. Последние три байта следует удалить, а зашифрованный массив байтов следует расшифровать с помощью пароля. Наконец, сообщение должно быть восстановлено до типа String, и программа должна напечатать Message:, а затем и само сообщение на новой строке.

Пример
Символ «больше», за которым следует пробел ( > ), представляет ввод пользователя. Обратите внимание, что это не часть ввода.

Пример 1: Шифрование и дешифрование сообщения .

Task (hide, show, exit):
> hide
Input image file:
> travel.png
Output image file:
> enc.png
Message to hide:
> My encrypted message!
Password:
> mypassword
Message saved in enc.png image.
Task (hide, show, exit):
> show
Input image file:
> enc.png
Password:
> mypassword
Message:
My encrypted message!
Task (hide, show, exit):
> exit
Bye!

Пример 2: Шифрование сообщения в очень маленьком изображении .

Task (hide, show, exit):
> hide
Input image file:
> small.png
Output image file:
> out2.png
Message to hide:
> abcdefghijk
Password:
> mypassword
The input image is not large enough to hold this message.
Task (hide, show, exit):
> exit
Bye!
