# Example modular documentation repository (Kogito)

>**IMPORTANT**: As modular documentation imperatives continue to evolve, the details and recommendations in this guide might be modified. The modular documentation team recognizes that the fluctuation in recommendations can be frustrating for contributors trying to adopt modularization standards and apologizes in advance as the initiative pursues the best possible outcome for modularized documentation.

Due to many factors that determine documentation repository design for individual products, the modular documentation initiative cannot provide a strict documentation repository template for all documentation teams to follow. Each team faces different implementation challenges, especially when Red Hat enterprise product documentation is single-sourced with corresponding community project documentation.

However, for optimal FCC, Pantheon, and Pantheon 2 compliance, the modular documentation initiative recommends a general repository structure similar to the following example, where possible:

**Recommended documentation repository structure (where possible)**
```
├── _artifacts
├── _images
├── assemblies
    ├── modules -> ../modules
    ├── assembly-kogito-configuring.adoc
    ├── assembly-kogito-creating-running.adoc
    ├── assembly-kogito-decision-engine.adoc
    ├── assembly-kogito-developing-decision-services.adoc
    ├── assembly-kogito-developing-process-services.adoc
    ├── assembly-kogito-using-dmn.adoc
    ├── assembly-kogito-using-drl.adoc
    ├── ...
├── modules
    ├── configuration
        ├── con-data-index-service.adoc
        ├── con-jobs-service.adoc
        ├── proc-data-index-service-enable-security.adoc
        ├── ref-data-index-service-queries.adoc
        ├── ...
    ├── creating-running
        ├── con-kogito-automation.adoc
        ├── con-kogito-modelers.adoc
        ├── proc-kogito-app-examples-running.adoc
        ├── ref-kogito-app-examples.adoc
        ├── ...
    ├── decision-services  // Contains modules related to multiple assemblies.
        ├── con-decision-tables.adoc
        ├── con-dmn.adoc
        ├── con-drl.adoc
        ├── proc-dmn-model-creating.adoc
        ├── ref-dmn-model-example.adoc
        ├── ...
    ├── process-services
        ├── ...
    ├── ...
├── titles-community
    ├── // See README file
├── titles-enterprise
    ├── kogito-configuring
        ├── _artifacts -> ../_artifacts  // Symlinks can be extracted.
        ├── _images -> ../../_images
        ├── assemblies -> ../../assemblies
        ├── master.adoc
        └── master-docinfo.xml
    ├── kogito-creating-running
        ├── ...
    ├── kogito-developing-decision-services  // Contains multiple assemblies.
        ├── ...
    ├── ...
├── pantheon2.yml
└── README.md
```

This repository structure contains the following key components:

