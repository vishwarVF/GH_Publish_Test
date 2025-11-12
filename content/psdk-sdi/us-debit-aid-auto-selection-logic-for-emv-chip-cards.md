
### US Debit AID Auto-Selection Logic for EMV Chip Cards — ESAMDOCS-51222
**Tags:** emv, emv-contact, emv-ctls, card
**Components:** card

- **Q:** What is the core problem?  
  **A:** Enabling automatic selection of US Common Debit AIDs on EMV chip cards with multiple AIDs.

- **Q:** Where does it surface (signals)?  
  **A:** Cardholder not prompted for AID selection; terminal auto-selects US Common Debit AID if present.

- **Q:** Which interfaces/config are relevant?  
  **A:** EMV_CT_GetCandidateData (39-16, 40-16); config key: Merchant->UsCommonDebitPreferredAids; DF36 transaction options.

- **Q:** What is the safe fix pattern?  
  **A:** Detect US region, auto-select US Common Debit AID, bypass cardholder prompt if rules allow.

- **Q:** What should devs do/avoid?  
  **A:** Do: Apply regional rules to candidate list. Don’t: Rely on default selection for US debit.

- **Q:** Any gotchas or edge cases?  
  **A:** Ensure IIN and issuer country code confirm US debit; handle both contact and contactless flows.

- **Q:** Tiny snippet (optional)  
  **A:**  
  ```java
  val candidateList = sdiManager.emvCt.candidateData
  if (candidateList.result == OK) {
    for (c in candidateList.canData) {
      if (c.country2 == "US") { /* apply US Debit rules */ }
    }
  }
  ```

