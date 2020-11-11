* `-F|--no-formatting`

  Ein Flag, das die Unterdrückung der HTTP-Antwortformatierung bewirkt.

* `-h|--header`

  Legt einen HTTP-Anforderungsheader fest. Die folgenden zwei Werteformate werden unterstützt:

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  Gibt eine Datei an, in die der HTTP-Antworttext geschrieben werden soll. Beispiel: `--response:body "C:\response.json"`. Wenn die Datei nicht vorhanden ist, wird sie erstellt.

* `--response:headers`

  Gibt eine Datei an, in die die HTTP-Antwortheader geschrieben werden sollen. Beispiel: `--response:headers "C:\response.txt"`. Wenn die Datei nicht vorhanden ist, wird sie erstellt.

* `-s|--streaming`

  Ein Flag, das das Streaming der HTTP-Antwort aktiviert.
