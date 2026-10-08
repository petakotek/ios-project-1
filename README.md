# bootutil - správa položek zavaděče

První projekt do předmětu **IOS (Operační systémy)** na FIT VUT z roku **2025**. Cílem bylo vytvořit shellový nástroj `bootutil` pro správu zaváděcích položek systému Linux.

Finální hodnocení: 15/15.

## O projektu

Projekt se zabýval konfigurací zavaděče, který při spuštění počítače načítá operační systém. Zadání vycházelo ze zjednodušené podoby **Boot Loader Specification**, kde je každá zaváděcí položka uložena v samostatném textovém souboru s příponou `.conf`.

Úkolem bylo zpracovávat tyto soubory, vypisovat a filtrovat dostupné položky, vytvářet jejich upravené kopie, odstraňovat je a nastavovat výchozí položku. Součástí byla také práce s parametry předávanými linuxovému jádru.

## Funkce nástroje

| Příkaz | Účel |
| --- | --- |
| `list` | Výpis zaváděcích položek s možností řazení a filtrování. |
| `remove <title-regex>` | Odstranění položek podle regulárního výrazu odpovídajícího jejich názvu. |
| `duplicate [<entry_file_path>]` | Vytvoření kopie zadané nebo výchozí položky s možností úprav. |
| `show-default` | Zobrazení obsahu výchozí položky; s přepínačem `-f` pouze cesty k jejímu souboru. |
| `make-default <entry_file_path>` | Nastavení zvolené položky jako jediné výchozí. |

Příkaz `list` umožňuje řazení podle názvu souboru (`-f`) nebo hodnoty `sort-key` (`-s`). Výsledky lze filtrovat rozšířenými regulárními výrazy podle názvu položky (`-t`) a cesty k jádru (`-k`).

Při duplikování lze změnit název (`-t`), cestu k jádru (`-k`) a počátečnímu ramdisku (`-i`), přidávat (`-a`) či odebírat (`-r`) parametry jádra a zvolit cílový soubor (`-d`). Přepínač `--make-default` označí novou položku jako výchozí.

## Formát konfigurace

Konfigurační soubory obsahují dvojice klíčů a hodnot. Zpracovávaná pole jsou `title`, `version`, `linux`, `initrd`, `options` a volitelná pole `sort-key` a `vutfit_default`.

Pole `vutfit_default` bylo zavedeno speciálně pro tento projekt a není součástí standardní Boot Loader Specification. Hodnota `y` označuje výchozí položku. Pokud se některý klíč v souboru opakuje, rozhoduje jeho poslední výskyt.

## Použití

Obecný tvar spuštění:

```sh
./bootutil [-b <boot_entries_dir>] <příkaz> [argumenty]
```

Výchozí adresář s položkami je `/boot/loader/entries`. Přepínačem `-b` lze zadat vlastní adresář, například s testovacími konfiguracemi. Následující ukázky předpokládají existující adresář `./entries` s položkami `.conf`:

```sh
# Výpis položek seřazených podle sort-key
./bootutil -b ./entries list -s

# Vyhledání položek podle názvu
./bootutil -b ./entries list -t 'Fedora.*'

# Zobrazení cesty k výchozí položce
./bootutil -b ./entries show-default -f

# Kopie výchozí položky s novým názvem a upravenými parametry jádra
./bootutil -b ./entries duplicate -t 'Linux debug' -r quiet -a debug
```

## Co projekt procvičoval

- Skriptování v shellu a práci s unixovými textovými nástroji.
- Zpracování argumentů příkazové řádky, textových souborů a regulárních výrazů.
- Řazení a filtrování záznamů podle více pravidel.
- Zpracování parametrů jádra včetně uvozovek, escapování a hodnot obsahujících mezery.
- Úpravy souborů bez nechtěného přepsání existujících položek a zachování jediné výchozí položky.

Zadání vyžadovalo řešení v shellu bez použití jazyků jako Python, Perl nebo Ruby. Důraz kladlo na přesné dodržení rozhraní příkazů a správné chování i v okrajových případech.
