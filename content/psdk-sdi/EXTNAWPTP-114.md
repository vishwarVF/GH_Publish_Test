
### Handling EMV Transaction Timeout Cleanup Failures — EXTNAWPTP-114
**Tags:** emv, emv-contact, emv-ctls, comms
**Components:** card, comms

- **Q:** What is the core problem?  
  **A:** Transaction timeouts during local checks can prevent proper EMV cleanup, blocking subsequent transactions.

- **Q:** Where does it surface (signals)?  
  **A:** Error pattern: continueOffline returns "EMVSTATUS_ABORT" or "ERR_BUSY"; next transaction fails to initialize.

- **Q:** Which interfaces/config are relevant?  
  **A:** Methods: `exit()`, `endTransaction()`, `exitFramework()`, `continueOffline`, `abort()`; config: SDI EMV callback.

- **Q:** What is the safe fix pattern?  
  **A:** Do not call endTransaction or exitFramework within the callback. To exit, send EA TLV at the local checks break, call continueOffline with abort, then proceed to cleanup.

- **Q:** What should devs do/avoid?  
  **A:** Do: Wait for continueOffline to return abort before cleanup. Don’t: Call cleanup APIs inside the callback.

- **Q:** Tiny snippet (optional)  
  **A:**  
  ```java
  output?.accessTlv()?.obtain(0xF0)?.obtain(0xDF76)?.assignBinary("EA".hexStringToByteArray())
  ```

