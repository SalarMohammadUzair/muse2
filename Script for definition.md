```bash
#!/usr/bin/env bash

word=${1:-$(xclip -o -selection primary)}
query=$(curl -s "https://api.dictionaryapi.dev/api/v2/entries/en_US/$word")
[ -z "$query" ] && notify-send -h string:bgcolor:#bf616a -t 3000 "Invalid word." && exit 0

# Show first definition for each part of speech
def=$(echo "$query" | jq -r '.[0].meanings[] | "\(.partOfSpeech): \(.definitions[0].definition)\n"')

notify-send -t 10000 "$word - " "$def"

```
 # how to make it work on windows?
 
 ```powershell
 param(
    [string]$word
)

# If no word provided, get from clipboard
if (-not $word -or $word.Trim() -eq "") {
    $word = Get-Clipboard
}

$word = $word.Trim()

if (-not $word -or $word -eq "") {
    Add-Type -AssemblyName PresentationFramework
    [System.Windows.MessageBox]::Show("No word provided or in clipboard.", "Define")
    exit
}

# Call dictionary API
$url = "https://api.dictionaryapi.dev/api/v2/entries/en_US/$word"

try {
    $response = Invoke-RestMethod -Uri $url -Method Get
} catch {
    Add-Type -AssemblyName PresentationFramework
    [System.Windows.MessageBox]::Show("Invalid word or API error.", "Define")
    exit
}

# Build definition text
$defs = ""

foreach ($meaning in $response[0].meanings) {
    $pos = $meaning.partOfSpeech
    $def = $meaning.definitions[0].definition
    $defs += "${pos}: ${def}`n`n"
}

# Show Windows popup
Add-Type -AssemblyName PresentationFramework
[System.Windows.MessageBox]::Show($defs, $word)

 ```
 # to be able to access it from anywhere
 ```powershell
 Set-Alias define F:\Documents\(02)Mycodeworks\scripts\define.ps1

 ```
# check to see if alias exists
```
Get-Alias define

```

## to remove it
```
Remove-Item Alias:define

```
# alternative:
### do this:
```powershell
notepad $PROFILE

```
and past this in
```powershell
function define {
    & "F:\Documents\(02)Mycodeworks\scripts\define.ps1" $args
}

```
remove if no more required
