# Home Assistant s RGB LED páskem

Zdrojové kódy jsou převzaty od bruhautomation a jsou dostupné na adrese https://github.com/bruhautomation/ESP-MQTT-JSON-Digital-LEDs. 
Zdrojové kódy byly upraven tak, aby bylo možné projekt zkompilovat a nahrát do ESP32. Dále byly obohaceny o detekci pohybu pomocí PIR senzoru a předávání stavu senzoru platformě Home Assistant. Komunikace mezi platformou Home Assistant a ESP32 probíhá ve formátu JSON.

### Funkcionality
- Výběr několika světelných efektů 
- Výběr několika barevných efektů
- Změna rychlosti světelných efektů
- Detekci pohybu 
- Notifikace na Telegram
- Notifikace na Gmail
- Automatické zapnutí LED pásku při detekci pohybu
- Časovač pro vypnutí LED pásku
- Komunikace v JSON
![alt text](https://github.com/stuchlajz/pds/blob/master/HA.PNG?raw=true "Zapojení")

### Co budete potřebovat

- Arduino IDE
https://www.arduino.cc/en/main/software
- Instalace ESP32 Desky v Arduino IDE
http://randomnerdtutorials.com/installing-the-esp32-board-in-arduino-ide-windows-instructions/
- Instalace Home Assistant na Raspberry Pi
https://www.home-assistant.io/getting-started/
- Instalace Home Assistant na Linuxu
https://github.com/home-assistant/hassio-build/tree/master/install#install-hassio
- Nastavení MQTT
https://www.home-assistant.io/components/mqtt/
- Kdyby někdo nevěděl, o co se jedná, zde je zajímavý článek
https://www.root.cz/clanky/protokol-mqtt-komunikacni-standard-pro-iot/

### Zapojení
![alt text](https://github.com/stuchlajz/pds/blob/master/Zapojeni.png?raw=true "Zapojení")

#### Hardware
- Digitální RGB LED pásek
- Raspberry Pi nebo VM
- Napájecí zdroj 5V
- PIR senzor
- ESP32
- Nepájivé pole
- Drátky na propojení


### Notifikace
#### Gmail:
1)	Vytvořit si Gmail účet
2)	Navštívit stránku https://myaccount.google.com/lesssecureapps, kde povolíme méně bezpečné aplikace.
3)	Následně do souboru **notify.yaml** vložíme následující kód
```yaml
- name: SMTP_GMAIL
  platform: smtp
  server: smtp.gmail.com
  port: 587
  sender: váš vytvořený Gmail účet
  starttls: 1
  username: uživatelské jméno
  password: heslo
  recipient: 
    - seznam příjemců, kteří budou dostávat notifikace
  sender_name: My Home Assistant
  ```
4)	Dále budeme notifikace volat v automatizaci viz. **configuration.yaml**
#### Telegram:
1)	Nejprve je potřeba vytvořit nového bota na adrese https://web.telegram.org/#/im nebo v aplikaci telegram dáme vyhledat **botfather**
2)	Poté vytvoříme nového bota odesláním zprávy **/newbot**
3)	Dále budete vyzváni pro zadání jména bota a poté uživatelského jména. Uživatelské jméno musí obsahovat slovo **bot**
4)	Nyní obdržíte **API KEY**, který budete potřebovat dále
5)	Dále potřebujeme získat **CHAT ID**. Přejděte do konverzace s vaším nově vytvořeným botem a zadejte **/getid**
6)	V tuto chvíli byste měli obdržet vaše CHAT ID, které budeme potřebovat dále
7)	Do souboru **configuration.yaml** vyhledejte tyto řádky a vložte do nich váš **CHAT ID** a **API KEY**
```yaml
telegram_bot:
 - platform: polling
   api_key: VÁŠ API KEY
   allowed_chat_ids:
   - VÁŠ CHAT ID
```
8)	Dále v souboru **notify.yaml**  vyhledejte tyto řádky a vložte tam váš **CHAT ID**
```yaml
- platform: telegram
  name: telegram
  chat_id: VAŠE CHAT ID
  ```
První notifikace je odeslána pokaždé, když PIR senzor detekuje pohyb a následně je spuštěn odpočet přibližně 1 minuty. Po tuto dobu je pohyb signalizován pouze v Home Assistant. Po uplynutí tohoto času může být odeslána další notifikace. Jednotlivé notifikace je možné libovolně kombinovat nebo je vypnout úplně. 

