## <a name="share-interop-code-in-a-class-library"></a>Freigeben von Interop-Code in einer Klassenbibliothek

JavaScript-Interop-Code kann in einer Klassenbibliothek eingeschlossen werden, wodurch Sie den Code in einem NuGet-Paket freigeben können.

Die Klassenbibliothek behandelt das Einbetten von JavaScript-Ressourcen in der erstellten Assembly. Die JavaScript-Dateien werden im Ordner `wwwroot` gespeichert. Die Tools kümmern sich um das Einbetten der Ressourcen, wenn die Bibliothek erstellt wird.

In der Projektdatei der App wird so auf das NuGet-Paket verwiesen, wie auf jedes andere NuGet-Paket verwiesen wird. Nachdem das Paket wiederhergestellt wurde, kann App-Code einen Aufruf in JavaScript wie auch unter C# durchführen.

Weitere Informationen finden Sie unter <xref:blazor/components/class-libraries>.
