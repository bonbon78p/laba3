
# Лабораторная работа №3: Работа с базой данных в Android

**Выполнила**: Плотникова Вероника  
**Язык программирования**: Java

## Цель работы
Изучить работу Android-приложения с базой данных, используя SQLite. В ходе выполнения работы будет создано приложение, которое будет:
1. Создавать базу данных и таблицу при первом запуске.
2. Вносить данные в таблицу и отображать их.
3. Обновлять записи в таблице.

## Задание
### Часть 1:
Создать приложение, взаимодействующее с базой данных. Первое активити должно содержать три кнопки:
1. **Показать записи** — открывает новое активити, где выводится информация из таблицы «Одногруппники».
2. **Добавить запись** — вносит новую запись в таблицу.
3. **Обновить запись** — обновляет ФИО в последней записи на «Иванов Иван Иванович».

Требования:
- База данных должна создаваться при первом запуске приложения, если она не существует.
- Таблица «Одногруппники» содержит поля:
  - ID (целочисленный, автоинкремент);
  - Фамилия, Имя, Отчество (отдельные поля для каждого);
  - Время добавления записи.

### Часть 2:
Создать новую версию базы данных с обновленной схемой. При изменении версии БД нужно удалить таблицу «Одногруппники» и создать её заново с новой структурой (разделить поле ФИО на три отдельных поля). Убедитесь, что приложение корректно обновляет базу данных при изменении версии.

## Технологии и инструменты
- Android Studio
- Язык программирования: Java
- База данных: SQLite
- SQLiteOpenHelper для работы с базой данных

## Структура приложения

### 1. Класс `DatabaseHelper`
Отвечает за создание и управление базой данных. В нем реализованы методы для:
- создания базы данных и таблицы;
- добавления новой записи;
- обновления последней записи;
- извлечения всех данных.

### 2. `MainActivity`
Главное активити приложения. Содержит три кнопки для взаимодействия с базой данных:
- Показать записи.
- Добавить запись.
- Обновить запись.

### 3. `ViewActivity`
Активити для отображения всех записей из таблицы «Одногруппники». Извлекает данные из базы и отображает их в формате: «Фамилия Имя Отчество - Время добавления».

## Установка и запуск

### Шаг 1: Склонируйте проект
```
bash
git clone https://github.com/VeronikaPlotnikova/3Laba_Plotnikova
```
### Шаг 2: Откройте проект в Android Studio
Откройте Android Studio.
Выберите Open an existing project и укажите путь к папке с проектом.

### Шаг 3: Запуск проекта
Подключите устройство или настройте эмулятор Android.
Нажмите кнопку Run в Android Studio для сборки и запуска приложения.

## Ключевые классы и методы

### 1. DBHelper
Класс, который управляет базой данных и выполняет операции CRUD (создание, чтение, обновление, удаление).
Пример создания таблицы в методе onCreate:
```
@Override
private static final String TABLE_CREATE = "CREATE TABLE " + TABLE_NAME + " (" +
            COLUMN_ID + " INTEGER PRIMARY KEY AUTOINCREMENT, " +
            COLUMN_SURNAME + " TEXT, " +
            COLUMN_NAME + " TEXT, " +
            COLUMN_PATRONYMIC + " TEXT, " +
            COLUMN_TIMESTAMP + " TIMESTAMP DEFAULT CURRENT_TIMESTAMP" + ");";

    public DatabaseHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(TABLE_CREATE);
    }
```
### 2. MainActivity
Главное активити, где пользователь может выбрать одно из трех действий: показать записи, добавить запись, обновить запись.

### 3. ViewActivity
Активити для отображения всех записей. Использует Cursor для чтения данных из базы.

Пример извлечения данных:

```
ublic class ViewActivity extends AppCompatActivity {
    private TextView textView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_view);

        textView = findViewById(R.id.textView);
        displayRecords();
    }

    @SuppressLint("Range")
    private void displayRecords() {
        SQLiteDatabase db = new DatabaseHelper(this).getReadableDatabase();
        Cursor cursor = db.rawQuery("SELECT * FROM " + DatabaseHelper.TABLE_NAME, null);
        StringBuilder data = new StringBuilder();

        int studentNumber = 1; // Счётчик студентов
        while (cursor.moveToNext()) {
            data.append("Студент ")
                    .append(studentNumber++) // Номер студента
                    .append(": ")
                    .append(cursor.getString(cursor.getColumnIndex(DatabaseHelper.COLUMN_NAME))) // ФИО
                    .append(", ")
                    .append(cursor.getString(cursor.getColumnIndex(DatabaseHelper.COLUMN_TIMESTAMP))) // Время добавления
                    .append("\n");
        }

        textView.setText(data.toString());
        cursor.close();
    }
}
```
Логика обновления базы данных
При изменении версии базы данных, метод onUpgrade() удаляет старую таблицу и создает новую с обновленной схемой:

```
    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL("DROP TABLE IF EXISTS " + TABLE_NAME);
        onCreate(db);
    }
```
