
### US Debit AID Auto-Selection Logic for EMV Transactions — ESAMDOCS-51222
**Tags:** emv, emv-contact, emv-ctls, card
**Components:** card

- **Q:** What is the core problem?  
  **A:** Enabling automatic selection of US Common Debit AIDs on EMV cards with multiple AIDs.

- **Q:** Where does it surface (signals)?  
  **A:** Application selection phase during chip (contact/contactless) transactions; candidate list includes multiple AIDs.

- **Q:** Which interfaces/config are relevant?  
  **A:** EMV_CT_GetCandidateData (39-16, 40-16); config key: Merchant->UsCommonDebitPreferredAids; DF36 transaction options.

- **Q:** What is the safe fix pattern?  
  **A:** Detect US region, auto-select US Common Debit AID if present, bypass cardholder prompt.

- **Q:** What should devs do/avoid?  
  **A:** Do: Apply regional rules to candidate list. Don’t: Rely on default prompt for US debit selection.

- **Q:** Tiny snippet (optional)  
  **A:**  
  ```java
  val candidateListResponse = sdiManager.emvCt.candidateData
  if(SdiResultCode.OK == candidateListResponse.result && candidateListResponse.canData.isNotEmpty()) {
    for (candidateData in candidateListResponse.canData) {
      val countryCode = candidateData.country2?.toHexString()
      if (countryCode == "US") {
        // Apply US Common Debit rules
      }
    }
  }
  ```

