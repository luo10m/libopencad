# libopencad

Ubuntu 14.04 (gcc 4.8 + clang): [![Build Status](https://travis-ci.org/sandyre/libopencad.svg?branch=master)](https://travis-ci.org/sandyre/libopencad)

OpenSource library under X11/MIT license for everyday use.
Under development, but some of basic functionality is already available. This project is being developed by Alexandr Borzykh ([sandyre](https://github.com/sandyre)) under mentorship of Dmitry Baryshnikov ([NextGIS](http://nextgis.ru/en/)).

Current project state:

| DWG 	| Read 	| Write 	| Features 	| Missing 	|
|--------	|------	|-------	|------------------------------------------------------------------------------------	|-----------------------------------------------	|
| R13-14 	| - 	| - 	| - 	| - 	|
| R15 	| + 	| - 	|  Reading of layers.Basic geometry reading. Exporting header variables and classes. 	| Linetypes, CRC calculation, by-block reading. 	|
| R17 	| - 	| - 	| - 	| - 	|

## Getting started

First you need to download repository

```sh
git clone https://github.com/sandyre/libopencad
```

### Static library way

Then, run cmake

```sh
cmake CMakeLists.txt
```

Build the project

```sh
make -j4
```

At this point, you will have a static library at lib/libopencadstatic.(your OS static library extension)
All you have to do now - is to link library with your project, and include associated header files - opencad_api.h, cadgeometries.h and others.

### Dynamic library way

Then, run cmake

```sh
cmake -DBUILD_SHARED_LIBS=ON CMakeLists.txt
```

Build the project

```sh
make -j4
```

At this point, you will have a dynamic library at lib/libopencad{library version}.(your OS dynamic library extension)
All you have to do now - is to link library with your project, and include associated header files - opencad_api.h, cadgeometries.h and others.

### Library sources inclusion way

All you have to do is to link the lib/ directory to your project file tree, that's actually it.

### Usage example

As an example of library usage, there is a built-in app called cadinfo (builds by default with library, available in apps/ directory).

```cpp
#include <iostream>
# include "lib/opencad_api.h"

// returns nullptr on fail. GetLastErrorCode() returns an error code.
CADFile *pCADFile = OpenCADFile( pszCADFilePath,
                                      CADFile::OpenOptions::READ_ALL );

const CADHeader& header = pCADFile->getHeader ();
header.print (); // prints CAD Header variables.
cout << endl;

const CADClasses& classes = pCADFile->getClasses ();
classes.print (); // prints custom CAD classes
cout << endl;

for ( size_t i = 0; i < pCADFile->getLayersCount (); ++i )
{
    CADLayer &layer = pCADFile->getLayer (i);
    cout << "Layer #" << i << " contains "
         << layer.getGeometryCount () << " geometries" << endl;

    for ( size_t j = 0; j < layer.getGeometryCount (); ++j )
    {
        unique_ptr<CADGeometry> geom(layer.getGeometry (j));

        if ( geom == nullptr )
            continue;

        switch ( geom->getType() ) // returns GeometryType enum.
        {
            case CADGeometry::CIRCLE:
                CADCircle * poCADCircle = ( CADCircle* ) geom.get();
                std::cout << poCADCircle->getPosition().getX() << std::endl;
                std::cout << poCADCircle->getPosition().getY() << std::endl;
                std::cout << poCADCircle->getPosition().getZ() << std::endl;
                break;
            // any other geometry type you need.
        }
    }
}
```

## Contribution

Feel free to submit an issue, or make a pull request. To begin with, it's better to fix some FIXME/TODO's, to get more familiar with code base.

## Library documentation

Documentation is generated by Doxygen, available at this [link](http://sandyre.github.io/libopencad/docs/html/index.html)
