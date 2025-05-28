import random

class Postac:
    def __init__(self, imie, zycie, sila, zrecznosc, inteligencja, many, poziom=1):
        self.imie = imie
        self.zycie = zycie
        self.maks_zycie = zycie
        self.sila = sila
        self.zrecznosc = zrecznosc
        self.inteligencja = inteligencja
        self.mana = many
        self.maks_mana = many
        self.poziom = poziom
        self.ekwipunek = []
        self.ataki = []
    
    def czy_zyje(self):
        return self.zycie > 0
    
    def otrzymano_obrazenia(self, ilosc):
        self.zycie -= ilosc
        if self.zycie < 0:
            self.zycie = 0
        print(f"{self.imie} otrzymał {ilosc} obrażeń. Życie: {self.zycie}/{self.maks_zycie}")
    
    def ulecz(self, ilosc):
        self.zycie += ilosc
        if self.zycie > self.maks_zycie:
            self.zycie = self.maks_zycie
        print(f"{self.imie} odzyskał {ilosc} punktów życia. Życie: {self.zycie}/{self.maks_zycie}")
    
    def zuzyj_mane(self, ilosc):
        if self.mana >= ilosc:
            self.mana -= ilosc
            return True
        else:
            print(f"{self.imie} nie ma wystarczająco many!")
            return False
    
    def odzyskaj_mane(self, ilosc):
        self.mana += ilosc
        if self.mana > self.maks_mana:
            self.mana = self.maks_mana
        print(f"{self.imie} odzyskał {ilosc} many. Mana: {self.mana}/{self.maks_mana}")
    
    def dodaj_atak(self, atak):
        self.ataki.append(atak)
    
    def pokaz_ataki(self):
        for i, atak in enumerate(self.ataki):
            print(f"{i+1}. {atak.nazwa} (Moc: {atak.moc}, Koszt many: {atak.koszt_many})")

class Atak:
    def __init__(self, nazwa, moc, koszt_many=0):
        self.nazwa = nazwa
        self.moc = moc
        self.koszt_many = koszt_many

class Gracz(Postac):
    def __init__(self, imie):
        super().__init__(imie, zycie=100, sila=15, zrecznosc=12, inteligencja=10, many=30)
        self.zloto = 0
        self.poziom = 1
    
    def atakuj(self, cel, atak):
        if atak.koszt_many > 0:
            if not self.zuzyj_mane(atak.koszt_many):
                print("Brak many na atak!")
                return
        obrazenia = self.sila + atak.moc + random.randint(-3,3)
        print(f"{self.imie} używa {atak.nazwa} i zadaje {obrazenia} obrażeń {cel.imie}!")
        cel.otrzymano_obrazenia(obrazenia)

class Potwor(Postac):
    def __init__(self, nazwa, zycie, sila, zrecznosc, inteligencja, many=0, poziom=1):
        super().__init__(nazwa, zycie, sila, zrecznosc, inteligencja, many, poziom)
    
    def atakuj(self, cel):
        if not self.ataki:
            obrazenia = self.sila + random.randint(-2,2)
            print(f"{self.imie} atakuje i zadaje {obrazenia} obrażeń {cel.imie}!")
            cel.otrzymano_obrazenia(obrazenia)
        else:
            atak = random.choice(self.ataki)
            if atak.koszt_many > 0:
                if not self.zuzyj_mane(atak.koszt_many):
                    print(f"{self.imie} nie ma many na atak {atak.nazwa} i uderza fizycznie.")
                    obrazenia = self.sila + random.randint(-2,2)
                else:
                    obrazenia = self.sila + atak.moc + random.randint(-2,2)
                    print(f"{self.imie} używa {atak.nazwa} i zadaje {obrazenia} obrażeń {cel.imie}!")
            else:
                obrazenia = self.sila + atak.moc + random.randint(-2,2)
                print(f"{self.imie} używa {atak.nazwa} i zadaje {obrazenia} obrażeń {cel.imie}!")
            cel.otrzymano_obrazenia(obrazenia)

