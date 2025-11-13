
### Paper Detection After Out-of-Paper Event in Terminal Printer — ESAMDOCS-52728
**Tags:** android, comms
**Components:** android, comms

- **Q:** What is the core problem?  
  **A:** Terminal fails to detect new paper after out-of-paper event until reboot or print action.

- **Q:** Where does it surface (signals)?  
  **A:** Application logs repeated "Out of paper" status after paper is reloaded.

- **Q:** Which interfaces/config are relevant?  
  **A:** SdiPrinter.getPropertyInt(SdiPrinterPropertyInt.STATE); SPROC status query API.

- **Q:** What is the safe fix pattern?  
  **A:** Trigger a print or status query after paper reload to refresh detection.

- **Q:** What should devs do/avoid?  
  **A:** Do: Query status or print after reload. Don’t: Rely on passive detection.

- **Q:** Any gotchas or edge cases?  
  **A:** Continuous status polling may not trigger detection; hardware sensor issues possible.

- **Q:** Tiny snippet (optional)  
  **A:**  
  ```java
  SdiPrinter printer = getPrinter();
  SdiIntegerResponse state = printer.getPropertyInt(SdiPrinterPropertyInt.STATE);
  ```

