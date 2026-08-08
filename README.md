# PV-blueprint – negatieve stroomprijs regeling

Een Home Assistant automation blueprint voor het automatisch afschakelen en
herinschakelen van zonnepanelen op basis van de actuele elektriciteitsprijs.

## Werking

- **Prijs ≤ ingestelde drempel:** PV uit.
- **Prijs > ingestelde drempel:** PV aan.
- Ondersteunt een **geïnverteerde schakelaar** (`switch ON = PV UIT`).
- Minimale schakeltijd geldt alleen voordat PV wordt uitgeschakeld.
- Bij herstel van de prijs wordt PV direct weer ingeschakeld.
- Watchdog controleert iedere 5 minuten de gewenste en werkelijke toestand.
- Controle na een externe wijziging van de PV-schakelaar.
- Herstel na een herstart van Home Assistant.
- Temperatuurwaarschuwing voor Shelly/relais.
- Maximaal twee notify-services plus optionele persistent notification.

## Importeren

[![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fdkwolf1%2FPV-blueprint%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fpv_negative_price_control.yaml)

Of importeer handmatig via:

```text
https://github.com/dkwolf1/PV-blueprint/blob/main/blueprints/automation/pv_negative_price_control.yaml
```

Home Assistant kan blueprints rechtstreeks vanaf GitHub importeren.

## Benodigde helpers/entities

De blueprint verwacht:

- een sensor met actuele elektriciteitsprijs in €/kWh;
- een switch die de PV-installatie of contactor schakelt;
- `input_boolean` voor automatische regeling;
- `input_boolean` voor handmatige blokkering;
- `input_number` voor de afschakeldrempel;
- `input_datetime` voor het laatste schakelmoment;
- `input_number` voor de minimale inschakeltijd;
- een temperatuursensor van de Shelly/relais;
- optioneel één of twee notify-services.

### Voorbeeld voor de oorspronkelijke installatie

| Functie | Entity / waarde |
|---|---|
| Prijssensor | `sensor.zonneplan_current_electricity_tariff` |
| PV-schakelaar | `switch.shelly_pro_1` |
| Inverted | `true` |
| Automatische regeling | `input_boolean.pv_automatisch_afschakelen` |
| Handmatige blokkering | `input_boolean.pv_handmatig_geblokkeerd` |
| Prijsdrempel | `input_number.pv_negatieve_prijs_drempel` |
| Laatste schakelmoment | `input_datetime.pv_laatst_geschakeld` |
| Minimale inschakeltijd | `input_number.pv_minimale_schakeltijd_minuten` |
| Temperatuursensor | `sensor.shellypro1_a4f00fcb3cd0_temperatuur` |
| Temperatuurgrens | `55 °C` |
| Notify 1 | `notify.mobile_app_dennis` |
| Notify 2 | `notify.mobile_app_minke` |

## Inverted

De blueprint bevat een schakelaar **Inverted**.

- **Inverted AAN:** switch AAN = PV UIT, switch UIT = PV AAN.
- **Inverted UIT:** switch AAN = PV AAN, switch UIT = PV UIT.

Gebruik **Inverted AAN** wanneer een relais/Shelly de PV-installatie afschakelt zodra de switch wordt bekrachtigd.

## Schakellogica

Bij een drempel van `0.00 €/kWh`:

| Prijs | PV | Inverted switch |
|---:|---|---|
| `> 0` | AAN | OFF |
| `= 0` | UIT | ON |
| `< 0` | UIT | ON |

Bij bijvoorbeeld een drempel van `-0.02 €/kWh` blijft PV aan zolang de prijs
hoger is dan `-0.02 €/kWh`. Vanaf `-0.02 €/kWh` en lager wordt PV uitgeschakeld.

## Watchdog

Iedere 5 minuten, en na iedere wijziging van de PV-schakelaar, vergelijkt de
blueprint de werkelijke schakelstand met de toestand die bij de actuele prijs
hoort.

Bij een foutieve toestand:

1. wordt de schakelaar automatisch gecorrigeerd;
2. wordt het nieuwe schakelmoment opgeslagen;
3. wordt een melding verstuurd;
4. kan optioneel een Home Assistant persistent notification worden aangemaakt.

De watchdog grijpt niet in wanneer de handmatige blokkering actief is.

## Installatie

1. Klik op de importknop hierboven, of ga in Home Assistant naar
   **Instellingen → Automatiseringen & scènes → Blueprints → Blueprint importeren**.
2. Vul de GitHub-URL in.
3. Maak een automation vanuit de geïmporteerde blueprint.
4. Selecteer de juiste entities en services.
5. Sla de automation op.
6. Test eerst met de bestaande losse automations uitgeschakeld.

## Migreren van bestaande automations

Na succesvolle test kan deze blueprint de volgende losse automations vervangen:

- PV aan bij positieve stroomprijs
- PV uit bij negatieve stroomprijs
- PV foutief uit bij positieve stroomprijs
- PV herstel na herstart
- Shelly meterkast temperatuur te hoog

Schakel de oude automations eerst uit. Verwijder ze pas wanneer de blueprint
correct is getest.

## Updaten

Home Assistant onthoudt de importbron. Na een wijziging van de blueprint op
GitHub kun je in de Blueprint-interface **Re-import blueprint** gebruiken.

Bestaande automations die de blueprint gebruiken nemen de bijgewerkte blueprint
over nadat de automations opnieuw zijn geladen.

## Licentie

MIT License. Zie [LICENSE](LICENSE).