cios = Atak("Cios", moc=5, koszt_many=0)
ognisty_podmuch = Atak("Ognisty Podmuch", moc=12, koszt_many=10)
trzask_pioruna = Atak("Trzask Pioruna", moc=15, koszt_many=15)

gracz = Gracz("Bohater")
gracz.dodaj_atak(cios)
gracz.dodaj_atak(ognisty_podmuch)

potwor = Potwor("Goblin", zycie=50, sila=8, zrecznosc=10, inteligencja=5)
potwor.dodaj_atak(cios)

gracz.atakuj(potwor, gracz.ataki[1])  
potwor.atakuj(gracz)

class Przedmiot:
    def __init__(self, nazwa, typ, wartosc, bonusy=None):
        self.nazwa = nazwa
        self.typ = typ  
        self.wartosc = wartosc
        self.bonusy = bonusy or {}  

    def pokaz_info(self):
        print(f"{self.nazwa} ({self.typ}) - Wartość: {self.wartosc} zł")
        if self.bonusy:
            print("Bonusy:")
            for stat, wartosc in self.bonusy.items():
                znak = "+" if wartosc > 0 else ""
                print(f"  {stat}: {znak}{wartosc}")

class Gracz(Postac):
    def __init__(self, imie):
        super().__init__(imie, zycie=100, sila=15, zrecznosc=12, inteligencja=10, many=30)
        self.zloto = 0
        self.poziom = 1
        self.ekwipunek = []
    
    def zaloz_przedmiot(self, przedmiot):
        print(f"{self.imie} zakłada {przedmiot.nazwa}.")
        self.ekwipunek.append(przedmiot)
        for stat, wartosc in przedmiot.bonusy.items():
            if hasattr(self, stat):
                setattr(self, stat, getattr(self, stat) + wartosc)
    
    def zdejmij_przedmiot(self, przedmiot):
        if przedmiot in self.ekwipunek:
            print(f"{self.imie} zdejmuje {przedmiot.nazwa}.")
            self.ekwipunek.remove(przedmiot)
            for stat, wartosc in przedmiot.bonusy.items():
                if hasattr(self, stat):
                    setattr(self, stat, getattr(self, stat) - wartosc)
    
    def pokaz_ekwipunek(self):
        if not self.ekwipunek:
            print("Brak ekwipunku.")
        else:
            print("Ekwipunek:")
            for przedmiot in self.ekwipunek:
                przedmiot.pokaz_info()

class Potwor(Postac):
    def __init__(self, nazwa, zycie, sila, zrecznosc, inteligencja, many=0, poziom=1):
        super().__init__(nazwa, zycie, sila, zrecznosc, inteligencja, many, poziom)


szkielet = Potwor("Szkielet", zycie=60, sila=12, zrecznosc=8, inteligencja=3, poziom=2)
smok = Potwor("Smok", zycie=200, sila=30, zrecznosc=15, inteligencja=20, many=50, poziom=10)

szkielet.dodaj_atak(Atak("Cios kością", moc=7))
smok.dodaj_atak(Atak("Ognisty oddech", moc=25, koszt_many=20))
smok.dodaj_atak(Atak("Miażdżący cios", moc=20))

miecz = Przedmiot("Miecz rycerski", "broń", 150, bonusy={"sila": 5})
zbroja = Przedmiot("Zbroja płytowa", "zbroja", 300, bonusy={"zycie": 30})
mikstura = Przedmiot("Mikstura zdrowia", "mikstura", 50)


def menu_walki(gracz, przeciwnik):
    while gracz.czy_zyje() and przeciwnik.czy_zyje():
        print("\nTwoja kolej! Wybierz atak:")
        gracz.pokaz_ataki()
        wybor = input("Podaj numer ataku lub 'q' aby uciec: ")
        if wybor == 'q':
            print("Uciekasz z walki...")
            break
        if not wybor.isdigit() or int(wybor) < 1 or int(wybor) > len(gracz.ataki):
            print("Niepoprawny wybór!")
            continue
        atak = gracz.ataki[int(wybor)-1]
        gracz.atakuj(przeciwnik, atak)
        if not przeciwnik.czy_zyje():
            print(f"{przeciwnik.imie} został pokonany!")
            break
        print(f"\nTeraz atakuje {przeciwnik.imie}!")
        przeciwnik.atakuj(gracz)
        if not gracz.czy_zyje():
            print("Zostałeś pokonany...")
            break


