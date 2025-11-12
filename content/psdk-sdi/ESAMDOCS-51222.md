
### US Debit AID Auto-Selection Logic for EMV Applications — ESAMDOCS-51222
**Tags:** emv, emv-contact, emv-ctls, card
**Components:** card

- **Q:** What is the core problem?  
  **A:** Enabling automatic selection of US Common Debit AIDs on EMV cards with multiple AIDs.

- **Q:** Where does it surface (signals)?  
  **A:** Application selection phase during chip (contact/contactless) transactions; candidate list includes multiple AIDs.

- **Q:** Which interfaces/config are relevant?  
  **A:** EMV_CT_GetCandidateData (39-16); config key: Merchant->UsCommonDebitPreferredAids; DF36 transaction options.

- **Q:** What is the safe fix pattern?  
  **A:** Detect US region, auto-select US Common Debit AID if present, bypass cardholder prompt.

- **Q:** What should devs do/avoid?  
  **A:** Do: Apply regional rules to candidate list. Don’t: Rely on default prompt for US debit selection.

- **Q:** Any gotchas or edge cases?  
  **A:** Ensure country code and IIN confirm US debit; handle both contact and contactless flows.

- **Q:** Tiny snippet (optional)  
  **A:**  
  ```text
  if (countryCode == "US" && !iin.isNullOrEmpty()) {
    // Apply US Common Debit rules
  }
  ```

