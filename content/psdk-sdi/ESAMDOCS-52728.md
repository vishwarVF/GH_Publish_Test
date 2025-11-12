
### Printer Paper Detection After Out-of-Paper Event — ESAMDOCS-52728
**Tags:** android, comms
**Components:** comms, android

- **Q:** What is the core problem?  
  **A:** Terminal fails to detect new paper after an out-of-paper event until reboot or print attempt.

- **Q:** Where does it surface (signals)?  
  **A:** Persistent "out of paper" state; log: `Printer state: -4` after paper reload.

- **Q:** Which interfaces/config are relevant?  
  **A:** `SdiPrinter.getPropertyInt(SdiPrinterPropertyInt.STATE)`; print activity APIs; SPROC library status query.

- **Q:** What is the safe fix pattern?  
  **A:** Trigger a print or explicit status query after paper reload to refresh detection.

- **Q:** What should devs do/avoid?  
  **A:** Do: Query status or print after reload. Don’t: Assume auto-detection without activity.

- **Q:** Any gotchas or edge cases?  
  **A:** Faulty or dirty paper sensor can cause intermittent detection failures.

- **Q:** Tiny snippet (optional)  
  **A:**  
  ```java
  SdiPrinter printer = getPrinter();
  SdiIntegerResponse state = printer.getPropertyInt(SdiPrinterPropertyInt.STATE);
  ```