print("Walka z Goblinem rozpoczęta!")
menu_walki(gracz, potwor)

import random

class Gracz(Postac):
    def __init__(self, imie):
        super().__init__(imie, zycie=100, sila=15, zrecznosc=12, inteligencja=10, many=30)
        self.zloto = 0
        self.poziom = 1
        self.ekwipunek = []
        self.exp = 0
        self.exp_do_nastepnego = 100

    def zdobadz_exp(self, ile):
        print(f"{self.imie} zdobywa {ile} punktów doświadczenia.")
        self.exp += ile
        while self.exp >= self.exp_do_nastepnego:
            self.exp -= self.exp_do_nastepnego
            self.awansuj()

    def awansuj(self):
        self.poziom += 1
        print(f"{self.imie} awansuje na poziom {self.poziom}!")
        self.zycie += 20
        self.max_zycie += 20
        self.sila += 2
        self.zrecznosc += 1
        self.inteligencja += 1
        self.many += 10
        self.max_mana += 10
        self.exp_do_nastepnego = int(self.exp_do_nastepnego * 1.5)

    def uzyj_mikstury(self):
        for przedmiot in self.ekwipunek:
            if przedmiot.typ == "mikstura":
                print(f"{self.imie} używa {przedmiot.nazwa}!")
                self.zycie += 30
                if self.zycie > self.max_zycie:
                    self.zycie = self.max_zycie
                self.ekwipunek.remove(przedmiot)
                return
        print("Nie masz mikstur!")

ork = Potwor("Ork", zycie=80, sila=18, zrecznosc=10, inteligencja=4, poziom=3)
troll = Potwor("Troll", zycie=120, sila=22, zrecznosc=6, inteligencja=3, poziom=4)
ghul = Potwor("Ghul", zycie=90, sila=16, zrecznosc=12, inteligencja=6, poziom=3)


ork.dodaj_atak(Atak("Topór wojenny", moc=14))
troll.dodaj_atak(Atak("Miażdżący młot", moc=18))
ghul.dodaj_atak(Atak("Zatruty pazur", moc=10))
ghul.dodaj_atak(Atak("Krzyk śmierci", moc=8, koszt_many=5))


def wybierz_przeciwnika():
    potwory = [potwor, szkielet, smok, ork, troll, ghul]
    wybrany = random.choice(potwory)
    print(f"Z naprzeciwka wychodzi {wybrany.imie}!")
    return wybrany

def menu_walki(gracz, przeciwnik):
    while gracz.czy_zyje() and przeciwnik.czy_zyje():
        print("\nTwoja kolej! Co chcesz zrobić?")
        print("1. Atakuj")
        print("2. Użyj mikstury")
        print("3. Pokaż statystyki")
        print("q. Uciekaj")
        wybor = input("Wybór: ")

        if wybor == "1":
            gracz.pokaz_ataki()
            nr = input("Wybierz numer ataku: ")
            if not nr.isdigit() or int(nr) < 1 or int(nr) > len(gracz.ataki):
                print("Niepoprawny wybór ataku.")
                continue
            atak = gracz.ataki[int(nr)-1]
            gracz.atakuj(przeciwnik, atak)

        elif wybor == "2":
            gracz.uzyj_mikstury()

        elif wybor == "3":
            gracz.pokaz_statystyki()
            continue

        elif wybor == "q":
            print("Uciekasz z pola walki...")
            break

        else:
            print("Nieznana opcja.")
            continue

        if przeciwnik.czy_zyje():
            print(f"\n{przeciwnik.imie} atakuje!")
            przeciwnik.atakuj(gracz)
        else:
            print(f"\n{przeciwnik.imie} został pokonany!")
            exp = przeciwnik.poziom * 50
            gracz.zdobadz_exp(exp)

