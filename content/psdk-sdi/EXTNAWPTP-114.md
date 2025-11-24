
### Handling LocalChecks Timeout and EMV Transaction Cleanup — EXTNAWPTP-114
**Tags:** emv, emv-contact, emv-ctls, security
**Components:** card, security

- **Q:** What is the core problem?  
  **A:** Transaction timeouts during LocalChecks cause EMV cleanup to fail, blocking subsequent transactions.

- **Q:** Where does it surface (signals)?  
  **A:** Error pattern: continueOffline returns "EMVSTATUS_ABORT" or "ERR_BUSY"; next transaction init fails.

- **Q:** Which interfaces/config are relevant?  
  **A:** Methods: `exit()`, `abort()`, `continueOffline`; config: SDI EMV callback, TLV 0xF0/0xDF76.

- **Q:** What is the safe fix pattern?  
  **A:** Send TLV "EA" to cancel in LOCAL_CHECKS, then wait for continueOffline to finish before cleanup.

- **Q:** What should devs do/avoid?  
  **A:** Do: Ensure continueOffline completes before exit. Don’t: Call exit/abort while SDI is busy.

- **Q:** Tiny snippet (optional)  
  **A:**  
  ```text
  output?.accessTlv()?.obtain(0xF0)?.obtain(0xDF76)?.assignBinary("EA".hexStringToByteArray())
  ```

