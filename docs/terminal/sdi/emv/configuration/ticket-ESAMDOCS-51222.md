### US Common Debit AID Selection Logic for EMV Transactions — ESAMDOCS-51222
**Tags:** emv, emv-contact, emv-ctls, card
**Components:** card

- **Q:** What is the core problem?  
  **A:** Enabling automatic selection of US Common Debit AIDs on EMV cards with multiple AIDs.

- **Q:** Where does it surface (signals)?  
  **A:** Application selection phase during chip (contact/contactless) card transactions.

- **Q:** Which interfaces/config are relevant?  
  **A:** EMV_CT_GetCandidateData (39-16), candidate list callback, config key: Merchant->UsCommonDebitPreferredAids.

- **Q:** What is the safe fix pattern?  
  **A:** Detect US region, auto-select US Common Debit AID if present, bypass cardholder prompt.

- **Q:** What should devs do/avoid?  
  **A:** Do: Apply regional rules to candidate list. Don’t: Rely on default prompt for US cards.

- **Q:** Tiny snippet (optional)  
  **A:**  
  ```text
  if (countryCode == "US" && !iin.isNullOrEmpty()) {
    // Apply US Common Debit selection logic
  }
  ```
