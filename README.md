# PlantUML Quick Guide

- [PlantUML Quick Guide](#plantuml-quick-guide)
  - [Installation and Running](#installation-and-running)
    - [Online](#online)
    - [Local installation](#local-installation)
    - [PlantUML server](#plantuml-server)
  - [Diagram source code](#diagram-source-code)
    - [Encoding](#encoding)
    - [PNG metadata](#png-metadata)
  - [Integrations](#integrations)
    - [Visual Studio Code](#visual-studio-code)
      - [`Local` render](#local-render)
      - [`PlantUMLServer` render](#plantumlserver-render)
  - [Resources](#resources)

## Installation and Running

### Online

- [Server-hosted](https://www.plantuml.com/plantuml/)
  - has ads
  - does not store diagrams in their servers
    - the whole diagram source code is compressed into the URL
    - the server decompresses the diagram's compressed text and generates the image with it
    - e.g., with diagram source code such as [`src/sequencediagram/simple.puml`](src/sequencediagram/simple.puml):
      - compressed into `SoWkIImgAStDuNBCoKnELT2rKt3AJx9IA4ajBk5oICrB0Ke10000`
      - view with the online server: <https://www.plantuml.com/plantuml/uml/SoWkIImgAStDuNBCoKnELT2rKt3AJx9IA4ajBk5oICrB0Ke10000>
- [Client-side](https://plantuml.github.io/plantuml-core/raw.html) (pure JavaScript)
  - code: <https://github.com/plantuml/plantuml-core>

### Local installation

- Requirements
  - Java
  - [Graphviz](https://graphviz.org/download/)
    - not needed if you only need sequence diagrams and activity diagrams
    - see <https://plantuml.com/graphviz-dot>
- Install
  - download and install the above requirements
  - download [`plantuml.jar`](http://sourceforge.net/projects/plantuml/files/plantuml.jar/download)
  - place `plantuml.jar` into a directory of your choice
    - no need to unpack
- Command line
  - see <https://plantuml.com/command-line>
  - `java -jar plantuml.jar simple.puml`
    - `simple.puml` is a text file with PlantUML commands, e.g., [`src/sequencediagram/simple.puml`](src/sequencediagram/simple.puml)
    - outputs diagram to a file called `simple.png`, e.g., [`src/sequencediagram/simple.png`](src/sequencediagram/simple.png)
- GUI
  - `java -jar plantuml.jar -gui` or `java -jar plantuml.jar -gui /source/directory`
    - the diagram updates automatically if the diagram source code is modified

### PlantUML server

- Options to run PlantUML locally or on a self-hosted server
- JEE server
  - download the [`plantuml.war`](http://sourceforge.net/projects/plantuml/files/plantuml.war/download) file and copy it into the _webapp_ directory of your server
- PlantUML PicoWeb server
  - start the embedded web server using `java -jar plantuml.jar -picoweb:8080`
  - only understands GET requests with the following patterns:
    - `/plantuml/png/xyz....`
    - `/plantuml/svg/xyz....`
  - to test: `http://localhost:8080/`
  - examples:
    - `http://localhost:8080/plantuml/png/SyfFKj2rKt3CoKnELR1Io4ZDoSa70000`
    - `http://localhost:8080/plantuml/svg/SyfFKj2rKt3CoKnELR1Io4ZDoSa70000`
  - GET requests are used by various PlantUML plugins
    - use plugins with the embedded server without the [official online server](http://www.plantuml.com/plantuml)
  - note: PicoWeb server is [part of PlantUML code](https://github.com/plantuml/plantuml/blob/master/src/net/sourceforge/plantuml/picoweb/PicoWebServer.java)

## Diagram source code

### Encoding

- PlantUML defines a way to encode diagram source code
  - to facilitate communication of diagrams through URL, e.g., `https://www.plantuml.com/plantuml/uml/SyfFKj2rKt3CoKnELR1Io4ZDoSa70000`
- Diagram source code is encoded by:
  - encoding in UTF-8
  - compressing using the Deflate algorithm
  - re-encoding in ASCII using a transformation similar to Base64

```console
$ cat path/to/simple.puml
@startuml
Alice -> Bob: test
@enduml

$ java -jar plantuml.jar -encodeurl path/to/simple.puml
Syp9J4vLqBLJSCfFib8eIIqk0G00

$ java -jar plantuml.jar -decodeurl Syp9J4vLqBLJSCfFib8eIIqk0G00
@startuml
@startuml
Alice -> Bob: test
@enduml
@enduml
```

### PNG metadata

- Generated diagrams in PNG format include the diagrams' source code in the PNG metadata
  - can be disabled using the `-nometadata` option on the command line
- Extract diagram source code:
  - `java -jar plantuml.jar -metadata path/to/simple.png`

```console
$ java -jar plantuml.jar -metadata path/to/simple.png
------------------------
path/to/simple.png

@startuml
Alice -> Bob: test
@enduml

PlantUML version 1.2023.4(Thu Mar 09 19:03:49 GMT 2023)
(MIT source distribution)
Java Runtime: OpenJDK Runtime Environment
JVM: OpenJDK 64-Bit Server VM
Default Encoding: UTF-8
Language: en
Country: GB

------------------------
```

```console
$ exiftool -Plantuml path/to/simple.png
Plantuml                        : @startuml..Alice -> Bob: test..@enduml...PlantUML version 1.2023.4(Thu Mar 09 19:03:49 GMT 2023).(MIT source distribution).Java Runtime: OpenJDK Runtime Environment.JVM: OpenJDK 64-Bit Server VM.Default Encoding: UTF-8.Language: en.Country: GB.
```

## Integrations

- This section describes some integrations that are available with PlantUML
- See <https://plantuml.com/running> for the full list

### Visual Studio Code

- Extension: <https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml>
- Supports rendering using a locally installed `plantuml.jar` file (default) or with a PlantUML server installed locally or remotely
- See [extension documentation](https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml&ssr=false#user-content-extension-settings) for a complete list of settings

#### `Local` render

- Requirements
  - per [Local installation](#local-installation)
  - but _not_ `plantuml.jar`
    - bundled with extension
    - unless you want to use your own copy
    - note: the bundled `plantuml.jar` can be pretty outdated!
- Settings
  - `plantuml.jar`: location of your own `plantuml.jar`

#### `PlantUMLServer` render

- Recommended for this extension
- Requirements
  - a [PlantUML server](#plantuml-server)
- Settings
  - `plantuml.server`: URL of your PlantUML server
  - `plantuml.render`: `PlantUMLServer`

## Resources

- "Quick Start Guide." _Plantuml.com_, 2023, [plantuml.com/starting](https://plantuml.com/starting). Accessed 29 Apr. 2023.
- "Frequently Asked Questions about installation." _Plantuml.com_, 2023, [plantuml.com/faq-install](https://plantuml.com/faq-install). Accessed 29 Apr. 2023.
- "Call It from Your Script Using Command Line." _Plantuml.com_, 2023, [plantuml.com/command-line](https://plantuml.com/command-line). Accessed 29 Apr. 2023.
- "Text Encoding Format." _Plantuml.com_, 2023, [plantuml.com/text-encoding](https://plantuml.com/text-encoding). Accessed 29 Apr. 2023.
- "Standalone Tool Usage." _Plantuml.com_, 2023, [plantuml.com/gui](https://plantuml.com/gui). Accessed 30 Apr. 2023.
- "The Servlet for Server Side." _Plantuml.com_, 2023, [plantuml.com/server](https://plantuml.com/server). Accessed 30 Apr. 2023.
- "Frequently Asked Questions." _Plantuml.com_, 2023, [plantuml.com/faq](https://plantuml.com/faq). Accessed 30 Apr. 2023.
- "PlantUML - Visual Studio Marketplace." _@Code_, 2022, [marketplace.visualstudio.com/items?itemName=jebbs.plantuml&ssr=false](https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml&ssr=false). Accessed 8 May 2023.
