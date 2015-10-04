# Работа с файловыми путями

    Стабильность: 3 - Стабильно

Этот модуль содержит средства для обработки и преобразования путей к файлам.
Большинстов из этих методов осуществляют лишь строковые преобразования.
Они не содержат проверок существования и валидности путей с помощью обращений к файловой системе.

Используйте `require('path')` чтобы получить доступ к этому модулю.

## path.normalize(p)

Нормализует строку пути, обрабатывая `'..'` и `'.'`.

Пример:

    path.normalize('/foo/bar/baz/asdf/quux/..')
    // returns
    '/foo/bar/baz/asdf'

## path.join([path1], [path2], [...])

Соединяет все аргументы и нормализует получившийся путь.
Начиная с v0.10 все аргументы должны быть `string`.

Пример:

    path.join('/foo', 'bar/sub', 'xxl', 'file.ext')
    // returns
    '/foo/bar/sub/xxl/file.ext'

    path.join('foo', {}, 'bar')
    //               ^
    //             throws exception
    TypeError: Arguments to path.join must be strings

## path.resolve([from ...], to)

Разрешает путь `to` в абсолютный.

Если `to` не является абсолютным, то к нему добавляют пути, справа налево,
из аргументов `from` до тех пор, пока полученный путь не будет абсолютным.
Если в итоге путь останется относительным, он будет разрешён относительно
рабочей директории. полученный путь нормализуется и у него удаляется
завершающий слеш, если конечно это не корневая директория.
Игнорирует аргументы, не являющиеся строками.

Возможно, вам будет проще понять механизм работы этого метода,
если считать что он последовательно выполняет команду `cd` и возвращает конечный путь, т.е.

    path.resolve('foo/bar', '/tmp/file/', '..', 'a/../subfile')

возвращает тоже самое, что и:

    cd foo/bar
    cd /tmp/file/
    cd ..
    cd a/../subfile
    pwd

Разница в том, что промежуточные пути могут не существовать или быть файлами.

Примеры:

    path.resolve('/foo/bar', './baz')
    // returns
    '/foo/bar/baz'

    path.resolve('/foo/bar', '/tmp/file/')
    // returns
    '/tmp/file'

    path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif')
    // if currently in /home/myself/node, it returns
    '/home/myself/node/wwwroot/static_files/gif/image.gif'

## path.relative(from, to)

Вычисляет относительный путь от `from` до `to`.

Стоит использовать в случае, если нужно получить относительный путь до одного абсолютного пути
относительно другого абсолютного пути. Это преобразование является обратным по отношению
к  `path.resolve`, т.е.:

    path.resolve(from, path.relative(from, to)) == path.resolve(to)

Пример:

    path.relative('C:\\orandea\\test\\aaa', 'C:\\orandea\\impl\\bbb')
    // returns
    '..\\..\\impl\\bbb'

    path.relative('/data/orandea/test/aaa', '/data/orandea/impl/bbb')
    // returns
    '../../impl/bbb'

## path.dirname(p)

Возвращает имя директории для пути. Действует как Unix-команда `dirname`.

Пример:

    path.dirname('/foo/bar/baz/asdf/quux')
    // returns
    '/foo/bar/baz/asdf'

## path.basename(p, [ext])

Возвращает последнюю часть пути. Действует как Unix-команда `basename`.

Пример:

    path.basename('/foo/bar/baz/asdf/quux.html')
    // returns
    'quux.html'

    path.basename('/foo/bar/baz/asdf/quux.html', '.html')
    // returns
    'quux'

## path.extname(p)

Возвращает расширение пути. Учитывается всё после последней '.' в последней части пути.
Если в последней части нет '.' или '.' единственный символ, возвращает пустую строку.

Пример:

    path.extname('index.html')
    // returns 
    '.html'
    
    path.extname('index.')
    // returns
    '.'

    path.extname('index')
    // returns
    ''

///////////////////////////////
## path.sep()

Возвращает платформозависимый файл-сепаратор. '\\' or '/'.

В *nix системах:

    'foo/bar/baz'.split(path.sep)
    // returns
    ['foo', 'bar', 'baz']

В Windows:

    'foo\\bar\\baz'.split(path.sep)
    // returns
    ['foo', 'bar', 'baz']
    
## path.delimiter

Платформо-зависимый путь-сепаратор: ';' или ':'.

В *nix системах:

    console.log(process.env.PATH)
    // '/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin'

    process.env.PATH.split(path.delimiter)
    // returns
    ['/usr/bin', '/bin', '/usr/sbin', '/sbin', '/usr/local/bin']

В Windows:

    console.log(process.env.PATH)
    // 'C:\Windows\system32;C:\Windows;C:\Program Files\node\'

    process.env.PATH.split(path.delimiter)
    // returns
    ['C:\\Windows\\system32', 'C:\\Windows', 'C:\\Program Files\\node\\']
    
## path.parse(pathString)

Возвращает объект из составных частей пути.

В *nix системах:

    path.parse('/home/user/dir/file.txt')
    // returns
    {
        root : "/",
        dir : "/home/user/dir",
        base : "file.txt",
        ext : ".txt",
        name : "file"
    }
    
В Windows:

    path.parse('C:\\path\\dir\\index.html')
    // returns
    {
        root : "C:\\",
        dir : "C:\\path\\dir",
        base : "index.html",
        ext : ".html",
        name : "index"
    }

## path.format(pathObject)

Обратная операция для `path.parse(pathString)` Возвращает путь из объекта.

    path.format({
        root : "/",
        dir : "/home/user/dir",
        base : "file.txt",
        ext : ".txt",
        name : "file"
    })
    // returns
    '/home/user/dir/file.txt'
    
## path.posix

Provide access to aforementioned path methods but always interact in a posix compatible way.

## path.win32#

Provide access to aforementioned path methods but always interact in a win32 compatible way.