gracz = Gracz("Bohater")
gracz.dodaj_atak(Atak("Uderzenie mieczem", moc=10))
gracz.dodaj_atak(Atak("Cios krytyczny", moc=18, koszt_many=10))


class Miasto:
    def __init__(self, nazwa):
        self.nazwa = nazwa
        self.sklep = Sklep()
        self.trener = Trener()

    def odwiedz(self, gracz):
        while True:
            print(f"\nWitaj w mieście {self.nazwa}. Co chcesz zrobić?")
            print("1. Odwiedź sklep")
            print("2. Trenuj u trenera")
            print("3. Odpocznij w gospodzie")
            print("4. Wyjdź z miasta")
            wybor = input("Twój wybór: ")

            if wybor == "1":
                self.sklep.otworz(gracz)
            elif wybor == "2":
                self.trener.trenuj(gracz)
            elif wybor == "3":
                print("Odpoczywasz...")
                gracz.zycie = gracz.max_zycie
                gracz.many = gracz.max_mana
                print("Twoje życie i mana zostały całkowicie odnowione.")
            elif wybor == "4":
                print("Wychodzisz z miasta.")
                break
            else:
                print("Nieznana opcja.")

class Sklep:
    def __init__(self):
        self.przedmioty = [
            Przedmiot("Mikstura leczenia", "mikstura"),
            Przedmiot("Mikstura many", "mikstura"),
            Przedmiot("Mały miecz", "bron", sila=2),
            Przedmiot("Zbroja skórzana", "zbroja", zrecznosc=2),
        ]
        self.ceny = {
            "Mikstura leczenia": 10,
            "Mikstura many": 10,
            "Mały miecz": 30,
            "Zbroja skórzana": 25,
        }

    def otworz(self, gracz):
        print("\nWitamy w sklepie!")
        while True:
            print("\nDostępne przedmioty:")
            for i, p in enumerate(self.przedmioty):
                print(f"{i + 1}. {p.nazwa} - {self.ceny[p.nazwa]} złota")
            print("q. Wyjdź ze sklepu")

            wybor = input("Co chcesz kupić? ")
            if wybor == "q":
                break
            if not wybor.isdigit() or int(wybor) < 1 or int(wybor) > len(self.przedmioty):
                print("Zły wybór.")
                continue

            przedmiot = self.przedmioty[int(wybor) - 1]
            cena = self.ceny[przedmiot.nazwa]

            if gracz.zloto >= cena:
                gracz.zloto -= cena
                gracz.zaloz_przedmiot(przedmiot)
                print(f"Kupiłeś {przedmiot.nazwa}.")
            else:
                print("Nie masz wystarczająco złota.")

class Trener:
    def trenuj(self, gracz):
        print("\nWitaj u trenera!")
        print("Trening kosztuje 20 złota.")
        if gracz.zloto < 20:
            print("Nie masz wystarczająco złota.")
            return
        gracz.zloto -= 20
        gracz.sila += 1
        gracz.zrecznosc += 1
        gracz.inteligencja += 1
        print("Twoje statystyki zostały zwiększone!")

def losowe_wydarzenie(gracz):
    zdarzenie = random.choice(["walka", "znalezisko", "pułapka"])
    if zdarzenie == "walka":
        przeciwnik = wybierz_przeciwnika()
        menu_walki(gracz, przeciwnik)
    elif zdarzenie == "znalezisko":
        znalezione = random.choice(["zloto", "mikstura"])
        if znalezione == "zloto":
            ilosc = random.randint(10, 30)
            gracz.zloto += ilosc
            print(f"Znalazłeś {ilosc} złota!")
        elif znalezione == "mikstura":
            mik = Przedmiot("Mikstura leczenia", "mikstura")
            gracz.zaloz_przedmiot(mik)
            print("Znalazłeś miksturę leczenia!")
    elif zdarzenie == "pułapka":
        obrazenia = random.randint(5, 15)
        gracz.zycie -= obrazenia
        print(f"Wpadłeś w pułapkę! Tracisz {obrazenia} punktów życia.")

