---
title: Configurando o IIS para Directory Browsing
---

Para configurar o recurso directory browsing em um dado diretório no IIS (seja ele virtual ou não) basta criar um arquivo `web.config` na raiz deste diretório com a seguinte configuração:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <directoryBrowse enabled="true" />
    <security>
      <authorization>
        <!-- Allow all users access to the Public folder -->
        <remove users="*" roles="" verbs="" />
        <add accessType="Allow" users="*" roles="" />
      </authorization>
      <!-- Unblock all sourcecode related extensions (.cs, .aspx, .mdf) and files/folders (web.config, bin) -->
      <requestFiltering>
        <hiddenSegments>
          <clear />
        </hiddenSegments>
        <fileExtensions>
          <clear />
        </fileExtensions>
      </requestFiltering>
    </security>
    <!-- Remove all ASP.NET file extension associations. Only include this if you have the ASP.NET feature installed, otherwise this produces an Invalid configuration error. -->
    <handlers>
      <clear />
      <add name="StaticFile" path="*" verb="*" modules="StaticFileModule,DefaultDocumentModule,DirectoryListingModule" resourceType="Either" requireAccess="Read" />
    </handlers>
    <!-- Map all extensions to the same MIME type, so all files can be downloaded. -->
    <staticContent>
      <clear />
      <mimeMap fileExtension="*" mimeType="application/octet-stream" />
    </staticContent>
  </system.webServer>
</configuration>
```
