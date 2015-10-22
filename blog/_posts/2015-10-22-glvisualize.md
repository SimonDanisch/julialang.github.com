# GLVisualize.jl: Interactive Visualizations in Julia

**Simon Danisch**

GLVisualize is an interactive visualization library that supports 2D and 3D rendering as well as building of basic GUIs. It's written entirely in Julia and OpenGL.
I'm really glad that I could continue working on this project with the support of Julia Summer of Code.

During **JSoC**, my main focus was on advancing [GLVisualize](https://github.com/JuliaGL/GLVisualize.jl), but also improving the surrounding infrastructure like [GeometryTypes](https://github.com/JuliaGeometry/GeometryTypes.jl), [FileIO](https://github.com/JuliaIO/FileIO.jl) and [FixedSizeArrays](https://github.com/SimonDanisch/FixedSizeArrays.jl).
All recorded gifs in this blog post suffer from lossy compression. You can click on most of them to see the code that produced them.

One of the most interesting parts of **GLVisualize** is, that it's combining GUIs and visualizations, instead of relying on a 3rd party library like **QT** for GUIs.
This has many advantages and disadvantages.
The main advantage is that interactive visualization shares a lot of infrastructure with GUI libraries.
By combining these two, new features are possible, e.g. text editing of labels in 3D space, or making elements of a visualization work like a button. These features should end up pretty snappy, since GLVisualize was created with [high performance]() in mind.
The biggest downside is, that it will be hard to reach the maturity and feature completeness of e.g. **QT**.
So it is a lot of work, but there is a large potential payoff.

What have I been doing during Julia summer of code?
A surprisingly large amount of time went into improving FileIO together with [Tim Holy](https://github.com/timholy).
[FileIO is a library to support drag and drop operations (?)]. The selling point of FileIO is that it recognizes the format of imported files and loads the respective IO library. This makes it easy to start working with files in Julia, since no prior knowledge [about what?] is needed.
This is perfect for a visualization library as most visualizations start from data, maybe of unknown format, serialized on a disk.
Since all files are loaded via the same function it also reduces the burden to implement operations like drag and drop.
[Ich bin mir nicht sicher, ob ich den zuvorgehenden Satz richtig verstehe:] The implementation of drag and drop functionality becomes easy, as all file types can be loaded using a unified function.
To give you an example, the implementation of drag and drop in GLVisualize only needs a [few lines of code](https://gist.github.com/SimonDanisch/e0a8a2cbc3106ce6c123#file-dragndrop-jl) thanks to FileIO:

[![drag and drop](https://github.com/SimonDanisch/Blog/blob/master/10-22-15-jsoc/dragndrop2.gif?raw=true)](
https://gist.github.com/SimonDanisch/e0a8a2cbc3106ce6c123#file-dragndrop-jl
)

Another feature I've been working on is better 2D support.
I've implemented different anti-aliased marker, text rendering and line types.
They all use the [distance field technique](http://www.valvesoftware.com/publications/2007/SIGGRAPH2007_AlphaTestedMagnification.pdf) to achieve view independent anti-aliasing.
Here are a few examples:

![lines](https://github.com/SimonDanisch/Blog/blob/master/10-22-15-jsoc/lines.png?raw=true)
[![markers](https://github.com/SimonDanisch/Blog/blob/master/10-22-15-jsoc/markers.gif?raw=true)](
https://github.com/SimonDanisch/Blog/blob/master/10-22-15-jsoc/marker.jl
)

In the last example, you notice that all the markers move together. This is actually a cool feature, since they share the same memory for the positions on the GPU without any overhead. [für mich gerade nicht verständlich ohne genauere erklärung] I just added an offset for each of the markers.
This is easily achieved in GLVisualize, since all visualization methods are defined on the GPU objects.

The latest version of GLVisualize also adds UI widgets for sliders and line editing.
We can use these to add interactivity to parameters of a visualization:

[![line_edit](https://github.com/SimonDanisch/Blog/blob/master/10-22-15-jsoc/volume_color.gif?raw=true)](
https://github.com/SimonDanisch/Blog/blob/master/10-22-15-jsoc/color_volume.jl
)
[![arbitrary_surf](https://github.com/SimonDanisch/Blog/blob/master/10-22-15-jsoc/arbitrary_surf.gif?raw=true)](
https://github.com/SimonDanisch/Blog/blob/master/10-22-15-jsoc/arbitrary_surf.jl
)

I have also worked with [David P. Sanders](https://github.com/dpsanders) to visualize his [billiard model](https://github.com/dpsanders/BilliardModels.jl), which demonstrates the particle system and a new camera type.

[![billiard](https://github.com/SimonDanisch/Blog/blob/master/10-22-15-jsoc/billiard.gif)](
https://github.com/SimonDanisch/Blog/blob/master/10-22-15-jsoc/billard.jl
)

You can see that there are some GUI widgets to interact with the camera.
The small rectangles in the corner are switching between orthographic and perspective projection. The cube can be used to center the camera on a particular side.
These kind of widgets are easy to implement in GLVisualize, as it is build for interactive GUIs from the beginning. 
Better camera controls are a big usability win, and I will put more time into improving these even further.

I recorded one last demo to give you an even better understanding of what is possible with GLVisualize:

![interactivity](https://github.com/SimonDanisch/Blog/blob/master/10-22-15-jsoc/interactivity.gif?raw=true)

The demo shows different kind of animations, 3D text editing and pop ups that are all easy to implement with GLVisualize editing primitives.

All of this looks promising, but there is still a lot of work needed!
First of all, there is no tagged version of GLVisualize that offers a binary installer [was genau meinst du hier mit "can just be installed"?].
Versions of [Reactive.jl](https://github.com/JuliaLang/Reactive.jl) and [Images.jl](https://github.com/timholy/Images.jl) that are compatible with GLVisualize are not tagged yet.

GLVisualize's API also need further work. The goal here is to support the *Compose* interface at some point.
For this, it is planned to port ideas from [Escher.jl](https://github.com/shashi/Escher.jl) and [Compose.jl](https://github.com/dcjones/Compose.jl).
With an implementation of the *Compose* interface GLVisualize can be used as a backend for Gadfly. This will make Gadfly much fitter for large, animated data sets.
In the next weeks I will work on tutorials, documentation and better handling of edge cases.
Stay tuned for updates and thanks to the Julia team and everyone involved for making this possible!
