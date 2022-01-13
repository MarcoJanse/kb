# Unable to access NAS drive when logged in using PIN to AzureAD account

This seems to be a bug and has been reported over and over: When you log in using a password everything works, when using a PIN, the NAS cannot be accessed. Here's a solution for it:

- Use the Windows search for "Credential Manager" and select "Windows credentials". 
- Remove any preexisting entry for your NAS and add a new windows credential entry using the following structure: 
  - Server adress: `\\NameOfYourNAS`
  - Username: `NameOfYourNAS\Username`
  - Password: `<password>`

Credits: [https://answers.microsoft.com/en-us/windows/forum/all/unable-to-access-nas-drive-when-logged-in-using/3587cf33-7ed9-403f-ac7c-d4158969412d()](Unable to access NAS drive when logged in using PIN to AzureAD account | Microsoft Answers)