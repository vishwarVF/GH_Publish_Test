
### US Debit AID Auto-Selection Logic for EMV Applications — ESAMDOCS-51222
**Tags:** emv, emv-contact, emv-ctls, card
**Components:** card

- **Q:** What is the core problem?  
  **A:** Enabling automatic selection of US Common Debit AIDs on EMV cards with multiple AIDs.

- **Q:** Where does it surface (signals)?  
  **A:** Cardholder is prompted for application selection when US Common Debit AIDs are present.

- **Q:** Which interfaces/config are relevant?  
  **A:** EMV_CT_GetCandidateData (39-16), candidate list callback, config key: Merchant->UsCommonDebitPreferredAids.

- **Q:** What is the safe fix pattern?  
  **A:** Detect US Common Debit AIDs and auto-select them; bypass cardholder prompt if present.

- **Q:** What should devs do/avoid?  
  **A:** Do: Apply regional rules to candidate list. Don’t: Rely on default prompt for US debit.

- **Q:** Any gotchas or edge cases?  
  **A:** Ensure country code and IIN confirm US debit; handle both contact and contactless flows.

- **Q:** Tiny snippet (optional)  
  **A:**  
  ```text
  if (countryCode == "US" && !iin.isNullOrEmpty()) {
    // Apply US Common Debit selection logic
  }
  ```