- `_artifacts`: Directory with document attribute files and other metadata files. The underscore in the folder name is an optional indication of a non-content folder.
- `_images`: Directory with image files, optionally divided into sub-directories by subject matter. The underscore in the folder name is an optional indication of a non-content folder.
- `assemblies`: Directory with assembly files. You collect and compile assemblies into documents (titles) using the `titles-enterprise` folder.
- `modules`: Directory with module files, optionally divided into sub-directories by subject matter.
- `titles-community`: (Optional) Directory with community-specific build-chain components, if applicable, to build community titles from the central module or assembly content. This folder is left empty in this example repository because community options and variants are expansive and highly specific to each project and product. For more information, see the `README` file in the [`titles-community`](https://github.com/redhat-documentation/modular-docs/tree/mod-doc-repo-example/titles-community) folder.
- `titles-enterprise`: Directory with folders for each enterprise document or _title_ to be built in Pantheon. Each document folder contains a `master.adoc` file where you include one or more relevant assemblies that are built as part of the document. The `master-docinfo.xml` file contains metadata that is required by `bccutil` (and `ccutil`) when converting to DocBook format. (This metadata file might be deprecated in a future release of `bccutil` and Pantheon 2.) In repositories that are single-sourced with a community project, a separate `titles-community` folder might be dedicated to community configurations and output, if applicable.
- `pantheon2.yml`: YAML file with metadata that is required for publishing content on Pantheon 2:
+
**Example `pantheon2.yml` file**
```
server: http://localhost:8080
repository: mod-doc-repo-example
variants:
   - name: global-attributes
     path: _artifacts/document-attributes.adoc
     canonical: true
   - name: community-attributes
     path: _artifacts/document-attributes-kogito-comm.adoc
   - name: enterprise-attributes
     path: _artifacts/document-attributes-kogito-ent.adoc
assemblies:
  - assemblies/*.adoc
modules:
  - modules/*/*.adoc
resources:
  - _images/*/*.png
  - _artifacts/*.adoc
```
+
For information about configuring this file for your specific content, contact a member of the CCS tooling team.

This repository structure offers the following advantages:

- **Flexibility in determining publishable titles.** In this structure, the images, artifacts, assemblies, and modules all serve as distinct building blocks that you use to compile a document or _title_. You can include an image in multiple modules, a module in multiple assemblies, and an assembly in multiple titles. Additionally, just as an assembly can include multiple modules, a title can contain multiple assemblies.
- **More intuitive content reuse.** When you decouple the standalone content components from the publishable titles, you avoid potentially awkward includes when modules or assemblies from one title are reused in another. For example, consider a repository where each publishable title folder contains all modules or assemblies or both directly within it. This creates a dependency for that module or assembly content on that title. As you reuse content across title folders, you end up with includes that direct to various other unrelated titles to retrieve the content. Alternatively, in the recommended repository structure, you can reuse all content independent of the titles in which you include it.
- **Potentially easier collaboration in community and between content repositories.** For repositories that are single-sourced with community projects or that need to reuse content between product documentation repositories, this structure enables you to isolate the enterprise title compilation requirements so that the standalone components can be shared more readily. However, single-sourcing between community and enterprise content repositories poses other challenges beyond the scope of this document. (See the disadvantages and community considerations sections that follow.)
- **Consistency with Pantheon 2 YAML configurations.** In this structure, the division of modules, assemblies, and other resources is consistent with the division of components in the `pantheon2.yml` file that is required for documents published on Pantheon 2.

This repository structure poses the following disadvantages:

- **Extensive use of symlinks.** In order to enable titles to be built with distinct assemblies, modules, and images, this repository structure uses many symlinks (symbolic links) for access across directories. You can eliminate these symlinks with a build script or additional variable configurations (such as `imagesdir`), but because this example repository is intended for a general audience, it maintains explicit symlinks, so that a user can build the repository titles with standard Asciidoctor tooling. Alternative repository structures that combine all relevant images, modules, and assemblies together in title folders can reduce the number of symlinks, but these alternative structures then forego many of the advantages listed previously.
- **Potential conflicts with community content strategy.** Although this documentation repository structure is typically more collaboration-friendly than other FCC repository alternatives (as described in the previous advantages), the FCC strategy as a whole still might not be well received among all community projects. For example, many community projects prefer Mavenized repositories divided into chapter files instead of module files, all categorized in folders by guides or pages (titles) to be built. A community aversion to modularization in general can require writers to modify this example repository design, to pursue tooling alternatives to accommodate all content needs, or in some cases to abandon single-sourcing with community.

To build the example titles in this example repository, in a command terminal, navigate to `~/titles-enterprise/__DOC_FOLDER__` and enter one of the following commands:

**Build with `bccutil` (RHEL 8 or Fedora 30 and later)**
```
$ bccutil compile --lang=en-US --format html-single --open
```

**Build with `ccutil` (RHEL 7 or Fedora 29 and earlier)**
```
$ ccutil compile --lang=en-US --format html-single --open
```

**Build with Asciidoctor**
```
$ asciidoctor master.adoc
$ google-chrome master.html
```

## Community considerations in modular documentation repositories

For documentation repositories that are single-sourced with community projects, many factors and challenges influence repository design. Community projects might require different build chains, different contribution processes, different formatting, or other elements that complicate repository design decisions. For this reason, the FCC recommendations in this section are general guidelines to be followed only where possible, with the assumption that various or all aspects of the recommended repository design might require a different strategy among some communities.

For help with community collaboration needs and challenges, see the following resources:

- [Community collaboration guide](https://redhat-documentation.github.io/community-collaboration-guide/)
- [Creating modular documentation repositories course](https://learning.redhat.com/mod/facetoface/view.php?id=6590)
- [Creating modular documentation repositories agenda](https://docs.google.com/document/d/1XLptcmIaU9ymQoxwC5FIf_VDhBNrHb4zzvUiM8qesEw/edit?usp=sharing) (contains links to example single-sourced repositories)
