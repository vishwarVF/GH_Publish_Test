
### US Debit AID Auto-Selection Logic for EMV Transactions — ESAMDOCS-51222
**Tags:** emv, emv-contact, emv-ctls, card
**Components:** card

- **Q:** What is the core problem?  
  **A:** Enabling automatic selection of US Common Debit AIDs on EMV cards with multiple AIDs.

- **Q:** Where does it surface (signals)?  
  **A:** Cardholder is not prompted for AID selection; terminal auto-selects US Common Debit AID if present.

- **Q:** Which interfaces/config are relevant?  
  **A:** PSDK-SDI EMV_CT_GetCandidateData (39-16, 40-16); config key: Merchant->UsCommonDebitPreferredAids.

- **Q:** What is the safe fix pattern?  
  **A:** Use candidate data to detect US region and auto-select US Common Debit AID; bypass cardholder prompt.

- **Q:** What should devs do/avoid?  
  **A:** Do: Apply regional rules to candidate list. Don’t: Rely on default selection or prompt if US AID is present.

