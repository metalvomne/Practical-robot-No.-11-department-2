import pandas as pd
import matplotlib.pyplot as plt
import calendar

# === 1. Завантаження даних ===
file_path = r"C:\Users\metal\Downloads\comptagevelo2010.csv"
df = pd.read_csv(file_path)

# === 2. Інформація про DataFrame ===
print("=== Перші 5 рядків ===")
print(df.head(), "\n")

print("=== Інформація про DataFrame ===")
print(df.info(), "\n")

print("=== Описова статистика ===")
print(df.describe(), "\n")

# === 3. Обробка дати ===
df["Date"] = pd.to_datetime(df["Date"], format="%d/%m/%Y", errors="coerce")
df["Month"] = df["Date"].dt.month

# === 4. Вибір лише числових колонок ===
numeric_cols = df.select_dtypes(include=["number"]).columns

# === 5. Загальна кількість велосипедистів за рік (усі доріжки) ===
total_all = df[numeric_cols].sum().sum()
print(f"Загальна кількість велосипедистів за рік (усі доріжки): {int(total_all)}\n")

# === 6. Загальна кількість велосипедистів за рік по кожній доріжці ===
total_per_lane = df[numeric_cols].sum().sort_values(ascending=False)
print("=== Загальна кількість велосипедистів по кожній доріжці ===")
print(total_per_lane, "\n")

# === 7. Найпопулярніші місяці для трьох вибраних доріжок ===
lanes_to_check = list(numeric_cols[:3])
print("=== Найпопулярніші місяці для трьох вибраних доріжок ===")
months_ukr = {
    1: "січень", 2: "лютий", 3: "березень", 4: "квітень",
    5: "травень", 6: "червень", 7: "липень", 8: "серпень",
    9: "вересень", 10: "жовтень", 11: "листопад", 12: "грудень"
}

for lane in lanes_to_check:
    month_num = df.groupby("Month")[lane].sum().idxmax()
    month_name = months_ukr.get(month_num, "невідомо")
    print(f"Для доріжки '{lane}' найпопулярніший місяць: {month_num} ({month_name})")
print()

# === 8. Побудова графіка для однієї велодоріжки ===
lane_to_plot = lanes_to_check[0]
monthly_data = df.groupby("Month")[lane_to_plot].sum()

plt.figure(figsize=(8, 5))
plt.plot(monthly_data.index, monthly_data.values, marker='o', color='blue', linewidth=2)
plt.title(f"Завантаженість велодоріжки '{lane_to_plot}' по місяцях (2010 рік)")
plt.xlabel("Місяць")
plt.ylabel("Кількість велосипедистів")
plt.xticks(range(1, 13))
plt.grid(True)
plt.tight_layout()
plt.show()
