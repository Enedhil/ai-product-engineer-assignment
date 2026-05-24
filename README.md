1. Problem Understanding & Scope (v1)
    A probléma: A marketingesek eddig csak a végső, összesített konverziós rátát látták (pl. 5%), de vakon voltak azzal kapcsolatban, hogy a több lépésből álló popup folyamatok melyik konkrét pontján lépnek ki a felhasználók.

    v1 Scope döntések: * Az egyszerűség és a fókuszált szállítás (Timebox: 4-6 óra) érdekében a JSON adatokat közvetlenül a frontend oldalon ágyaztam be. Ez feleslegessé tette egy külön adatbázis vagy bonyolult Node.js backend réteg kiépítését erre a fázisra, tökéletesen kiszolgálva a v1 üzleti céljait.

    Kihagytam a komplex grafikonrajzoló könyvtárakat (pl. Chart.js), helyette egy tisztán követhető, vertikális tölcsér-UX-et építettem fel Tailwind segítségével, ami egy nem-technikai marketinges számára is azonnal átlátható.

2. Solution & Architecture
    Az alkalmazás egy modern Vue 3 single-page alkalmazás, amely a legfrissebb Tailwind CSS v4 keretrendszert használja a reszponzív és letisztult UI-ért.

    Adatlogika: A konverziók kiszámítása dinamikusan történik az első lépés megtekintései (views) és az utolsó lépés sikeres továbblépései (proceeds) alapján.

    Problem Step Kiemelés: A kód automatikusan végigmegy a kampány lépésein, kiszámítja a százalékos lemorzsolódást (drop-off), és a legmagasabb értékkel rendelkező lépést piros figyelmeztető dobozzal és egyedi kártya-szegéllyel emeli ki.

    Szabályalapú ajánlások: Beépítettem egy kontextus-érzékeny ajánlási rendszert, ami figyeli a lemorzsolódás mértékét (50% felett kritikusnak jelöli), valamint azt, hogy az adott kampány mobil vagy desktop eszközre van-e optimalizálva, és ennek megfelelő marketing tippeket ad.

3. AI Tool Usage Disclaimer
    Mivel mérnökinformatikusként alapvetően IoT és Kotlin háttérrel rendelkezem, a webfejlesztési technológiák (Vue, Tailwind konfigurációk) terén magabiztos asszisztenciaként használtam az AI-t (Large Language Model).

    Az AI segített a modern Tailwind v4 és Vite pluginek kezdeti konfigurációs hibáinak elhárításában (CommonJS vs ES Modules ütközések feloldása).

    A tölcsér matematikai logikáját és a reaktív állapotkezelést (ref, computed property-k) közösen terveztük meg, biztosítva, hogy a kód ember által olvasható és könnyen karbantartható maradjon.

4. Future Improvements (v2)
    Ha több idő állna rendelkezésre, a következő funkciókkal bővíteném a terméket:

    Valódi Backend és Perzisztencia: Node.js/Express alapú REST API kialakítása, PostgreSQL vagy MongoDB adatbázissal a kampányadatok dinamikus lekérdezéséhez.

    Dinamikus AI Insights: Integráció egy LLM API-val (pl. OpenAI vagy Anthropic), ami a statikus szabályok helyett az aktuális számadatok alapján valós időben generálna egyedi konverzió-optimalizálási szövegeket a marketingeseknek.

    A/B Tesztelés vizualizáció: Lehetőség két azonos kampány tölcsérének egymás mellé tételére és összehasonlítására.

5. How to Run It (Futtatási útmutató)

Előfeltételek: Node.js: v18.x vagy újabb (ajánlott az LTS verzió)

Telepítés és indítás:

Töltsd le a forráskódot (vagy klónozd a GitHub repót).

Nyiss egy terminált a projekt gyökérmappájában (funnel-app).

Telepítsd a függőségeket:"npm install"

Indítsd el a fejlesztői szervert:"npm run dev"

Nyisd meg a böngésződben a terminál által kiírt URL-t (alapértelmezetten: http://localhost:5173/).