miasto = Miasto("Stary Gród")

def rozpocznij_przygode(gracz):
    while gracz.czy_zyje():
        print("\nCo chcesz zrobić?")
        print("1. Wyrusz na przygodę")
        print("2. Idź do miasta")
        print("3. Pokaż statystyki")
        print("4. Wyjście z gry")
        wybor = input("Wybór: ")

        if wybor == "1":
            losowe_wydarzenie(gracz)
        elif wybor == "2":
            miasto.odwiedz(gracz)
        elif wybor == "3":
            gracz.pokaz_statystyki()
        elif wybor == "4":
            print("Do zobaczenia, bohaterze!")
            break
        else:
            print("Nieznana komenda.")


klasy_potworow = [
    {"nazwa": "Wilk", "zycie": 30, "sila": 5, "nagroda": 15},
    {"nazwa": "Ork", "zycie": 45, "sila": 8, "nagroda": 25},
    {"nazwa": "Szaman", "zycie": 35, "sila": 6, "nagroda": 20},
    {"nazwa": "Upiór", "zycie": 50, "sila": 10, "nagroda": 30},
    {"nazwa": "Demon Cienia", "zycie": 80, "sila": 15, "nagroda": 60},
    {"nazwa": "Król Potworów", "zycie": 120, "sila": 20, "nagroda": 100},
]

def wybierz_przeciwnika():
    przeciwnik = random.choice(klasy_potworow)
    return Potwor(przeciwnik["nazwa"], przeciwnik["zycie"], przeciwnik["sila"], przeciwnik["nagroda"])

class Lokacja:
    def __init__(self, nazwa, trudnosc):
        self.nazwa = nazwa
        self.trudnosc = trudnosc

    def przeszukaj(self, gracz):
        print(f"\nWchodzisz do lokacji: {self.nazwa}")
        if random.random() < 0.7:
            potwor = wybierz_przeciwnika()
            print(f"W tej lokacji czai się {potwor.nazwa}!")
            menu_walki(gracz, potwor)
        else:
            print("Znalazłeś skarb!")
            ilosc = random.randint(10, 50)
            gracz.zloto += ilosc
            print(f"Zyskałeś {ilosc} sztuk złota.")

lokacje = [
    Lokacja("Las Cieni", trudnosc=1),
    Lokacja("Bagna Grozy", trudnosc=2),
    Lokacja("Jaskinia Krzyków", trudnosc=3),
    Lokacja("Ruiny Starego Królestwa", trudnosc=4),
    Lokacja("Piekielna Przełęcz", trudnosc=5),
]

def wybierz_lokacje():
    print("\nDostępne lokacje do zwiedzania:")
    for i, l in enumerate(lokacje):
        print(f"{i + 1}. {l.nazwa} (Poziom trudności: {l.trudnosc})")
    wybor = input("Wybierz lokację: ")
    if wybor.isdigit():
        wybor = int(wybor) - 1
        if 0 <= wybor < len(lokacje):
            return lokacje[wybor]
    print("Nieprawidłowy wybór. Spróbuj ponownie.")
    return None

def rozpocznij_przygode(gracz):
    while gracz.czy_zyje():
        print("\nCo chcesz zrobić?")
        print("1. Eksploruj nieznane tereny")
        print("2. Idź do miasta")
        print("3. Pokaż statystyki")
        print("4. Zakończ grę")
        wybor = input("Wybór: ")

        if wybor == "1":
            lokacja = wybierz_lokacje()
            if lokacja:
                lokacja.przeszukaj(gracz)
        elif wybor == "2":
            miasto.odwiedz(gracz)
        elif wybor == "3":
            gracz.pokaz_statystyki()
        elif wybor == "4":
            print("Dziękujemy za grę!")
            break
        else:
            print("Nieznana komenda.")

