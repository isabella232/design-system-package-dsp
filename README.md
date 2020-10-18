# Design System Package (DSP) documentation
> *DSP is a new open-format folder-structure created to help teams share design system information across tools.*

This document describes the Design System Package (DSP) structure, including details of how each internal file or folder should be used.

## Package format

A Design System Package (DSP) is a folder containing subfolders with assets and JSON files that represent design system information:
* Package details (e.g. version number, status, languages/platforms).
* Documentation pages (e.g. introduction, principles).
* Agnostic design tokens such as color, size, custom (numeric, string or boolean) and aliases. As well as  documentation in markdown, and tags to help with search.
* Collections of tokens, such as typography tokens (e.g. family, weight, size). As well as  documentation and tags.
* Component documentation including markdown sections with support for basic HTML (e.g. img, iframe), component-specific tokens, code snippets, and tags.

[Download a DSP folder example](https://github.com/AdobeXD/design-system-package-dsp/raw/master/sample-dsp.zip)

These are the contents of each DSP folder:

* [/assets](#assets-folder)
* [/data](#data-folder)
* [/dist](#dist-folder)
* [/ext](#ext-folder)
* [dsp.json](#understanding-the-dsp.json-file)

### assets folder
This folder contains all the static assets included in the DSP, such as SVG or PNG files.

### data folder
This folder contains all of the data that comprises the DSP format. The folder has no required files. File examples are components.json, docs.json, fonts.json and tokens.json.

### dist folder
This folder contains platform-specific code generated by a build system.
For example, the [Adobe XD extension for Visual Studio Code](http://www.adobe.com/go/xd_vscode_marketplace) has [Style Dictionary](https://amzn.github.io/style-dictionary/#/) as its build system. Examples of folders created by a build system are /android, /css and /styledictionary. Dist also contains non-platform-specific code such as the Style Dictionary input files and folders ([config](https://amzn.github.io/style-dictionary/#/config) and [properties](https://amzn.github.io/style-dictionary/#/properties)).

### ext folder
This folder could hold files written by third party tools. Its subfolders must follow the reverse domain name convention of "com_partner_name" (e.g. for Adobe, the folder should be named "com_adobe").

### dsp.json
This is the entry point for the Design System Package (DSP). It includes package info, settings, and imports.

## Understanding the dsp.json file

The dsp.json file has specific elements:

1. **File header** (required)

```javascript
// DSP Spec Version, present in all DSP JSON files
"dsp_spec_version": "1.0",

// Who last updated this file
"last_updated_by": "Author Name",

// When this file was last updated using UTCDateTime (eg. 2020-09-24T06:08:46.497Z)
"last_updated": "UTCDateTime",
```

2. **Settings** (required)

```javascript

"settings": {

    "name": "My Design System",
    "package_version": "0.5.0",

    // Examples: dev, staged, released, deprecated
    "build_status_label": "dev",

    // Prefix that will be prepended to code snippets
    "snippet_trigger_prefix": "xd-",

    "build_tool": "styledictionary",
    "build_tool_version": "1.0.0",

    // extra command line parameters that can be passed to the build tool during token compilation
    "build_params": "-c ./config.json",

    // languages to be used in component code snippets and build tool
    "languages": [
        {
            "label": "Spectrum CSS",
            "export_tokens": true,

            // snippet_id is used in components to identify a language option for code snippets
            "snippet_id": "my-css-snippet",
            "syntax": "text/css"
            
            /*
            additional properties that are not core to DSP should follow the inverse domain
            naming convention (e.g. 'ext-com_adobe_xd-styledictionary_id' is set to use Style
            Dictionary transform group 'css' to compile agnostic design tokens stored in /data,
            into CSS variables, if export_tokens is set to true)
            */
            "ext-com_adobe_xd-styledictionary_id": "css"
        },
        {
            "label": "React",
            "export_tokens": false,
            "snippet_id": "react",
            "syntax": "javascript"
        },
        {
            "label": "Angular",
            "export_tokens": false,
            "snippet_id": "angular",
            "syntax": "javascript"
        }
    ]
}
```

3. **Import** (not required - it can be omitted)

```javascript
/* DSP JSON files can be imported into other packages,
 by being added to the import array. 
 If there are overrides, the first instance in
 the array wins. */

"import": [
    {"src": "data/components.json"},
    {"src": "data/docs.json"},
    {"src": "data/tokens.json"},
    {"src": "data/fonts.json"}
],
```

4. **Extension** (optional)

```javascript
/* If partners need to add specific information that
is not core to DSP, the new information has to be placed
inside an ext object following the inverse domain
naming convention */

"ext-com_partnername": {
    "foo": "..."
},
```

5. **Entities** (optional)

The entities array contains objects defining the entities that comprise the design system. This can include entities such as design tokens, collections of tokens, documentation pages, and components. While entities can be defined in dsp.json, it is common to split them into separate files and include them via the import property.

> *Note: See the sample in [Imported JSON files](#imported-json-files) for additional information and examples.*

* **class**
Currently recognized values are:
    * token (most basic entity to represent key/value pairs)
    * collection (entities that are collections of tokens)
    * doc (entities that describe documentation in markdown with basic HTML support)
    * component (entities that describe components including documentation in markdown, code snippets and related entities)

* **type**
Divides each class by functional category. Currently recognized values for each class are:
    * token: color, size, custom, alias
    * collection: font
    * doc: page
    * component: page

> *Note: For the color value, we recommend using [RGBA hexadecimal](https://en.wikipedia.org/wiki/RGBA_color_model) (#RRGGBBAA, hex value with alpha).

* **id**
* **name** *// name is optional, will default to id if omitted*
* **value**
* **last updated**
* **last update author**
* **description**

> *Note 1: Collections have a **tokens** element listing all tokens that make that collection.*

> *Note 2: Component pages can have **related_entity_ids** for "used by" tokens, as well as **snippets**.*

### Sample dsp.json file

```json
{
    "dsp_spec_version": "1.0",
    "last_updated": "UTCDateTime",
    "last_updated_by": "Author Name",

    "ext-com_partnername": {
        "foo": "..."
    },

    "import": [
        {"src": "data/components.json"},
        {"src": "data/docs.json"},
        {"src": "data/tokens.json"},
        {"src": "data/fonts.json"}
    ],

    "settings": {
        "name": "My Design System",
        "package_version": "0.5.0",
        "build_status_label": "dev",
        "snippet_trigger_prefix": "xd-", 
        "build_tool": "styledictionary",
        "build_tool_version": "1.0.0",
        "build_params": "-c ./config.json",
        "languages": [
            {
                "label": "CSS",
                "export_tokens": true,
                "snippet_id": "css",
                "syntax": "text/css"
            },
            {
                "label": "React",
                "export_tokens": true,
                "snippet_id": "react",
                "syntax": "javascript",
                "ext-com_adobe_xd-styledictionary_id": "javascript"
            },
            {
                "label": "Angular",
                "export_tokens": true,
                "snippet_id": "angular",
                "syntax": "javascript",
                "ext-com_adobe_xd-styledictionary_id": "javascript"
            }
        ]
    }
}
```

## Imported JSON files

Additional JSON files can be imported to define additional tokens, components, docs and collections. They can be broken down into separate JSON files, or can be all in one JSON file. For organization purposes, we recommend saving them in the /data folder.

Each JSON file must include the standard file header, and may contain any of the properties defined for the dsp.json file. If a settings property is defined in an imported file, it will be ignored in favor of the dsp.json value.

### Sample JSON files

#### data/tokens.json

```json
{
    "dsp_spec_version": "1.0",
    "last_updated": "UTCDateTime",
    "last_updated_by": "Author Name",

    "entities": [
        {
            "class": "token",
            "type": "color",
            "id": "warning-label-color",
            "value": "FFFF0011",
            "name": "name is optional, will default to id if omitted",
            "last_updated": "UTCDateTime",
            "last_updated_by": "Author Name",
            "description": "The red used everywhere",

            "tags": [
                "branding",
                "color",
                "primary"
            ],

            "ext-com_adobe-cclib": {
                "source": "http://cclib-full-url",
                "id": "cclibid",
                "last_sync": "UTCDateTime",
                "sync": true
            }
        },
        {
            "class": "token",
            "type": "size",
            "id": "size-font-medium",
            "value": "14.0",
            "last_updated": "UTCDateTime",
            "last_updated_by": "Author Name",
            "description": "The medium 14 used everywhere",

            "tags": [
                "branding",
                "color",
                "primary"
            ]
        },
        {
            "class": "token",
            "type": "color",
            "id": "primary-red",
            "name": "primary-red",
            "value": "#FF0000",
            "description": "This is the red used _everywhere_, it is **important** to remember our brand guidelines",
            "last_updated": "2020-07-28T22:44:29.537Z",
            "last_updated_by": "Author Name",
            "tags": [
                "branding",
                "color",
                "primary"
            ]
        },
        {
            "class": "token",
            "type": "color",
            "id": "primary-green",
            "name": "primary-green",
            "value": "#00ff00",
            "description": "The green used everywhere",
            "last_updated": "2020-07-28T22:44:29.537Z",
            "last_updated_by": "Author Name",
            "tags": []
        },
        {
            "class": "alias",
            "type": "color",
            "id": "cta-background",
            "name": "cta-background",
            "value": "{primary-red}",
            "description": "The background color used for calls to action",
            "last_updated": "2020-07-28T22:44:29.537Z",
            "last_updated_by": "Author Name",
            "tags": []
        },
        {
            "class": "token",
            "type": "size",
            "id": "base-padding",
            "name": "base-padding",
            "value": "4",
            "description": "Size description here",
            "last_updated": "2020-07-28T22:44:29.537Z",
            "last_updated_by": "Author Name",
            "tags": []
        },
        {
            "class": "token",
            "type": "custom",
            "id": "base-weight-value",
            "name": "base-weight-value",
            "value": "bold",
            "description": "Token description here",
            "last_updated": "2020-07-28T22:44:29.537Z",
            "last_updated_by": "Author Name",
            "tags": []
        },
        {
            "class": "token",
            "type": "custom",
            "id": "custom-value",
            "name": "custom-value",
            "value": "foo",
            "description": "",
            "last_updated": "2020-07-28T22:44:29.537Z",
            "last_updated_by": "Author Name",
            "tags": []
        },
        {
            "class": "token",
            "type": "custom",
            "id": "font-default-text-family",
            "name": "font-default-text-family",
            "value": "Segoe UI",
            "description": "",
            "last_updated": "2020-07-28T22:44:29.537Z",
            "last_updated_by": "Author Name",
            "tags": []
        },
        {
            "class": "token",
            "type": "custom",
            "id": "font-default-text-weight",
            "name": "font-default-text-weight",
            "value": "Bold",
            "description": "",
            "last_updated": "2020-07-28T22:44:29.537Z",
            "last_updated_by": "Author Name",
            "tags": []
        },
        {
            "class": "token",
            "type": "size",
            "id": "font-default-text-size",
            "name": "font-default-text-size",
            "value": "12",
            "description": "",
            "last_updated": "2020-07-28T22:44:29.537Z",
            "last_updated_by": "Author Name",
            "tags": []
        }
    ]
}
```

#### data/fonts.json

```json
{
    "dsp_spec_version": "1.0",
    "last_updated": "UTCDateTime",
    "last_updated_by": "Author Name",
    "entities": [
        {
            "class": "collection",
            "type": "font",
            "id": "myfont",
            "last_updated": "UTCDateTime",
            "last_updated_by": "Author Name",
            "description": "The red used everywhere",
            "tags": [
                "branding",
                "color",
                "primary"
            ],
            "ext-com_adobe-cclib": {
                "source": "http://cclib-full-url",
                "id": "cclibid",
                "last_sync": "UTCDateTime",
                "sync": true
            },
            "tokens": [
                {
                    "key": "family",
                    "id": "font-myfont-family",
                    "type": "custom",
                    "value": "Segoe UI, Arial, sans-serif"
                },
                {
                    "key": "size",
                    "id": "size-myfont-small",
                    "type": "size",
                    "value": "10"
                }
            ]
        },
        {
            "class": "collection",
            "type": "font",
            "id": "default-text",
            "name": "default-text",
            "tags": [],
            "tokens": [
                {
                    "id": "font-default-text-family",
                    "type": "custom",
                    "value": "Segoe UI",
                    "key": "family"
                },
                {
                    "id": "font-default-text-weight",
                    "type": "custom",
                    "value": "Bold",
                    "key": "weight"
                },
                {
                    "id": "font-default-text-size",
                    "type": "size",
                    "value": "12",
                    "key": "size"
                }
            ]
        }
    ]
}
```

#### data/components.json

```json
{
    "dsp_spec_version": "1.0",
    "last_updated": "UTCDateTime",
    "last_updated_by": "Author Name",

    "entities": [
        {
            "class": "component",
            "type": "page",
            "id": "unique-id",
            "name": "Button",
            "last_updated": "UTCDateTime",
            "last_updated_by": "Author Name",
            "description": "Markdown with support for basic HTML including img and iframe tags.",

            "tags": [
                "branding",
                "color",
                "primary"
            ],

            "related_entity_ids": [
                "btn-background-color",
                "default-text"
            ],

            "snippets": {
                "trigger": "my_component",
                "languages": {
                    "my-css-snippet": "...",
                    "react": "...",
                    "angular": "..."
                }
            }
        }
    ]
}
```

#### data/docs.json

```json
{
    "dsp_spec_version": "1.0",
    "last_updated": "UTCDateTime",
    "last_updated_by": "Author Name",

    "import": [],

    "entities": [
        {
            "class": "doc",
            "type": "page",
            "id": "a-unique-id",
            "name": "Introduction",
            "last_updated": "UTCDateTime",
            "last_updated_by": "Author Name",
            "description": "The red used everywhere. This field fully supports [Markdown](https://commonmark.org/help/) with support for basic HTML.",

            "tags": [
                "branding",
                "color",
                "primary"
            ]
        }
    ]
}
```

## Using NPM to distribute your Design System Package

If you decide to use NPM to distribute your package, please add a package.json file to the root folder by following [the instructions to publish to NPM](https://docs.npmjs.com/creating-and-publishing-unscoped-public-packages).

For iOS you can explore sharing the tokens using [CocoaPods](https://cocoapods.org/), and for Android you can explore sharing the tokens as part of a [Gradle](https://gradle.org/).

## Questions or feedback
Please file an [issue](https://github.com/AdobeXD/design-system-package-dsp/issues).
