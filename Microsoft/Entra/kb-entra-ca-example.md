# CA example

- Name: 00 - MFA - CA - Prod
- **Assignments:**
  - Users
    - Included: All users
    - Exclued: Emergency account excluded
  - Target resources:
    - IncludedL All cloud apps
    - Excluded:
      - Microsoft Intune
      - Microsoft Intune Enrollment
  - Conditions:
    - Device platforms: Any Device
- **Access Controls:**
  - Grant
    - Require multifactor authentication