def ulepsz_postac(gracz):
    print("\nUlepszanie postaci:")
    print("1. Zwiększ życie (+10) za 30 złota")
    print("2. Zwiększ siłę (+2) za 40 złota")
    print("3. Zwiększ zręczność (+2) za 40 złota")
    print("4. Zwiększ inteligencję (+2) za 40 złota")
    wybor = input("Co chcesz ulepszyć? ")
    if gracz.zloto < 30:
        print("Nie masz wystarczająco złota.")
        return

    if wybor == "1":
        gracz.max_zycie += 10
        gracz.zycie = gracz.max_zycie
        gracz.zloto -= 30
        print("Twoje życie zostało zwiększone.")
    elif wybor == "2" and gracz.zloto >= 40:
        gracz.sila += 2
        gracz.zloto -= 40
        print("Twoja siła została zwiększona.")
    elif wybor == "3" and gracz.zloto >= 40:
        gracz.zrecznosc += 2
        gracz.zloto -= 40
        print("Twoja zręczność została zwiększona.")
    elif wybor == "4" and gracz.zloto >= 40:
        gracz.inteligencja += 2
        gracz.zloto -= 40
        print("Twoja inteligencja została zwiększona.")
    else:
        print("Niepoprawny wybór lub niewystarczająco złota.")

class Miasto:
    def __init__(self, nazwa):
        self.nazwa = nazwa
        self.sklep = Sklep()
        self.trener = Trener()

    def odwiedz(self, gracz):
        while True:
            print(f"\nWitaj w mieście {self.nazwa}. Co chcesz zrobić?")
            print("1. Odwiedź sklep")
            print("2. Trenuj u trenera")
            print("3. Ulepsz postać")
            print("4. Odpocznij w gospodzie")
            print("5. Wyjdź z miasta")
            wybor = input("Twój wybór: ")

            if wybor == "1":
                self.sklep.otworz(gracz)
            elif wybor == "2":
                self.trener.trenuj(gracz)
            elif wybor == "3":
                ulepsz_postac(gracz)
            elif wybor == "4":
                print("Odpoczywasz...")
                gracz.zycie = gracz.max_zycie
                gracz.many = gracz.max_mana
                print("Twoje życie i mana zostały całkowicie odnowione.")
            elif wybor == "5":
                print("Wychodzisz z miasta.")
                break
            else:
                print("Nieznana opcja.")



class EfektStatusu:
    def __init__(self, nazwa, trwanie, obrazenia_na_ture):
        self.nazwa = nazwa
        self.trwanie = trwanie
        self.obrazenia_na_ture = obrazenia_na_ture

    def zastosuj(self, cel):
        if self.trwanie > 0:
            print(f"{cel.nazwa} cierpi z powodu efektu: {self.nazwa}! Traci {self.obrazenia_na_ture} HP.")
            cel.zycie -= self.obrazenia_na_ture
            self.trwanie -= 1
        else:
            print(f"Efekt {self.nazwa} wygasł.")

class Zaklecie:
    def __init__(self, nazwa, koszt_many, obrazenia, efekt=None):
        self.nazwa = nazwa
        self.koszt_many = koszt_many
        self.obrazenia = obrazenia
        self.efekt = efekt

    def rzuć(self, rzucający, cel):
        if rzucający.many >= self.koszt_many:
            rzucający.many -= self.koszt_many
            print(f"{rzucający.nazwa} rzuca zaklęcie {self.nazwa} na {cel.nazwa}!")
            cel.zycie -= self.obrazenia
            print(f"{cel.nazwa} traci {self.obrazenia} punktów życia!")
            if self.efekt:
                cel.efekty.append(self.efekt)
                print(f"{cel.nazwa} został dotknięty efektem: {self.efekt.nazwa}")
        else:
            print("Za mało many!")


