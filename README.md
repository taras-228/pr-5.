# pr-5.
import sqlite3
from pathlib import Path

folder = Path(__file__).resolve().parent
con = sqlite3.connect(folder / "app.db")

con.execute("""
CREATE TABLE IF NOT EXISTS workshops (
    id INTEGER PRIMARY KEY,
    name TEXT,
    category TEXT,
    value INTEGER
)
""")

data = [
    ("Лепка из глины", "Творчество", 90),
    ("Рисование", "Творчество", 60),
    ("Музыка", "Творчество", 120),
    ("Оригами", "Творчество", 45),
    ("Основы 3D-печати", "Технологии", 75),
    ("Программирование", "Технологии", 100),
    ("Домашняя еда", "Кулинария", 80),
    ("Кондитерское еда", "Кулинария", 150),
    ("Йога", "Спорт", 50),
    ("Футбол", "Спорт", 65),
]

count = con.execute(
    "SELECT COUNT(*) FROM workshops"
).fetchone()[0]
if count == 0:
    con.executemany(
        "INSERT INTO workshops (name, category, value) "
        "VALUES (?, ?, ?)",
        data
    )
    con.commit()

print("Все записи:")
for row in con.execute("SELECT * FROM workshops ORDER BY id"):
    print(row)

chosen_category = "Творчество"
print("\nВыбранная категория:", chosen_category)
for row in con.execute(
    "SELECT * FROM workshops WHERE category = ?",
    (chosen_category,)
):
    print(row)

limit = 60
print("\nФильтр: длительность больше", limit, "минут")
for row in con.execute(
    "SELECT * FROM workshops WHERE value > ?",
    (limit,)
):
    print(row)

print("\nСортировка по value (ASC):")
for row in con.execute(
    "SELECT * FROM workshops ORDER BY value ASC, id ASC"
):
    print(row)

record_id = 1
new_value = 105
con.execute(
    "UPDATE workshops SET value = ? WHERE id = ?",
    (new_value, record_id)
)
con.commit()
print("\nПосле изменения записи id =", record_id)
print(con.execute(
    "SELECT * FROM workshops WHERE id = ?", (record_id,)
).fetchone())

record_id = 4
con.execute("DELETE FROM workshops WHERE id = ?", (record_id,))
con.commit()
print("\nПосле удаления записи id =", record_id)
for row in con.execute("SELECT * FROM workshops ORDER BY id"):
    print(row)

con.close()
