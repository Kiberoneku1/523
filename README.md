import random
import time

# ==================== КЛАССЫ ====================

class Fighter:
    def __init__(self, name):
        self.name = name
        self.hp = 100
        self.max_hp = 100
        self.power = 10
        self.defense = 5
        self.alive = True

    def is_alive(self):
        return self.hp > 0

    def take_damage(self, dmg):
        real = max(1, dmg - self.defense)
        self.hp -= real
        if self.hp <= 0:
            self.hp = 0
            self.alive = False
        return real

    def show(self):
        bar_len = 20
        filled = int(bar_len * self.hp / self.max_hp)
        bar = "█" * filled + "░" * (bar_len - filled)
        print(f"  {self.name}: [{bar}] {self.hp}/{self.max_hp} HP")


# ==================== ДЕЙСТВИЯ ====================

def punch(attacker, target):
    """Обычный удар"""
    dmg = random.randint(attacker.power - 3, attacker.power + 5)
    real = target.take_damage(dmg)
    print(f"👊 {attacker.name} бьёт {target.name} на {real} урона!")
    return real

def kick(attacker, target):
    """ПИНОК — сильнее, но может промахнуться"""
    if random.random() < 0.2:
        print(f"🦵 {attacker.name} пытается пнуть {target.name}, но промахивается и падает в абобу!")
        return 0
    dmg = random.randint(attacker.power + 5, attacker.power + 15)
    real = target.take_damage(dmg)
    print(f"🦵💥 {attacker.name} ПИНАЕТ {target.name} на {real} урона! Бурмалда!")
    return real

def heal(attacker, target):
    """Лечение — сметана котоноса"""
    amount = random.randint(10, 25)
    attacker.hp = min(attacker.max_hp, attacker.hp + amount)
    print(f"🍦 {attacker.name} жрёт сметану и восстанавливает {amount} HP!")

def kotanos_blast(attacker, target):
    """Секретный приём — Пердёж Котоноса (только раз в бой)"""
    if attacker.used_ultimate:
        print(f"❌ {attacker.name} уже использовал Пердёж Котоноса!")
        return 0
    attacker.used_ultimate = True
    dmg = random.randint(25, 45)
    real = target.take_damage(dmg)
    print(f"💨🌌 {attacker.name} выпускает ПЕРДЁЖ КОТОНОСА! {target.name} получает {real} урона и видит космос Бурмалды!")
    return real


# ==================== БОЙ ====================

def battle(fighters):
    print("\n" + "=" * 50)
    print("⚔️  БИТВА НАЧИНАЕТСЯ!  ⚔️")
    print("=" * 50)

    for f in fighters:
        f.used_ultimate = False

    turn = 0
    while sum(1 for f in fighters if f.is_alive()) > 1:
        current = fighters[turn % len(fighters)]
        turn += 1

        if not current.is_alive():
            continue

        print(f"\n--- Ход: {current.name} ---")
        for f in fighters:
            f.show()

        # Цели — все живые, кроме себя
        targets = [f for f in fighters if f.is_alive() and f is not current]
        if not targets:
            break

        print("\nДействия:")
        print("  1. 👊 Ударить")
        print("  2. 🦵 Пнуть (сильно, но можно промахнуться)")
        print("  3. 🍦 Сметана (лечение)")
        print("  4. 💨 Пердёж Котоноса (раз в бой)")
        print("  5. 🏃 Пропустить ход")

        choice = input("Твой выбор (1-5): ").strip()

        # Выбор цели, если их несколько
        if len(targets) > 1 and choice in ("1", "2", "4"):
            print("\nКого выбрать целью?")
            for i, t in enumerate(targets):
                print(f"  {i+1}. {t.name} ({t.hp} HP)")
            try:
                idx = int(input("Номер цели: ")) - 1
                target = targets[idx]
            except (ValueError, IndexError):
                target = random.choice(targets)
                print(f"Ты запутался и атакуешь {target.name}!")
        else:
            target = targets[0]

        if choice == "1":
            punch(current, target)
        elif choice == "2":
            kick(current, target)
        elif choice == "3":
            heal(current, target)
        elif choice == "4":
            kotanos_blast(current, target)
        elif choice == "5":
            print(f"😴 {current.name} пропускает ход и думает о сметане.")
        else:
            print("🤔 Непонятное действие, пропуск хода.")

        time.sleep(0.5)

    # Итог
    print("\n" + "=" * 50)
    alive = [f for f in fighters if f.is_alive()]
    if len(alive) == 1:
        print(f"🏆 ПОБЕДИТЕЛЬ: {alive[0].name}! Бурмалда ликует!")
    elif len(alive) == 0:
        print("💀 Все умерли. Котонос грустит.")
    else:
        print("🤝 Ничья. Все живы, но устали.")
    print("=" * 50)


# ==================== ГЛАВНОЕ МЕНЮ ====================

def main():
    print("=" * 50)
    print("🐱 КОТОНОС: БИТВА В КОСМОСЕ БУРМАЛДЫ 🐱")
    print("=" * 50)
    print("Правила: побеждает последний выживший.")
    print("Можно пинаться. Можно пердеть как Котонос.")
    print()

    try:
        n = int(input("Сколько игроков? (2-4): "))
        n = max(2, min(4, n))
    except ValueError:
        n = 2
        print("Непонятно, играем вдвоём.")

    fighters = []
    for i in range(n):
        name = input(f"Имя игрока {i+1}: ").strip()
        if not name:
            name = f"Котонос-{i+1}"
        fighters.append(Fighter(name))

    battle(fighters)

    again = input("\nЕщё раз? (д/н): ").strip().lower()
    if again in ("д", "да", "y", "yes"):
        main()
    else:
        print("Пока! Мяу. 🌌")


if __name__ == "__main__":
    main()
    