zaklecia = {
    "Kula Ognia": Zaklecie("Kula Ognia", koszt_many=10, obrazenia=20, efekt=EfektStatusu("Płonięcie", 3, 5)),
    "Strzała Lodu": Zaklecie("Strzała Lodu", koszt_many=8, obrazenia=15),
    "Błyskawica": Zaklecie("Błyskawica", koszt_many=12, obrazenia=25),
    "Leczenie": Zaklecie("Leczenie", koszt_many=10, obrazenia=-30),
}


class Gracz:
    def __init__(self, nazwa):
        self.nazwa = nazwa
        self.poziom = 1
        self.doswiadczenie = 0
        self.zycie = 100
        self.max_zycie = 100
        self.many = 30
        self.max_mana = 30
        self.sila = 5
        self.zrecznosc = 5
        self.inteligencja = 5
        self.zloto = 50
        self.ekwipunek = []
        self.efekty = []

    def pokaz_statystyki(self):
        print(f"\n{self.nazwa} - Poziom {self.poziom}")
        print(f"Życie: {self.zycie}/{self.max_zycie}")
        print(f"Mana: {self.many}/{self.max_mana}")
        print(f"Siła: {self.sila}, Zręczność: {self.zrecznosc}, Inteligencja: {self.inteligencja}")
        print(f"Złoto: {self.zloto}")
        print(f"Ekwipunek: {[item.nazwa for item in self.ekwipunek]}")

    def czy_zyje(self):
        return self.zycie > 0

    def zastosuj_efekty(self):
        for efekt in list(self.efekty):
            efekt.zastosuj(self)
            if efekt.trwanie <= 0:
                self.efekty.remove(efekt)

def menu_walki(gracz, przeciwnik):
    while gracz.czy_zyje() and przeciwnik.czy_zyje():
        print(f"\nTwoje HP: {gracz.zycie}/{gracz.max_zycie} | Mana: {gracz.many}/{gracz.max_mana}")
        print(f"{przeciwnik.nazwa} HP: {przeciwnik.zycie}")
        print("1. Atakuj fizycznie")
        print("2. Rzuć zaklęcie")
        print("3. Użyj przedmiotu")
        print("4. Ucieczka")

        wybor = input("Wybierz akcję: ")

        if wybor == "1":
            obrazenia = gracz.sila + random.randint(1, 6)
            przeciwnik.zycie -= obrazenia
            print(f"Zadajesz {obrazenia} obrażeń.")
        elif wybor == "2":
            print("Dostępne zaklęcia:")
            for i, z in enumerate(zaklecia):
                print(f"{i + 1}. {z} (Koszt: {zaklecia[z].koszt_many})")
            zak_wybor = input("Wybierz zaklęcie: ")
            if zak_wybor.isdigit() and 1 <= int(zak_wybor) <= len(zaklecia):
                wybrane = list(zaklecia.values())[int(zak_wybor) - 1]
                wybrane.rzuć(gracz, przeciwnik)
        elif wybor == "3":
            if gracz.ekwipunek:
                print("Twoje przedmioty:")
                for i, p in enumerate(gracz.ekwipunek):
                    print(f"{i + 1}. {p.nazwa}")
                uzycie = input("Wybierz przedmiot do użycia: ")
                if uzycie.isdigit():
                    idx = int(uzycie) - 1
                    if 0 <= idx < len(gracz.ekwipunek):
                        gracz.ekwipunek[idx].uzyj(gracz)
                        gracz.ekwipunek.pop(idx)
            else:
                print("Nie masz żadnych przedmiotów.")
        elif wybor == "4":
            if random.random() < 0.5:
                print("Udało Ci się uciec!")
                return
            else:
                print("Nie udało się uciec!")

        
        if przeciwnik.czy_zyje():
            gracz.zycie -= przeciwnik.sila
            print(f"{przeciwnik.nazwa} atakuje za {przeciwnik.sila}!")

        
        gracz.zastosuj_efekty()

    if gracz.czy_zyje():
        print(f"\nPokonałeś {przeciwnik.nazwa}!")
        gracz.zloto += przeciwnik.nagroda
        print(f"Otrzymujesz {przeciwnik.nagroda} złota.")
        gracz.doswiadczenie += 20
        print("Zdobywasz 20 punktów doświadczenia.")
    else:
        print("\nZostałeś pokonany...")



