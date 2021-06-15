# MyMendelu Blockchain Koncept
Základní koncept zapojení blockchainu do některých modulů MyMendelu jako nositele důvěry mezi zůčastněnými stranami. K tomuto účelu jsou výbrány moduly Gamifikace, kde probíhá testování studentů a modul OnBoardingu, kde se pracuje s lokací uživatele.
## Technologický background
K tomuto účelu je vybraný Bloxberg blockchian, což je fork Ethereum blockchainu. Jedná se o konsorcium autorizovaných nodů, které mají právo vytvářet nové bloky. Mendelova univerzita je jedním z členů. Na univerzitě je zprovozněn uzel, který má právo se podílet na konsensu blockchainu a vytváření a validaci nových bloků.

Bloxberg má i své [API](https://blockexplorer.bloxberg.org/api_docs). Neměl by tak být problém získat obsah blockchianu. U čeho si nejsem jistý je komunikace serveru a nodu. K manipulaci a např. vytváření transakcí jsem vybral knihovnu [web3j](https://github.com/web3j/web3j), která spojuje Ethereum node s Javovským backendem. Lze pomocí ní obalit smartkontrakty do javovské třídy a tak s nimi i pracovat. Co je otázka, nevím, jestli bude fungovat i na bloxberg.

Ověření tohoto Use case bude probíhat v jednotlivých modulech aplikace MyMendelu na platformě Android.
## Modul Gamifikace
Tento modul spočívá v tom, že vyučující na konci přednášky nebo cvičení může studentům zadat malý test ve formě otázek abcd, může se jednat o malý doplněk výuky, kde na výsledky nebude brán ohled nebo body z testu můžou sloužit vyučujícímu jako součást hodnocení. Jelikož modul není součástí uisu, je zapotřebí zajistit důvěryhodnost a pravdivost těchto testů, reps. jejich výsledků. K tomuto účelu by měl být vhodnám nástrojem právě blockchain.

Základní myšlenka bude spočívat v tom, že uživatel si bude moci ověřit (mít důkaz), že u každého vyplněného a odeslaného testu na server odpověděl na každou otázku konkrétním způsobem (lze ověřit, jestli odpověděl dobře nebo špatně) a tím měl i důkaz o počtu dosažených bodů v testu. Toho se docílí tak, že na blockchainu budou tyto odpovědi testu uloženy v transakci v nějaké takové podobě:

```json
{
  "user": "xivan nebo nejaky jiny (pseudo)anonymni identifikator uživatele",
  "test_transaction": "Transaction HASH",
  "test_id": "id testu",
  "answers": [
    {
      "questionID": 1,
      "asnwerHash": "HASH(otazka + odpoved + HASH(tajny string))"
    },
    {
      "questionID": 2,
      "asnwerHash": "HASH(otazka + odpoved) + HASH(tajny string))"
    }
  ],
  "time": "Timestamp"
}
```
Ještě před tím, než si uživatel stáhne test ze serveru, musí se nějaký test vytvořit, to se děje v administraci. Při vytváření testu se automaticky uloží jeho otisk na blockchain, jeho podoba může vypadat následovně:

```json
{
  "test_id": "id testu",
  "answers": [
    {
      "questionID": 1,
      "asnwerHash": "HASH(otazka + spravna odpoved + HASH(tajny string))"
    },
    {
      "questionID": 2,
      "asnwerHash": "HASH(otazka + spravna odpoved + HASH(tajny string))"
    }
  ],
  "time": "Timestamp"
}
```
Problém zde vidím, že pokud takhle budou uloženy testy na blockchianu se správnými výsledky a pouze zahashované, kdokoli může zjistit správnou možnost otázky, napadlo mě, ke každé odpovědi přidat nějaký tajný string, který vygeneruje server, zašifruje a pošle uživateli. Ten jej poté dešifruje a a ke každé odpovědi přidá, stejný string bude použitý i v odpovědích testu při jeho vytváření. Tento string by byl unikátní např. pro každý test.

![](https://github.com/ondrejivann/MyMendelu_Blockchain/blob/main/gamification_blockchain_scheme.png?raw=true)