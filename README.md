# Lab 06b - Andra Switch

Andra on uus IT-tugi praktikant. Tema esimene ülesanne: serveriruumis on Cisco kommutaator, mille seadistust keegi ei tea. Andra peab ühendama oma arvuti kommutaatoriga, logima sisse ja seadistama selle nii, et kõik teaksid, mis seade see on ja et suvaline inimene sinna sisse ei pääseks.

Seni tegid sa seda Packet Traceris. Täna oled sina Andra ja teed sama **serveriruumi Cisco kommutaatoriga**.

Töötate **grupis (2–3 inimest)**. Poole pealt vahetate rollid, et igaüks trükiks ise käske.

| Roll | Kus | Mida teeb |
|---|---|---|
| Serveriruum | serveriruumis, kapp K1 | ühendab kaabli patch-paneelilt kommutaatori CONSOLE porti, teeb foto `kaabel.jpg` |
| Konsool | klassis arvuti taga | PuTTY, trükib käsud |
| Kirjutaja (3. inimene) | klassis | teeb ekraanipildid, märgib üles kommutaatori numbri |

Esitab **igaüks** oma repos. Grupi pildid võivad olla samad, vastused kirjutad ise.

Enne alustamist vaata: [video PuTTY ühendusest](https://drive.google.com/file/d/1y2phgdB0MZ0k9rTrmo_ES2ae5jxHEo1a/view?usp=sharing) ja [juhend: klassi arvuti, konsoolikaabel, PuTTY](https://docs.google.com/document/d/12NH_-liylELLgUhspfoTXxHba21JFe0_eW3Jok0Zffk/edit?usp=sharing). Kõik sammud on ka saidil: <https://optalvik.github.io/avalused/praktikumid/praktikum_06b.html>

> **Serveriruumis** puutu ainult oma grupi kommutaatorit ja sinist konsoolikaablit. Teisi kaableid ära tõmba välja: nendest käib kooli võrk.

**Parool on kõikjal `1234`.**

---

## Kuidas ühendus käib

```
Sinu arvuti ──(konsoolikaabel, USB → RJ-45)── laua pesa (nt 21.1)
      … seina sees …
Serveriruum, kapp K1: patch-paneeli pesa 21.1 ──(sinine kaabel)── kommutaatori CONSOLE port
```

**Konsoolikaabel** (helesinine) ei ole võrgukaabel. Selle kaudu räägid kommutaatoriga otse, ka siis, kui tal pole veel IP-aadressi.

---

## Samm 1. Ühenda kaabel

| | |
|---|---|
| **Tee** | Klassis: konsoolikaabli USB-ots arvutisse, RJ-45 ots laua pessa. Kirjuta üles pesa number. Serveriruumis: leia kapist K1 patch-paneelilt sama number ja ühenda sinine kaabel sealt kommutaatori **CONSOLE** porti. |
| **Näed** | Kommutaatori SYST-tuluke põleb roheliselt. |
| **Miks** | Kaabel peab olema katkematu: arvuti → sein → paneel → kommutaatori konsool. |
| **Tõend** | 📷 foto kommutaatorist, kus on näha konsoolikaabel CONSOLE pordis → `kaabel.jpg` |

## Samm 2. Leia COM-port

| | |
|---|---|
| **Tee** | Windowsis: paremklõps Start → **Device Manager** → **Ports (COM & LPT)**. |
| **Näed** | Rida nagu `USB Serial Port (COM3)`. Number võib olla muu. |
| **Miks** | PuTTY peab teadma, millise pordi kaudu kaabel arvutiga ühendatud on. |

## Samm 3. Ava PuTTY

| | |
|---|---|
| **Tee** | Connection type: **Serial**. Serial line: **COM3** (sinu number). Speed: **9600**. Vajuta **Open**. |
| **Näed** | Must aken. Esialgu võib see olla tühi. |
| **Miks** | Cisco konsool räägib kiirusega 9600 bitti sekundis. Vale kiiruse korral näed segast teksti või mitte midagi. |
| **Tõend** | 📷 ekraanipilt PuTTY seadetest enne Open vajutamist → `putty.png` |

## Samm 4. Ärata terminal

| | |
|---|---|
| **Tee** | Vajuta **Enter** paar korda. |
| **Näed** | `Switch>` või küsimus `Would you like to enter the initial configuration dialog? [yes/no]:`. Kui tuleb küsimus, kirjuta `no` ja vajuta Enter. Kui küsitakse parooli, kirjuta `1234`. |
| **Miks** | Konsool ei näita midagi enne, kui saadad esimese klahvivajutuse. |

> **Kui midagi ei tule:** kontrolli COM-numbrit ja kiirust 9600. Kas kaabel on serveriruumis CONSOLE pordis, mitte tavalises pordis?

## Samm 5. Seadista kommutaator

Kirjuta käsud ükshaaval. `XX` asemel kirjuta oma eesnimi (Andra kommutaator saab sinu nime).

```
enable
configure terminal
hostname SW-XX
enable secret 1234
line console 0
 password 1234
 login
 exit
banner motd # Andra switch - ainult IT-26 #
end
show running-config
```

| | |
|---|---|
| **Näed** | Prompt muutub: `SW-XX#`. `show running-config` näitab sinu hostname'i ja `enable secret 5 ...` (parool on krüpteeritud). |
| **Miks** | Hostname näitab, millise seadmega töötad. Paroolid kaitsevad kommutaatorit. |
| **Tõend** | 📷 ekraanipilt `show running-config` algusest → `show-run.png`. Kopeeri sealt ka read allpool olevasse README tabelisse. |

## Samm 6. Kontrolli paroole

Kirjuta `exit`, vajuta Enter. Kommutaator küsib nüüd parooli: kirjuta `1234`. Siis `enable` ja jälle `1234`.

## Samm 7. Tee kommutaator puhtaks (järgmise grupi jaoks)

```
enable
write erase
reload
```

Kui küsitakse `Proceed with reload? [confirm]`, vajuta Enter. Kui küsitakse `Save? [yes/no]`, kirjuta `no`.

## Kui unustasid parooli: reset nupuga

Kommutaatori esiküljel vasakul on nupp **MODE**. Hoia seda **15 sekundit all**, kuni tuled vilguvad **oranžilt**, ja lase lahti. Kommutaator kustutab seadistuse ja taaskäivitub. Pärast seda alusta uuesti sammust 4.

---

## Esitamine (brauseris)

1. **Add file → Upload files:** `kaabel.jpg`, `putty.png`, `show-run.png`
2. Ava see fail, vajuta pliiatsit ✏️, täida allolev osa ja vajuta **Commit changes**.

**Sinu nimi:** 

**Grupikaaslased:** 

**Kommutaatori number (valge kleebis):** 

**Read `show running-config` väljundist:**

| Rida | Mis seal oli |
|---|---|
| hostname | |
| enable secret | |

## Küsimused

**1. Mis kiirusega (baud) Cisco konsool töötab?**
> 

**2. Miks konsooliühendus töötab ka siis, kui kommutaatoril pole IP-aadressi?**
> 

**3. Mis vahe on paroolil `enable secret` ja paroolil `line console 0` all?**
> 

**4. Mida teeb MODE-nupp, kui seda 15 sekundit all hoida?**
> 