def awansuj(gracz):
    poziomy_exp = [0, 100, 250, 500, 800, 1200]
    if gracz.poziom < len(poziomy_exp) - 1:
        if gracz.doswiadczenie >= poziomy_exp[gracz.poziom]:
            gracz.poziom += 1
            gracz.max_zycie += 20
            gracz.max_mana += 10
            gracz.sila += 2
            gracz.zrecznosc += 1
            gracz.inteligencja += 1
            gracz.zycie = gracz.max_zycie
            gracz.many = gracz.max_mana
            print(f"\nGratulacje! Awansowałeś na poziom {gracz.poziom}!")
            print("Twoje statystyki zostały zwiększone.")
        else:
            print("Nie masz jeszcze wystarczająco dużo doświadczenia.")


class Boss(Potwor):
    def __init__(self, nazwa):
        super().__init__(nazwa, zycie=200, sila=25, nagroda=300)

def walka_z_bossem(gracz):
    print("\n=== OSTATNIA WALKA ===")
    boss = Boss("Władca Cieni")
    menu_walki(gracz, boss)

    if gracz.czy_zyje():
        print("\nZwyciężyłeś! Pokonałeś Władcę Cieni i uratowałeś krainę przed mrokiem.")
        print("Jesteś bohaterem! Koniec gry.")
    else:
        print("\nUpadłeś w walce z Władcą Cieni... Kraina pogrąża się w ciemności.")
        print("Koniec gry.")

def gra():
    print("=== GRA: WALKA Z POTWORAMI ===")
    imie = input("Podaj imię bohatera: ")
    gracz = Gracz(imie)

    potwory = [
        Potwor("Goblin", 30, 5, 10),
        Potwor("Wilk", 40, 7, 15),
        Potwor("Szkielet", 50, 9, 20),
        Potwor("Ork", 60, 11, 25),
        Potwor("Ognisty Smok", 100, 20, 100),
    ]

    przedmioty = [
        Przedmiot("Mikstura zdrowia", "lecz", 30),
        Przedmiot("Eliksir many", "mana", 20),
        Przedmiot("Stalowy Miecz", "atak", 5),
        Przedmiot("Amulet Magii", "int", 2)
    ]

    while gracz.czy_zyje():
        gracz.pokaz_statystyki()
        print("\nCo chcesz zrobić?")
        print("1. Wyrusz na walkę")
        print("2. Użyj przedmiotu")
        print("3. Awansuj postać")
        print("4. Idź do sklepu")
        print("5. Walka z bossem")
        print("6. Wyjście z gry")

        wybor = input("Wybierz opcję: ")

        if wybor == "1":
            przeciwnik = random.choice(potwory)
            print(f"\nWyruszasz na walkę z: {przeciwnik.nazwa}!")
            menu_walki(gracz, przeciwnik)
        elif wybor == "2":
            if gracz.ekwipunek:
                print("Twoje przedmioty:")
                for i, p in enumerate(gracz.ekwipunek):
                    print(f"{i + 1}. {p.nazwa}")
                wyb = input("Który chcesz użyć? ")
                if wyb.isdigit():
                    idx = int(wyb) - 1
                    if 0 <= idx < len(gracz.ekwipunek):
                        gracz.ekwipunek[idx].uzyj(gracz)
                        gracz.ekwipunek.pop(idx)
            else:
                print("Nie masz żadnych przedmiotów.")
        elif wybor == "3":
            awansuj(gracz)
       
        elif wybor == "4":
            walka_z_bossem(gracz)
            break
        elif wybor == "5":
            print("Opuszczasz grę. Do zobaczenia!")
            break
        else:
            print("Nieprawidłowy wybór.")

if __name__ == "__main__":
    gra()
