# Lab Writeup: DOM XSS using web messages
# 📚 Studijní materiál
Tento repozitář slouží jako můj osobní studijní zápisník a přehled řešení laboratoří z platformy PortSwigger Web Security Academy.

Tento dokument popisuje úspěšné řešení laboratoře z PortSwigger Web Security Academy zaměřené na **DOM XSS zneužívající webové zprávy (`postMessage`)**.

---

## 🎯 O co šlo (Princip zranitelnosti)
Cílová webová stránka obsahovala JavaScriptový kód, který naslouchal příchozím zprávám pomocí event listeneru `window.addEventListener('message', ...)`. 

Aplikace přicházející zprávu zpracovala a nebezpečným způsobem ji vložila do DOM struktury (například přes `innerHTML`). Útočník mohl skrze rozhraní `postMessage` poslat vlastní řetězec obsahující HTML/JavaScript kód, který se po zpracování v prohlížeči oběti automaticky vykonal.

---

## 🛠️ Použitý postup (Steps)

1. **Otevření Exploit serveru:** V laboratoři jsem přešel na přidělený Exploit server.
2. **Vložení payloadu do Body:** Do pole **Body** jsem zapsal exploit využívající HTML tag `iframe` a funkci `postMessage`:
   ```html
   <iframe src="https://YOUR-LAB-ID.web-security-academy.net/"
onload="this.contentWindow.postMessage('<img src=1
onerror=alert(document.cookie)>','*')">

Uložení (Store): Nastavení jsem uložil kliknutím na tlačítko Store[cite: 5].

Doručení oběti (Deliver exploit to victim): Kliknutím na tlačítko pro odeslání oběti se simulovaný uživatel (oběť) podíval na stránku s obsahem mého iframe

# 💡 Proč to fungovalo?

Vytvoření kontextu (iframe): Stránka na Exploit serveru načetla cílovou zranitelnou aplikaci uvnitř neviditelného iframe.

Odeslání zprávy (postMessage): Jakmile se iframe načetl (onload), skript v něm okamžitě odeslal škodlivý payload na cílovou doménu bez ohledu na původ (zde nastaveno jako wildcard '*').

Trigger XSS (onerror): Cílová stránka vzala text zprávy a vložila ho do stránky. Payload obsahoval obrázek s neplatnou cestou (src=1), což okamžitě vyvolalo chybu a spustilo událost onerror, která provedla JavaScriptový kód alert(document.cookie).