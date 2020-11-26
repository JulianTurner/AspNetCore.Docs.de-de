# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus. Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt. „Localhost“ dient nur Webanforderungen vom lokalen Computer. Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.
 
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.

  Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`. Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt. „Localhost“ dient nur Webanforderungen vom lokalen Computer.

  
# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Drücken Sie in Visual Studio **WAHL+CMD+EINGABETASTE**, um die Ausführung zu starten, ohne dabei den Debugger zu verwenden. Alternativ können Sie auch in der Menüleiste auf **Ausführen > Ohne Debugger starten** klicken.

  Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.

<!-- End of VS tabs -->

---
