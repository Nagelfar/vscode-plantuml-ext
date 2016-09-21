# Amigo PlantUML Modeler

If you are into agile, into modeling and also into low-fidelity tooling then you may want to read on... 

PlantUML is very simple language syntax (see example below) that supports most of the modeling semantics of the UML spec and more. However, it's more than just a syntax specification for UML models; it's also a Java binary which renders a PlantUML artifact to a nominated graphic format against the syntax specification. You can find out a lot more about PlantUML at the [PlantUML website](http://plantuml.com/).

```
@startuml
hide empty members
Causality -left- Self

Self o--> "*" Action: owner  
Self o--> "*" Action: heir
Self o--> "*" Action: born
Self o--> "*" Action: related
Self o--> "*" Action: live dependent
(Causality,Self) .. Consciousness
@enduml
```
Eg. Consciousness.pu Text File


This VScode extension is not affiliated in any way with the PlantUML team. I developed it because there was no other extension that supported the feature set that I required. Feel free to visit this extension's [open-source source code](https://github.com/ashinw/vscode-plantuml-ext).


## Background
In a former life I was once a UML distilled enthusiast and an agile modeling advocate. In 1990 I was fortunate enough to be part of an international Object Technology forum that collaborated on and reviewed the UML when it was in its pre-initial draft and at the time named the Unified Method. This extension (ie. Amigo) is named in honour of Grady, Jim and Ivar; the original 3 Amigos that created the UML.

Anyway, do as you see fit with this extension and code. I make no claim to it. 


## Configuration

The previous release of this extension had the following external dependencies:
1. JAVA
2. PlantUML.jar
3. GRAPH VIZ

This release attempts to reduce the barriers to entry by packaging together the dependent software distributions. Currently, the only external dependency is JAVA. I just spent the last one week attempting to native compile (ie. Way Ahead Of Time as opposed to JIT) plantuml.jar into a native terminal app for all OS's. Unfortunately, PlantUML's use of AWT put an end to those aspirations. 

This release of the extension bundles the following software:
1. plantuml-vscode-8407.jar
Note, this is custom build of plantuml.jar! I have modified PdfConverter.java by using NullSVGConverterController.java (refer source) in order to stop Batik from emitting warning messages to standard output whilst doing a PDF export. I also modified the manifest file which is used to package the jar together. The modified manifest makes the correct references to batik and the latest jsv8 binaries.

2. Batik
PlantUML uses Batik's rasterizer for the purposes of generating a PDF. Batik 1.7 is the only version that I could get to work. The newer Batik 1.8 release is plagued with missing dependencies.

3. VizJS & J2V8 
VizJS is a javascript implementation of GraphViz created via Emscripten and fronted by a Java class. J2V8 is then used to run the javascript implementation from Java. VizJS is a plantuml sub-project, and whilst I have included it in the bundle I have personally found it a little temperamental. Hence, if you encounter any VizJS related rendering issues, I recommend that you set up a GRAPHVIZ_DOT environment variable as in the previous release (refer Extension Settings section below). 


Having said that, all you need to do now is set JAVA_HOME either as a user setting in vscode or as an environmental variable. Keep in mind some developers (like myself) have many JDK's installed. In such instances, I wanted to be able to specify which JDK distribution to use and not be stuck with the one set in the environmental variable.

![configuration](https://github.com/ashinw/plantumlpreview/blob/master/images/previews/java_home-config.png?raw=true)



![PlantUML vscode extension Use-case model](https://github.com/ashinw/plantumlpreview/blob/master/images/previews/use-case-model.png?raw=true)


## Features
- PlantUML languageid definition for vscode
- 100+ PlantUML snippets 
- Preview PlantUML text in SVG format
- Selected Text Preview 
- Toggle Pause Live Preview 
- Export PlantUML document
- Import content from PlantUML template


### PlantUML languageid definition for vscode

An editor can only provide limited support whilst the definition of the document remains unknown. In this release of the extension a new "plantuml" languageid has been defined. PlantUML files are first class citizens of the editing environment and now benefit from syntax highlighting and code snippets. The following file extensions are now associated with the plantuml languageid:
```
  "id": "plantuml",
  "aliases": [
      "PlantUML"
  ],
  "extensions": [
      ".pu",
      ".plantuml",
      ".iuml",
      ".puml"
  ],
```

However, any file can temporarily apply the plantuml languageid by nominating it via vscode's language selector (ie. bottom right in UI). For example, when creating new files (ie. no file extension) you may choose to nominate the language via the selector and immediately start benefiting from syntax highlighting and snippets.

![Using Languageid](https://github.com/ashinw/plantumlpreview/blob/master/images/previews/setting-langid.png?raw=true)


### 100+ PlantUML snippets 
Snippets are code/text fragments that are embedded in text documents. The developers of PlantUML worked hard to create a very simple language syntax. However, often whilst modeling one may find themselves referencing the PlantUML manual to try and figure out how to model a particular aspect. Hence, rather than repeatedly reference a manual, the modeler can now invoke a snippet for support instead.  

It is for this reason that all snippets in this extension offer the more _verbose_ syntax. The principle here is that is much easier to tear down (ie. delete any unwanted modeling attributes), than to setup syntax from scratch.

Eg. Snippet for an association relationship in a class diagram

```
classAlias "multiplicityRole1" --> "multiplicityRole2" typeAlias : label <<stereotype>> >
``` 
Clearly, you will not model **all** associations with role names, multiplicities association names and stereotypes. However, what this snippet does, is it lets you visit each snippet segment, and then lets you choose whether to edit it or delete it.

All snippets have been organised by diagram. Therefore, unlike traditional snippets which are invoked by typing the first few characters of language control statement, these PlantUML snippets are invoked by UML diagram character identifier. These character identifiers are all located beside one another on the keyboard.

The PlantUML character snippet invokers are:
1. @ - Snippets that are universally applicable to all PlantUML diagrams
2. ! - Snippets for User Interface Diagrams
3. # - Snippets for Sequence Diagrams
4. $ - Snippets for Class/Package/Object Diagrams
5. % - Snippets for Use-case Diagrams
6. ^ - Snippets for State Transition Diagrams
7. & - Snippets for Activity Diagrams
8. * - Snippets for Component/Deployment Diagrams


### Preview PlantUML text in SVG format

PlantUML artifacts are regular text files which are governed by the following fundemental syntax rules: 
1. PlantUML source must start with the keyword: *@startuml*
2. PlantUML source must end with the keyword: *@enduml*

There is no commonly used file extension, nor are there any viewers built into the operating system that are capable of previewing these documents. As such, PlantUML users have traditionally used the [PlantUML server or variant](http://www.plantuml.com/uml/) for round-trip authoring/modeling. This extension's primary feature is to support the a webservice-free view of PlantUML models whilst remaining within vscode. Previewing is available via keyboard shortcut or Command Palette Selection.

Launch via:
- Short cut: ctrl+p ctrl+v
- Menu: View > Command Palette > Open PlantUML Preview

![Preview diagram](https://github.com/ashinw/plantumlpreview/blob/master/images/previews/preview.png?raw=true)


### Selected Text Preview 

One of the greatest features of PlantUML is its human readable syntax. The UML is a means for communication, yet for most organisation, almost all models are buried in documents or CASE tools. Instead, consider embedding PlantUML text as code comments in source code, or as sections in Markdown documents. However, in such instances developers/reviewers need the ability to preview the embedded PlantUML text whilst remaining in the richness of their editing environment for the host language file. In these situations the user may simply select text between the @startuml to @enduml tags and then invoke the same preview feature identified above.

![Preview Selected Diagram](https://github.com/ashinw/plantumlpreview/blob/master/images/previews/preview-selection.png?raw=true)


### Toggle Pause Live Preview 

Have you ever had a live continous compilation process executing whilst you are typing your source code? Seeing all those compiler errors when coding would not only be distracting and frustrating, but would be counter productive. Modeling is no different. Sure, there are those occasions when a live preview is desired, such as changing the model layout or changing an attribute of a relationship. However, more often than not, it is more desirable to keep some part of a model stable whilst modeling other aspects around it. This can be achieved through the Toggle Pause Live Preview Command. Each time you want to freeze the model image, simply invoke the Toggle command. Then continue modeling and when ready toggle the pause off using the same command and the model will be updated. You can continue with this toggle command as frequently as you like. Note, if you change the vscode editing context to another file, then the model is auto-toggled off and will return to live preview when you return to the file.

Launch via:
- Short cut: ctrl+p ctrl+p
- Menu: View > Command Palette > Toggle PlantUML Live Preview


![Toggle Pause Live Preview](https://github.com/ashinw/plantumlpreview/blob/master/images/previews/preview-toggle.png?raw=true)


### Export PlantUML document

Whilst PlantUML documents are highly human readable, their semantics can be better interpreted when in a graphic format. In graphic format the richness of UML notation and conventions can be applied. Hence, this command enables the modeler to export the PlantUML document into a nominated graphic format such that the model's life may continue in other documents or streams.

As of this writing the following types have in theory been supported:
```
    enum OutputFmt {
        PNG,
        SVG,
        EPS,
        PDF,
        VDX,
        XMI,
        SCXML,
        HTML,
        TXT,
        UTXT,
        LATEX,
        LATEX_NOPREAMBLE
    }
```

I have personally tested a few of these formats (incl PDF) and they have _finally_ worked for me. Note, this extension's implementation uses the STDIO (ie. standard input & standard output) on the Java PlantUML.jar binary. This in of itself should not be a problem because PlantUML.jar command line interface explicitly supports piped input/output. However, the developers of both PlantUML & Batik on occasion like to write diagnostic/information/error messages to standard output which may corrupt the standard output stream and consequently the exported image file. I have modified some problematic areas in the PlantUML codebase, but fixing up PlantUML is outside the scope of this hunble project.

Launch via:
- Menu: View > Command Palette > Export PlantUML text...

*Exporting Untitled documents*

A unsaved Untitled document is a document that is typically created in vscode using File > New File or (Ctrl+N). Such documents have no persistent state and reside only as a buffer in memory. Such documents have no file system path context and are then subject vscode's workspace configuration (ie. whether vscode is in folder mode or individual file mode). Therefore use the following condition statement to find exported Untitled documents: 

```
if (vscode in folder mode) then
  exported-file.path = vscode.workspace.rootPath
else
  exported-file.path = Operating System's User home folder
endif
```

![Export Document](https://github.com/ashinw/plantumlpreview/blob/master/images/previews/export.png?raw=true)


### Import content from PlantUML template

Modeling on a blank canvas can be difficult. In such instances you may prefer to start from an existing cookie cutter base. This is the purpose of a import PlantUML template feature. A PlantUML template serves as a source for you to start refactoring an existing model into your new model. This release of the extension ships with a sample set of templates that I have authored to model this vscode PlantUML extension itself. These models are far from exemplary. I added them because it was better than offering an empty templates folder or some other rather obscure academic example. The following templates are included:
- Activity Diagram.pu 
- Class Diagram.pu
- Component Diagram.pu
- Package Diagram.pu
- Sequence Diagram.pu
- State-Transition Diagram.pu
- Use-Case Diagram.pu

All templates must have a *'.pu'* suffix and reside in the extension's template folder:

```
[%USERPROFILE%|$USER]/.vscode/extensions/self-technologies.plant-uml-ext-<x.y.z>/templates
```
However, you *should* either edit the above templates and/or add your own templates to this folder. All *'.pu'* files in the templates folder will be automatically loaded into the registry when any one of the extension commands cited above are first launched.

Launch via:
- Menu: View > Command Palette > Import PlantUML template...

![Import Template](https://github.com/ashinw/plantumlpreview/blob/master/images/previews/template.png?raw=true)



## Extension Settings

**Requisite Environment Variables** 

- GRAPHVIZ_DOT:

  Although as stated above, this release of the extension only has JAVA as a external dependency, I recommend that you install Graphviz at the first instance of rendering errors on your platform. Your mileage may vary from mine and because I am yet to discern the causal factors resulting in VizJS crashing, I am unable to offer any advice.

  Graphviz is an open-source package that is used in some areas of PlantUML. Depending on the PlantUML text that you model you may encounter rendering errors if this component is not installed.

  This must be a full path and filename reference to the file dot executable file. Please refer to [PlantUML Graphviz notes](http://plantuml.com/graphvizdot.html) for further details.

  ```
  set GRAPHVIZ_DOT=C:\win.DATA\dev\tool\graphviz-2.38\bin\dot.exe
  ```

Note, the example Window's terminal environmental variables above are provided as a source of context and are only temporary settings. You should use the a permanent configuration approach offered by your OS.



## Acknowledgements
* Arnaud Roques & PlantUML team - [PlantUML sourceforge repo](http://sourceforge.net/projects/plantuml/)
* Florian Breisch - [PlantUML language syntax github repo](https://github.com/florianb/PlantUML.tmbundle) . Note, this repo seems to be have been abandoned 3 years ago. I used Florian's implementation as a reference; the syntax has since changed and I also found some of the regex matches overly obtrusive. Furthermore, there was a lot of attention given to standard colors and skinparams which are orthogonal to this implementation's principles.



## Looking for a new owner
- I developed this vscode extension for a purpose, and that purpose has now been achieved. I am at a point in my time in my life were I am practicing dispassion over passion, unfettered over fettered, shedding over accumulating, modesty over self-agrandisation, contentment over discontentment, persistence over laziness, seclusion over entanglement, and unburdensome over burdensome.

As such, it is time for me to _let go_ of this vscode extension. I offer it in totality to whom ever wants to claim it without attribution, without acknowledgements; just take it, make it yours; and if it interests you, collaborate with the PlantUML team on improving the overall platform. As soon as you have taken ownership of this extension, [post an issue](https://github.com/ashinw/vscode-plantuml-ext/issues) stating claim and I will immediately unpublish this extension and delete the repository from github.



## Known Issues
- The PlantUML.tmLanguage (ie. syntax highlighting) file is far from perfect. Even worse than my modeling and coding abilities are my skills with regular expressions. 



## Release Notes

### 0.1.0
- Added an quick selection export menu (ie. GUI) to avoid Command Palette PlantUML export format pollution
- Added support for PlantUML preview for selected text within any text document. This is handy for developers who want to preview UML embedded in code comments.
- Added edit buffer settlement delay before preview refresh
- Added toggle pause live preview command
- Added template support with common UML artifacts
- Added binaries to ensure that PDF and other features work out of box

### 0.0.1
- First release.