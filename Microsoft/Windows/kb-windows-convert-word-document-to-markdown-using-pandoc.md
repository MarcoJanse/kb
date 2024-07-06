# How to convert a Microsoft Word document to Markdown using Pandoc

## Pandoc install

Pandoc can be installed on Windows using Winget:

```powershell
winget install pandoc
```

## Convert Word document to Pandoc

- Open PowerShell or command prompt
- Change working directory to where you want to generate the markdown file
- Use the following command to convert a Word doc to Markdown
  - `pandoc -s <Word file name>.docx -t markdown --extract-media=images -o <Markdown filename>.md`
