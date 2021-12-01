# cardano-sport-nft

### Generování artu

Od grafika dostaneme assets, ze kterých budeme generovat výsledný art.
Volba jazyka není nějak důležitá, protože je tohle spíš "jednorázový" skript.
Letmým hledáním jsem našel, že někdo k tomu [používá python module PIL](
https://stackoverflow.com/questions/54499763/python-pil-merge-multiple-layers-of-images-into-one/54499942).
Nicméně bude třeba myslet na to, že generování není pouze o skládání obrázků na sebe,
ale také o správném rozložení assetů mezi všechen art podle rarit.

Výstupem programu na generování artu budou samotný obrázky a metadata k nim.
Pojmem metadata je myšleno pojmenování všech assetů v artu s jejich raritami a
možná i něco víc.

### Publikování artu

Art bychom mohli ukládat na [ipfs](https://ipfs.io/),
což je bezplatná decentralizovaná databáze pro různé dokumenty.
Pokud na ipfs uložíme obrázek, dostaneme adresu na něj,
se kterou umí pracovat [pool.pm](pool.pm).

### Mintování

#### Flow

Zákazník pošle adu na naši walletku.
Pokud částka, kterou obdržíme, nebude ta, kterou budeme chtít,
částku refundneme a tím transakce končí.
Pokud bude sedět, částku rozdělíme na náš profit,
adu potřebnou k poslání nftčka zpátky zákazníkovi a pošleme spolu s mintovanim nft.
Základem je vyčerpat celou transakci, kterou nám zákazník pošle,
abychom "odebírali" z walletky všechny dokončené transakce.

#### Technické specifikace

Pro tvorbu programu na mintovaní bychom měli určitě použít
[cardano-cli spolu s cardano-node](https://developers.cardano.org/docs/get-started/installing-cardano-node/).
Abychom mohli zjistit zákazníkovu adresu, na kterou máme nft poslat,
potřebujeme k tomu mít stáhnutý celý blockchain, což zajišťuje
[cardano-db-sync](https://github.com/input-output-hk/cardano-db-sync).
Budeme potřebovat databázi pro příchozí transakce,
abychom si k nim mohli uložit jejich výchozí adresu, počet ady,
časovou známku příchodu, a stav dokončení.
S těmito informace jsme schopní bez dalšího sběru informací vymintit nft a
odeslat ho zpátky s potřebnou adou a ještě k tomu poslat profit na naši profit walletku
pouze v jedné transakci.
K mintování samotného nft potřebujeme jejich metadata.
Ty budou uložený v další databázi.

#### Problémy

Klasickým problémem je rychlost mintování.
Abychom si totiž byli jistí, že transakce prošla a
mohli se vrhnout na dalšího zákazníka,
musíme si počkat na potvrzení mintovací transakce.
To může zabrat 1-2 bloky průměrně.
Proto je potřeba uvažovat o paralelizaci celého flow a
zajištění ACID pro transakce v našem kódu.

Někdo by mohl navrhnout, že náš flow zrychlíme tím,
že budeme mintovat víc nfts naráz a pak je pošleme zákazníkům postupně.
Tohle bz možná mohlo fungovat, ale nejsem si jistý, že to bude rychlejší.
Je třeba totiž myslet na to,
že jak mintování a poslání v jedné transakci je pořád jedna transakce,
tak i pouhé poslání předmintovaného nft.
Proto je třeba na oboje čekat 1-2 bloky a časově si moc nepomůžeme.

Je třeba myslet na to, že při mintování na ostro se může cokoliv vysrat a
je nutný, abychom byli schopní to co nejrychleji fixnout.
K tomu se váže to, že již zmíněný ACID by nám měl zaručit,
že bychom měli být schopní cokoliv fixnout v jakémkoliv okamžiku.
Jen je třeba program vytvářet s tímhle mindsetem.




