# slim-in-docker-hello-world

## Cél

**Docker konténeres környezetben szeretnék készíteni egy Slim framework projektet. Mindezt szeretném eltárolni egy GitHub repozitoriban.**

### 1. Docker konténeres környezet

Hordozható és könnyen konfigurálható fejlesztési környezetet biztosít. A Docker egy platform, amely lehetővé teszi, hogy alkalmazásokat és azok összefüggő komponenseit konténerekben futtasd. A konténerek izolált, hordozható környezetet biztosítanak, amelyben könnyen kezelhetőek a különböző alkalmazások, például a Slim framework.

- **Letöltés és telepítés:** [Docker Desktop](https://www.docker.com/products/docker-desktop)  
- **Dokumentáció és útmutatók:** [Docker Dokumentáció](https://docs.docker.com/)

### 2. Slim Framework projekt

Ideális választás, ha gyorsan szeretnél modern webes vagy API-alapú alkalmazást fejleszteni. A Slim egy könnyű, PHP alapú mikrokeretrendszer, amely gyors és hatékony webalkalmazások fejlesztésére alkalmas.

- **Hivatalos oldal:** [Slim Framework](https://www.slimframework.com/)  
- **Dokumentáció:** [Slim Dokumentáció](https://www.slimframework.com/docs/v4)

### 3. GitHub repozitoriban tárolás

A GitHub egy felhőalapú verziókezelő platform, amely lehetővé teszi a kódjaid tárolását, verziózását és másokkal való megosztását. A repozitori (repo) az a hely, ahol a projekt összes fájlja és annak előzményei tárolódnak.

- **GitHub regisztráció és hozzáférés:** [GitHub](https://github.com/)  
- **GitHub Desktop:** [GitHub Desktop](https://desktop.github.com/)

---

## Szint

**Abszolút kezdő**

---

## 1. GitHub repozitori elkészítése, klónozása a GitHub Desktoppal és a projekt megnyitása Visual Studio Code-ban

Ez a lépés bemutatja, hogyan hozhatsz létre egy GitHub repót, klónozhatod azt a gépedre a GitHub Desktop segítségével, és nyithatod meg Visual Studio Code-ban a fejlesztéshez.

### 1.1 Készíts a GitHub-on egy publikus repót

1. Lépj be a [GitHub](https://github.com/) oldalára, vagy regisztrálj egy fiókot, ha még nincs.
2. A jobb felső sarokban kattints a **"+" ikonra**, majd válaszd a **New repository** lehetőséget.
3. Töltsd ki a következő mezőket:
   - **Repository name:** Add meg a repó nevét, például `slim-in-docker-hello-world`.
   - **Public:** Válaszd a nyilvános opciót (ha mások számára elérhetővé akarod tenni).
4. Kattints a **Create repository** gombra.

### 1.2 Klónozd le a GitHub Desktop-pal

1. Töltsd le és telepítsd a [GitHub Desktop](https://desktop.github.com/) alkalmazást.
2. Indítsd el az alkalmazást, és jelentkezz be a GitHub fiókoddal.
3. A GitHub Desktop-ban válaszd a **File > Clone repository** menüt, vagy kattints a **Clone a repository** opcióra.
4. A listából válaszd ki az előzőleg létrehozott repót, vagy add meg annak URL-jét (például: `https://github.com/felhasznalonev/slim-in-docker-hello-world.git`).
5. Válaszd ki a helyi mappát, ahova klónozni szeretnéd a repót, majd kattints a **Clone** gombra.

### 1.3 Nyisd meg a Visual Studio Code-dal a mappát

1. Töltsd le és telepítsd a [Visual Studio Code](https://code.visualstudio.com/) alkalmazást.
2. Indítsd el a Visual Studio Code-ot.
3. Kattints a **File > Open Folder** menüpontra, és válaszd ki azt a mappát, ahova a repót klónoztad (például: `~/Documents/slim-in-docker-hello-world`).
4. A projekted most megnyílik, és készen állsz a fejlesztésre.

---

## 2. A konténer előkészítése a docker-compose.yml és a Dockerfile segítségével

### 2.1 docker-compose.yml

Hozd létre a `docker-compose.yml` fájlt a projekt gyökerében, és legyen ez a tartalma:

```yaml
services:
  app:
    image: php:8.2-apache
    container_name: slim-app
    ports:
      - "8080:80"
    volumes:
      - ./app:/var/www/html
    working_dir: /var/www/html
    build:
      context: .
      dockerfile: Dockerfile
```

#### Magyarázat

- **`services`**: Egy szolgáltatáscsoportot definiál. Itt az `app` nevű szolgáltatás a PHP alkalmazásunk.
- **`image: php:8.2-apache`**: A konténer a PHP 8.2-es verzióját és az Apache webszervert használja.
- **`container_name: slim-app`**: Meghatározza a konténer nevét.
- **`ports: "8080:80"`**: A 8080-as portot a gépen összeköti a konténer 80-as portjával, ahol az Apache fut.
- **`volumes: ./app:/var/www/html`**: A helyi `./app` mappa tartalmát hozzákapcsolja a konténer `/var/www/html` mappájához.
- **`working_dir: /var/www/html`**: Beállítja az alapértelmezett munkakönyvtárat a konténeren belül.
- **`build`**: Megadja, hogy a konténer képét helyben, a megadott Dockerfile alapján építse fel.

### 2.2 Dockerfile

Hozd létre a `Dockerfile` fájlt a projekt gyökerében, és legyen ez a tartalma:

```dockerfile
FROM php:8.2-apache

RUN apt-get update && apt-get install -y \
    unzip \
    git \
    libzip-dev && \
    docker-php-ext-install zip

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
```

#### Magyarázat

- **`FROM php:8.2-apache`**: Az alap image, amely tartalmazza a PHP 8.2-t és az Apache webszervert.
- **`RUN apt-get update && apt-get install -y ...`**: Telepíti a szükséges csomagokat:
  - **`unzip`**: Fájlok kicsomagolásához.
  - **`git`**: Verziókezeléshez (például a Composer függőségeihez).
  - **`libzip-dev`**: A ZIP támogatáshoz szükséges.
  - **`docker-php-ext-install zip`**: Aktiválja a PHP ZIP bővítményt.
- **`COPY --from=composer:latest /usr/bin/composer /usr/bin/composer`**: A legfrissebb Composer verziót egy másik Docker image-ből másolja át.

---

## 3. A konténer indítása és a Slim keretrendszer telepítése

### 3.1 Konténer indítása

```bash
docker-compose build
docker-compose up -d
```

- **`docker-compose build`**: A Dockerfile alapján felépíti az image-et.
- **`docker-compose up -d`**: Elindítja a konténert háttérben.

### 3.2 Slim Skeleton telepítése

```bash
docker exec -it slim-app bash
composer create-project slim/slim-skeleton .
```

- **`docker exec -it slim-app bash`**: Belépsz a konténerbe.
- **`composer create-project slim/slim-skeleton .`**: A Composer letölti és inicializálja a Slim Skeleton projektet az aktuális könyvtárban.

### 3.3 Apache mod_rewrite bekapcsolása

```bash
a2enmod rewrite
service apache2 restart
```

- **`a2enmod rewrite`**: Engedélyezi

 az Apache mod_rewrite modulját.
- **`service apache2 restart`**: Újraindítja az Apache-ot.

Ha a következő üzenetet kapod:  
**`Restarting Apache httpd web server: apache2Terminated`**,  
az azt jelenti, hogy az Apache nem indult újra megfelelően a konténeren belül.

#### Alternatív megoldás: rewrite modul automatikus engedélyezése a Dockerfile-ban

Ahelyett, hogy kézzel futtatnád a `a2enmod rewrite` parancsot minden újraépítéskor, automatizálhatod ezt a lépést a `Dockerfile`-ban. Írd hozzá a következő sort a `Dockerfile` végéhez:

```dockerfile
RUN a2enmod rewrite
```

Ezzel az Apache `mod_rewrite` modulja minden konténerindításkor automatikusan aktiválódik.

---

### 4. Kész! Hello World!

Ha minden helyesen van beállítva, mostantól a böngésződben megnyitva a **`http://localhost:8080`** címet, látnod kell a Slim Skeleton keretrendszer üdvözlőoldalát, amely megjeleníti a "Hello World!" szöveget.

#### További fejlesztés

A `./app` mappában dolgozhatsz, amely szinkronizálva van a konténer `/var/www/html` mappájával. Ez azt jelenti, hogy a fájlváltozások azonnal megjelennek a böngészőben frissítés után.

---

### 5. A docker-compose parancsokról

#### 5.1 `docker-compose down/up` = Újratelepítés

Ez hasonló ahhoz, mintha egy natív környezetet újratelepítenél.

- **`down`:**
  - Minden ideiglenes erőforrást (konténerek, hálózatok) eltávolít.
  - A volumeneket (adatokat) csak akkor törli, ha explicit megadod a `--volumes` kapcsolót.
  - Az újraindításkor (`up`) teljesen új konténerek jönnek létre a `docker-compose.yml` alapján.

- **`up`:**
  - Új konténereket hoz létre, mintha most kezdenéd a projektet.
  - Telepíti a szükséges függőségeket (például Composer vagy NPM).
  - Frissen és tisztán indul el minden.

#### 5.2 `docker-compose stop/start` = Hibernálás

Ez úgy működik, mint a számítógép alvó módja.

- **`stop`:**
  - Leállítja a konténereket, de megőrzi azok aktuális állapotát (például a fájlokat és a konfigurációkat).
  - A hálózat és a konténerkonfiguráció is megmarad.
  - Újraindítás után ott folytathatod, ahol abbahagytad.

- **`start`:**
  - Egyszerűen újraindítja a leállított konténereket az aktuális állapotukkal.
  - Gyors, mert nem építi újra a környezetet.

---

### 5.3 Mikor melyiket használd?

| **Cél**                                           | **Használandó parancs**                                |
|---------------------------------------------------|-------------------------------------------------------|
| Gyorsan folytatni a fejlesztést, ahol abbahagytad | `docker-compose stop` → `docker-compose start`        |
| Megszüntetni egy hibás vagy elavult környezetet   | `docker-compose down` → `docker-compose up`           |
| Tisztán újraépíteni a környezetet                 | `docker-compose down` → `docker-compose up --build`   |
| Csak ideiglenesen leállítani a konténereket       | `docker-compose stop`                                 |

---

### 5.4 Fontos tudni

1. **Volumenek (adattárolás):**
   - Ha a konténer használ volume-okat (pl. adatbázisok), azok megmaradnak még `down` után is, kivéve ha explicit törlöd őket a `--volumes` kapcsolóval.
   - Teljes újratelepítéshez használhatod:
     ```bash
     docker-compose down --volumes
     ```

2. **Újraépítés (`--build` kapcsoló):**
   - Ha változtatsz valamit a `Dockerfile`-ban, a környezet újraépítéséhez mindig használd:
     ```bash
     docker-compose up --build
     ```

3. **Adatok és állapotok:**
   - A `stop/start` megőrzi a konténerek belső állapotát és konfigurációját.
   - A `down/up` mindent alaphelyzetbe állít, kivéve a volume-ok adatait, hacsak nem törlöd azokat.

---

### 5.5 Röviden

- **`stop/start` = Hibernálás:** Gyors, mert semmi nem épül újra.
- **`down/up` = Újratelepítés:** Tiszta környezet, de minden konténer és hálózat újra létrejön.

---

### 6. Összefoglaló

Ezzel a dokumentációval egy teljes fejlesztési környezetet állíthatsz fel, és hatékonyan használhatod a Docker és Slim Framework nyújtotta előnyöket! Happy Coding!