# xeokit-viewer [*beta MVP*]

xeokit-viewer is an open source IFC model viewer for the Web, built on the [xeokit SDK](http://xeokit.io). The viewer is developed by [xeolabs](http://xeolabs.com) for integration within [OpenProject's](https://www.openproject.org/) BIM construction project management software, and is also usable as a stand-alone viewer for your BIM models. 

[![Screenshothttps://xeokit.github.io/xeokit-licensing/](images/xeokit-viewer.png)](https://xeokit.github.io/xeokit-viewer/index.html?project=OTCConferenceCenter&tab=storeys).

## Contents

- [Contents](#contents)
 * [Features](#features)
  * [Demos](#demos)
  * [Roadmap](#roadmap)
  * [License](#license)
  * [Usage](#usage)
    + [Model Database](#model-database)
      - [Adding your own models](#adding-your-own-models)
      - [Loading models from a custom source](#loading-models-from-a-custom-source)
    + [Customizing CSS](#customizing-css)
    + [Tooltips](#tooltips)
    + [Customizing Appearances of IFC Types](#customizing-appearances-of-ifc-types)
  * [Building](#building)
  
## Features

* Uses [xeokit SDK](https://xeokit.io) for super fast loading and rendering of large models.
* Works in all major browsers, including mobile.
* Loads BIM geometry and metadata from the file system.
* Loads multiple models, at arbitrary position, scale and rotation.
* Configure custom appearances for IFC types.
* Supports IFC2x3 and IFC4.
* 3D and 2D viewing modes.
* Tree view with three hierarchy modes: containment, IFC type and storeys.
* X-ray, highlight, show, hide and slice objects. 
* Customize with your own CSS.
* Implemented in JavaScript (ES6), with no external dependencies (other than xeokit).

## Demos 

| Live Demo | Model Source |
|---|---|
| [OTC Conference Center](https://xeokit.github.io/xeokit-viewer/index.html?project=OTCConferenceCenter&tab=storeys) | [Details](http://openifcmodel.cs.auckland.ac.nz/Model/Details/301) |
| [Holter Tower](https://xeokit.github.io/xeokit-viewer/index.html?project=HolterTower&tab=storeys)| [Details](http://openifcmodel.cs.auckland.ac.nz/Model/Details/316) |
| [West Riverside Hospital](https://xeokit.github.io/xeokit-viewer/index.html?project=WestRiversideHospital&tab=models)| [Details](http://openifcmodel.cs.auckland.ac.nz/Model/Details/308) |
| [Schependomlaan](https://xeokit.github.io/xeokit-viewer/index.html?project=Schependomlaan&tab=storeys)| [Details](https://github.com/openBIMstandards/DataSetSchependomlaan) |
| [Duplex](https://xeokit.github.io/xeokit-viewer/index.html?project=Duplex&tab=storeys)| [Details](http://openifcmodel.cs.auckland.ac.nz/Model/Details/274) |
| [Duplex and Conference Center](https://xeokit.github.io/xeokit-viewer/index.html?project=ManyModels)| [Duplex](http://openifcmodel.cs.auckland.ac.nz/Model/Details/274), [Conference Center](http://openifcmodel.cs.auckland.ac.nz/Model/Details/301) |

## Roadmap

- [x] Standalone viewer MVP
- [x] Integration into [OpenProject](https://www.openproject.org/)
- [ ] Save and load BCF viewpoints
- [ ] Measurement tools
- [ ] Memory usage monitoring
- [ ] Explode tool  
- [ ] Ambient shadows

Do you have feedback, or features you'd like to see in the viewer? Let us know in the [issue tracker](https://github.com/xeokit/xeokit-viewer/issues).  
  
## License

xeokit-viewer is bundled with the xeokit SDK, which is provided under an [Affero GPL V3](https://github.com/xeokit/xeokit-sdk/blob/master/LICENSE.txt) dual-license, which allows free use for non-commercial purposes, with the option to buy a licence for commercial use. Please [see here](https://xeokit.github.io/xeokit-licensing/) for commercial licensing options.

## Usage

### Model Database

The viewer loads models from the file system by default. These are contained within the [./data](https://github.com/xeokit/xeokit-viewer/tree/master/data) directory, which also contains a number of sample models to get you started. 

Each model consists of an ````.XKT```` binary geometry file and a JSON metadata file which classifies its IFC elements. 

Models are grouped within *projects*. Each project can contain multiple models, and has a JSON ````index.json```` manifest which lists its models.

At the root of ````./data```` is a JSON ````index.json```` manifest that lists all the projects.

The directory structure is designed to support RESTful queries, ie:


| Query  | Path |
|---|---|
| Get all projects | ````GET ./data/index.json```` |
| Get project | ````GET ./data/WestRiversideHospital/index.json````  |
| Get model geometry | ````GET ./data/WestRiversideHospital/electrical/geometry.xkt```` |
| Get model metadata | ````GET ./data/WestRiversideHospital/electrical/metadata.json```` |

Shown below is a portion of the ````./data```` directory, showing the directory structure.

````
./data/
└── projects
    ├── index.json
    ├── Duplex
    │   ├── index.json
    │   └── models
    │       └── design
    │           ├── geometry.xkt
    │           ├── issues.json
    │           └── metadata.json
    └── WestRiversideHospital
          ├── index.json
          └── models
              ├── electrical
              │   ├── geometry.xkt
              │   └── metadata.json
              ├── fireAlarms
              │   ├── geometry.xkt
              │   └── metadata.json
              ├── plumbing
              │   ├── geometry.xkt
              │   └── metadata.json
              ├── sprinklers
              │   ├── geometry.xkt
              │   └── metadata.json
              └── structure
                  ├── geometry.xkt
                  └── metadata.json
````

The ````index.json```` at the root of ````./data```` shown below. The ````id```` of each project matches the name of that project's subdirectory. 

````json
{
  "projects": [
    {
      "id": "Duplex",
      "name": "Duplex",
      "position": [-20, 0.0, -10.0],
      "scale": [1.0, 1.0, 1.0],
      "rotation": [0.0, 0.0, 0.0]
    },
    {
      "id": "WestRiversideHospital",
      "name": "West Riverside Hospital",
      "position": [20, 0.0, 0.0],
      "scale": [1.0, 1.0, 1.0],
      "rotation": [0.0, 0.0, 0.0]
    },
    //...
  ]
}
```` 

The ````index.json```` for the "WestRiversideHospital" project is shown below. The ````id```` of each model matches the name of that model's subdirectory, while ````name```` is the string that's displayed for the model in the viewers Models tab.

````json
{
  "id": "WestRiversideHospital",
  "name": "West Riverside Hospital",
  "models": [
    {
      "id": "structure",
      "name": "Hospital Structure",
      "default": true
    },
    {
      "id": "electrical",
      "name": "Hospital Electrical"
    },
    {
      "id": "sprinklers",
      "name": "Hospital Sprinklers"
    },
    {
      "id": "plumbing",
      "name": "Hospital Plumbing"
    },
    {
      "id": "fireAlarms",
      "name": "Hospital Fire Alarms"
    }
  ]
}
````

#### Adding your own models

To add your own project to the database, you need to: 

 * add a new project directory within ````./data````,
 * add a subdirectory within that for each model, containing each model's ````.XKT```` and metadata files,
 * add a ````index.json```` manifest of the models within the project directory, which lists the models, and
 * list your project in the ````index.json```` at the root of ````./data````.    

#### Loading models from a custom source

To load models from a different source than the file system, configure

````javascript


````

### Customizing CSS

The [index.html](https://github.com/xeokit/xeokit-viewer/blob/master/index.html) file for the standalone viewer contains CSS rules for the various viewer elements, which you can modify as required.

### Tooltips

Tooltips are not part of the core JavaScript for the viewer. Instead, viewer HTML elements are marked with ````data-tippy-content```` attributes that provide strings to show in their tooltips. 

For example, the *Toggle 2D/3D* button's element looks like this:

````html
<button type="button" class="xeokit-threeD xeokit-btn fa fa-cube fa-2x" data-tippy-content="Toggle 2D/3D"></button>
```` 

In the [index.html](https://github.com/xeokit/xeokit-viewer/blob/master/index.html) file for the standalone viewer, we're using [tippy.js](https://github.com/atomiks/tippyjs), which automatically creates tooltips for those elements.

### Customizing Appearances of IFC Types

The viewer loads colors for the various IFC element types straight from the IFC model, except where overrides are defined in the configuration file [src/IFCObjectDefaults/IFCObjectDefaults.js](src/IFCObjectDefaults/IFCObjectDefaults.js).

You can add or remove configurations in that file if you need to customize the color or pickability of specific IFC types.

For example, to ensure that ````IfcWindow```` and ````IfcSpace```` types are initially visible, transparent and pickable (ie. able to be selected by clicking on them), you might configure that file as shown below:

````javascript
const IFCObjectDefaults = {
    IfcSpace: { 
        visible: true,
        pickable: true,
        opacity: 0.2
    },
    IfcWindow: { 
        visible: true,
        pickable: true,
        opacity: 0.5
    }
};

export {IFCObjectDefaults};
```` 
 
Sometimes IFC models have opaque ````IfcWindow```` and ````IfcSpace```` elements, so it's a good idea to have configurations in there so that we can see through them.
 
## Building 

Initialize:

````
sudo npm install
````

Building ES6 module in ````/dist/main.js````:

````
npm run build
````

Then, within ````index.hml````, we use the module like so:

````javascript
import {Server, ViewerUI} from "./dist/main.js";

const server = new Server({
    dataDir: "./data/"
});

const viewerUI = new ViewerUI(server, {
    //...
});
````
