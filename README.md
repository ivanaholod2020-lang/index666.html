# index666.html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Продвинутый текстовый редактор</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: Arial, sans-serif;
            background-color: #f5f5f5;
            height: 100vh;
        }
        
        .editor-container {
            display: flex;
            flex-direction: column;
            height: 100vh;
        }
        
        .toolbar {
            background: #2c3e50;
            padding: 10px;
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }
        
        .btn {
            padding: 8px 12px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            background: #34495e;
            color: white;
            font-size: 14px;
        }
        
        .btn:hover {
            background: #46637e;
        }
        
        .active {
            background: #3498db;
        }
        
        .search-container {
            display: flex;
            gap: 5px;
            margin-left: auto;
        }
        
        .search-input {
            padding: 5px 10px;
            border: 1px solid #ccc;
            border-radius: 3px;
        }
        
        #editor {
            flex: 1;
            padding: 20px;
            border: none;
            outline: none;
            font-size: 16px;
            line-height: 1.5;
        }
    </style>
</head>
<body>
    <div class="editor-container">
        <!-- Панель инструментов -->
        <div class="toolbar">
            <button class="btn" onclick="execCmd('bold')"><b>Ж</b></button>
            <button class="btn" onclick="execCmd('italic')"><i>К</i></button>
            <button class="btn" onclick="execCmd('underline')"><u>Ч</u></button>
            <button class="btn" onclick="execCmd('justifyLeft')">Выровнять влево</button>
            <button class="btn" onclick="execCmd('justifyCenter')">По центру</button>
            <button class="btn" onclick="execCmd('justifyRight')">Выровнять вправо</button>
            <button class="btn" onclick="insertImage()">Вставить изображение</button>
            <button class="btn" onclick="addLink()">Добавить ссылку</button>
            <button class="btn" onclick="execCmd('insertOrderedList')">Нумерованный список</button>
            <button class="btn" onclick="execCmd('insertUnorderedList')">Маркированный список</button>
            <button class="btn" onclick="execCmd('undo')">Отменить</button>
            <button class="btn" onclick="execCmd('redo')">Повторить</button>
            
            <div class="search-container">
                <input type="text" id="searchInput" class="search-input" placeholder="Найти...">
                <button class="btn" onclick="findText()">Найти</button>
            </div>
        </div>
        
        <!-- Область редактирования -->
        <div contenteditable="true" id="editor" placeholder="Начните вводить текст..."></div>
    </div>

    <script>
        // Инициализация редактора
        document.addEventListener('DOMContentLoaded', function() {
            document.getElementById('editor').focus();
        });

        // Выполнение команд форматирования
        function execCmd(command) {
            document.execCommand(command, false, null);
            document.getElementById('editor').focus();
        }

        // Вставка изображения
        function insertImage() {
            const url = prompt('Введите URL изображения:');
            if (url) {
                document.execCommand('insertImage', false, url);
                document.getElementById('editor').focus();
            }
        }

        // Добавление ссылки
        function addLink() {
            const url = prompt('Введите URL ссылки:');
            if (url) {
                document.execCommand('createLink', false, url);
                document.getElementById('editor').focus();
            }
        }

        // Поиск текста
        function findText() {
            const searchTerm = document.getElementById('searchInput').value;
            if (!searchTerm) return;

            const editor = document.getElementById('editor');
            const range = document.createRange();
            range.selectNodeContents(editor);
            const text = range.toString();

            // Очищаем предыдущее выделение
            editor.innerHTML = editor.innerHTML.replace(/<mark>/g, '').replace(/<\/mark>/g, '');

            if (text.includes(searchTerm)) {
                // Выделяем найденные вхождения
                const regex = new RegExp(`(${searchTerm})`, 'gi');
                editor.innerHTML = editor.innerHTML.replace(regex, '<mark>$1</mark>');
            } else {
                alert('Текст не найден!');
            }
            document.getElementById('editor').focus();
        }

        // Обработка нажатия Enter — сохраняем форматирование
        document.getElementById('editor').addEventListener('keydown', function(e) {
            if (e.key === 'Enter') {
                e.preventDefault();
                document.execCommand('insertLineBreak');
            }
        });
    </script>
</body>
</html>
