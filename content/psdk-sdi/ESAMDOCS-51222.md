
### US Debit AID Auto-Selection Logic for EMV Transactions — ESAMDOCS-51222
**Tags:** emv, emv-contact, emv-ctls, card
**Components:** card

- **Q:** What is the core problem?  
  **A:** Enabling automatic selection of US Common Debit AIDs on EMV cards with multiple AIDs.

- **Q:** Where does it surface (signals)?  
  **A:** Application selection phase during chip (contact/contactless) transaction; candidate list includes US Debit AIDs.

- **Q:** Which interfaces/config are relevant?  
  **A:** EMV_CT_GetCandidateData (39-16, 40-16); config key: Merchant->UsCommonDebitPreferredAids; DF36 transaction options.

- **Q:** What is the safe fix pattern?  
  **A:** Detect US region, auto-select US Debit AID if present, bypass cardholder prompt for contact; enable callback for CTLS.

- **Q:** What should devs do/avoid?  
  **A:** Do: Apply regional rules to candidate list. Don’t: Rely on default prompt if auto-selection is required.

- **Q:** Tiny snippet (optional)  
  **A:**  
  ```text
  if (countryCode == "US" && !iin.isNullOrEmpty()) {
    // Apply US Common Debit selection logic
  }
  ```

