Story 3:
Extraction classes.dex / AndroidManifest.xml

Commande utilisé pour décompiler:

unzip app.apk -d Story-03

Commande pour vérifier le hash en SHA256 :

Commande pour AndroidManifest.xml : Get-FileHash .\AndroidManifest.xml | Format-List

    résultat :

        Algorithm : SHA256
        Hash      : 80B9B101E3BF02F03EEC4F2C9C9CCF4D61403612E1435949B51CEC8B85D7A276

Commande pour classes.dex : Get-FileHash .\classes.dex | Format-List

    résultat :

        Algorithm : SHA256
        Hash      : 57898C5978715DA96560ED2692B4A8FA1A8E914B37988DCF369CC59951F6C429