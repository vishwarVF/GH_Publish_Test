---
title: Printer Paper Detection After Out-of-Paper Event
components: ["PSDK-SDI"]
primary_component: PSDK-SDI
author: abilash@verifone.com
ticket: ESAMDOCS-52728
---

# Printer Paper Detection After Out-of-Paper Event

### Printer Paper Detection After Out-of-Paper Event — ESAMDOCS-52728
**Tags:** android
**Components:** card

- **Q:** What is the core problem?  
  **A:** Terminal fails to detect new paper after an out-of-paper event until reboot or print attempt.

- **Q:** Where does it surface (signals)?  
  **A:** Application logs repeated "Out of paper" status after paper is reloaded; error code -4.

- **Q:** Which interfaces/config are relevant?  
  **A:** SdiPrinter.getPropertyInt(SdiPrinterPropertyInt.STATE); SPROC library status query API.

- **Q:** What is the safe fix pattern?  
  **A:** Trigger a print or status query after paper reload to update detection state.

- **Q:** What should devs do/avoid?  
  **A:** Do: Query printer status or print after paper reload. Don’t: Rely on passive detection.

- **Q:** Any gotchas or edge cases?  
  **A:** Sensor or port misconfiguration may cause intermittent detection; reboot resets state.

- **Q:** Tiny snippet (optional)  
  **A:**  
  ```java
  SdiPrinter printer = getPrinter();
  SdiIntegerResponse state = printer.getPropertyInt(SdiPrinterPropertyInt.STATE);
  ```


- Primary Component: PSDK-SDI
- Components: PSDK-SDI
- Timestamp: 1762977586
