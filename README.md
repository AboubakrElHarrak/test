![](attachments/banner.png)

# Data Structure Course Design Topic 2 - Realization of Campus Navigation Map

## 1. Design content and requirements

1. Design a school campus floor plan with no less than 30 selected nodes. The vertices in the figure represent various scenic spots on the campus, and store information such as the name, code, and introduction of the scenic spots; the edges represent paths, and store relevant information such as path length.
2. Provide customers with information inquiries about any scenic spot in the picture.
3. Provide customers with directions for any scenic spot in the picture, that is, the shortest path between any two scenic spots, and give the specific path and path length.
4. In addition to completing basic functions, it is best to provide real-time real-time path query, such as the calculation of the shortest path after a path is temporarily closed (to be implemented in the program).
5. Submit the design report, which includes code, design ideas (flow chart) and screenshots of the implementation interface.

## Two, needs analysis

In the design content and requirements, the focus is on the calculation and display of the shortest path between two points. In addition, in order to better present the results of campus navigation, I plan to design a graphical user interface for displaying the results.

Additionally, an "Administrator Mode" needs to be provided. When the user opens the administrator mode, he has the right to disable some paths.

## 3. Finished product display

![](attachments/campus navigation system main view.png)

### 1. Attraction information query

Spots can be selected in three ways.

- Click on the red dot on the map;
- Select from the "Start" or "End" drop-down box on the right;
- Enter the name of the scenic spot in the drop-down box of "Start" or "End" on the right, and the name of the scenic spot must exist.

After selecting the attraction, the "Introduction" tab on the right will display the **attraction name** and the **description** of the attraction.

![](attachments/attraction information query.png)

### 2. Shortest path query between attractions

After you have selected two scenic spots as the starting point and the ending point, you can click the "Calculate Route" button.

![](attachments/Click the "Calculate Path" button.png)

After that, the program will automatically jump to the "Route" tab and display the distance between the two points.

![](attachments/Jump to "route" and display distance.png)

### 3. Admin mode

Right-click the title bar **Zijin College Logo** to switch to administrator mode (right-click again to switch back to normal mode).

![](attachments/right click Zijin College Logo.png)

After entering the administrator mode, the color of the title bar will change to a striking blue for distinction.

![](attachments/administrator mode interface.png)

At this point, all routes will be displayed. Those marked in green are available, and those marked in red are disabled.

Clicking on a route with the mouse toggles the available status of that route.

![](attachments/toggle route available status.png)

The program will save its state in real time. You don't have to worry about losing your modifications if you accidentally close the program.

## Four, get started quickly

This project uses C# language to write the main program, and uses Python language to write auxiliary scripts.

After you have the source code, it is recommended to open the solution `SchoolNavigator.sln` with Visual Studio 2022.

### 1. Install C# environment dependencies

> [!warning] Note
>
> The following operations are based on the fact that you have installed `.NET Core SDK 6.0` and installed C# related workloads in Visual Studio 2022.
>
> - To install .NET Core SDK 6.0, please go to [Microsoft Official Download](https://dotnet.microsoft.com/zh-cn/download/dotnet/6.0);
> - If you have not installed the C# workload, please use `Win + S` to search `Visual Studio Installer` in the installed application, and then set it in the selection menu after opening. Detailed steps can be found online if necessary.

Please install the following dependencies in the built-in NuGet package management tool of Visual Studio:

- `Newtonsoft.Json`
- `MaterialDesignThemes`

### 2. Install Python environment dependencies

> [!warning] Note
>
> The following operations are based on any Python environment where you have installed `Python 3.6` and above.
>
> - If you need to install Python, please go to the official website to download, it is recommended to install the latest version of Python.

Please open `SchoolNavigator` where the project is located in an IDE or editor that supports Python, such as Visual Studio Code, PyCharm or another Visual Studio window.

You should see two Python files inside the folder, namely:

- `JsonDataScripts.py`
- `certificate_all_paths.py`

In the current folder, create a new Python virtual environment:

```cmd
py -m venv venv
```

> [!important]- This tutorial takes Windows 10/11 system as an example
>
> The activation instructions here will vary depending on the type of **OS** or **console** you are using. If the installation fails, use a search engine to find the solution, making sure you have Python installed correctly.

Then activate the Python virtual environment venv:

```cmd
.\venv\Scripts\Activate.ps1
```

In the virtual environment venv, install the following Python packages:

- `svgpathtools`

That is, using the command:

```cmd
pip install svgpathtools
```

### 3. Start the Python script to generate data

In this project, the C# code requires a data file located at `.\data\graph.json`. This file is generated by `JsonDataScripts.py` using `SchoolMap.svg`. So, first start the Python script to generate the data:

```cmd
py JsonDataScripts.py
```

If you see the output `Success.`, the generation is complete.

At this point, the `graph.json` file should be visible in the `.\SchoolNavigator\data\` directory.

### 4. Start the C# program

Back to Visual Studio, after the dependencies are installed, press `F5` or click "SchoolNavigator" to start in Debug mode.

When the main window pops up, the startup is successful. Please get started.

## 5. Outline Design

Outline design involves early technical learning and testing. In the finalized architecture, the following core technical points are covered:

- GUI
- Data acquisition and data processing (emphasis)
- Implementation of the shortest path algorithm (emphasis)
- Display route and node information in the user interface
- Admin mode

### 1. Graphical interface design and implementation

#### Technology selection

At the beginning of the project, I planned to write the backend in C++ language and use the Qt framework to make the graphical interface. C++ is my first language, but after some study and evaluation, I found that the development mode of C++ Qt can't help me quickly create a beautiful and simple interface. On the contrary, using C++ will also make me spend more energy on some obscure syntax and unnecessary low-level implementation.

Therefore, I finally chose the C# language for development, and the front end uses WPF with a relatively mature system for development.

#### interface design

In the interface design part, the first is a large map of Zijin College on the left. The right side is the main function area, which undertakes all the functions of selecting attractions, displaying information of attractions and displaying the distance between attractions.

![campus navigation system main view](attachments/campus navigation system main view.png)

In order to optimize the user experience, I also made the punctuation of the attractions displayed on the map clickable. Users can choose a variety of ways to input the start point and end point.

#### Dynamic display of attraction information

Besides that, I tried to make the user experience and interaction experience as friendly and intuitive as possible. for example:

- When selecting the start point and end point, the detailed information of the scenic spot will be displayed automatically;
- When you click to calculate the route, it will automatically jump to the "Route" tab and display the distance between two points.

### 2. Obtain information from the map: data collection and data processing

Points on the map don't just appear out of thin air. Especially for navigation systems that require a graphical interface, it is necessary to accurately describe the location of each node.

#### Import nodes and paths from the artboard

The first step is to pull a large map covering the entire school from software such as Gaode Map and Baidu Map. First trace all the routes in Adobe Illustrator as a path; at the same time, trace each intersection as a node.

After exporting the contents of the artboard in `.svg` format, use algorithms to extract key information of paths and nodes.

#### Use scripts to extract data and convert it into object information

Program in Python, write the path information provided in `.svg` format: path start point, end point, way point; and node information: coordinate x, coordinate y, and write it into the `.json` file according to your own conventions for the library table.

#### Read in all data at final program startup

When the program is initialized, all the data in the `.json` file is read and stored in memory.

So far, the collection and processing of map information has been completed. We've got:

- All nodes: including coordinate x, coordinate y;
- All routes: including starting point, ending point, en route, distance.

### 3. Implementation of the shortest path algorithm: an improvement on Dijkstra's algorithm

#### Get the shortest path and path nodes at the same time

There are two types of traditional Dijkstra return values, either returning the shortest path or returning the predecessor node. And in my program, because the specific path needs to be displayed in the user interface, that is to say, the path is stored in memory in the form of an object. This requires that my program must be able to find the path through the predecessor node.

#### Load the route distance in advance in the program

According to the above, the coordinates and distance information of the route are stored in the local `.json` file, and are read into the memory during initial loading. The Dijkstra algorithm needs a two-dimensional matrix with the node index as the horizontal and vertical coordinates. The value of a coordinate is the distance.

For example, the value of point `a[1][2]` represents the distance from the point with index `1` to the point with index `2` is `a[1][2]`.

If this part of data is stored in the hard disk, not to mention the need to generate this data through Python scripts in the early stage, the speed of reading data from the hard disk is far slower than reading it directly from the memory. Therefore, the solution I adopted in the project is to generate this matrix in memory after initializing and reading the data file.

### 4. Display route and node information in the user interface

The focus here is to make the user experience more natural and smooth. And after pressing the button, there should be a corresponding event to respond.
By implementing some `Handler` methods by myself and using some `static` variables, I implemented an observer mode delegation system in disguise.

The display of detailed information and the front-end design of buttons, input boxes, etc. are some common knowledge, so I won’t expand them here.

### 5. Admin Mode: Disable routes

Get the extra ability to disable routes when admin mode is enabled. Obviously, this is also achieved by setting a `static` variable as a flag.

At the same time, when exiting the administrator mode, the set content will be saved.

## Six, detailed design

In the detailed design, the key points mentioned in the summary design will be explained, and the key parts will be explained with code assistance.

### 1. Database table design

- path `Path`:

| name | field | type | primary key? | Can be empty? |
| ------------ | ---------------- | --- | ------ | ------ |
| index | `Id` | `int` | is | |
| name | `Name` | `string` | | is |
| length | `Distance` | `double` | | |
| Starting point ID | `StartVerticeId` | `int` | | |
| Endpoint ID | `EndVerticeId` | `int` | | |
| front-end data description | `Data` | `string` | | yes |
| Whether to enable | `IsEnabled` | `bool` | | |

- Node `Vertice`:

| name | field | type | primary key? | Can be empty? |
| ----- | ---- | ------ | ------ | ------ |
| index | `Id` | `int` | is | |
| name | `Name` | `string` | | |
| coordinate x | `X` | `double` | | |
| coordinate y | `Y` | `double` | | |

- Attraction `Location`: (inherited from `Vertice`)

| name | field | type | primary key? | Can be empty? | Inherit from parent class? |
| ----- | ------ | -------- | ------ | ------ | ------------ |
| index | `Id` | `int` | is | | is |
| name | `Name` | `string` | | | is |
| coordinate x | `X` | `double` | | | is |
| coordinate y | `Y` | `double` | | | is |
| description | `Info` | `string` | | is | |

### 2. Data acquisition and data processing

First, it is necessary to obtain path and node information based on the real school map.

#### Using Adobe Illustrator to Trace Vectors

![](attachments/Ai vector graphics.png)

Draw all the nodes and paths on the vector graphics software. Nodes are further divided into ordinary nodes and scenic spots. The above three are located on three layers:

- `Locations` attractions;
- `Vertices` node;
- `Paths` paths.

Because Adobe Illustrator defines strokes as `path` curves when converting to `.svg` format images, the sights and nodes represented by circles cannot have strokes, and paths cannot have fills.

So far, the formats of the three elements are (take the representation in the figure as an example):

| Category | Stroke | Stroke Color | Stroke Thickness | Fill | Fill Color |
| ---- | ---- | -------- | -------- | ---- | -------- |
| Attractions | No | - | - | Yes | Blue |
| Node | No | - | - | Yes | Gray |
| path | yes | black | 3px | no | - |

Save the drawn vector graphics in `.ai` format for subsequent adjustment and modification, and export `.svg` format for conversion.

In the `SchoolMap.svg` file, the scenic spots, nodes, and paths are expressed in the following forms (for example):

```xml
<!-- attractions -->
<g id="Location">
     <circle id="_Dormitory Building 3" data-name="Dormitory Building 3" class="cls-1" cx="421.5" cy="55.5" r="8"/>
</g>

<!-- node -->
<g id="Vertice">
     <circle id="_0-6" data-name="0" class="cls-3" cx="532" cy="55" r="3"/>
</g>

<!-- path -->
<g id="Path">
     <polyline class="cls-2" points="545 459 637.5 408.5 664 388"/>
     <line class="cls-2" x1="708" y1="471" x2="664" y2="388"/>
</g>
```

The path will be represented as `line` and `polyline` respectively, depending on whether it is a straight line or a polyline segment.

When drawing paths, don't use Bezier curves (don't use the pen's anchor point modification tool in Adobe Illustrator). This is because,

1. When introducing a curve, it is difficult to determine the starting point and focus of the path, which is not conducive to the conversion and use when importing the program later;
2. For a Bezier curve, the time complexity of calculating its length attribute is much higher than that of a straight line or polyline segment;
3. In the background image of the Gaode map, they use polyline segments instead of Bezier curves.

#### Convert SVG encoding in Python

First create a dictionary to store the converted data:

```python
graph = {
     "Vertices": [], # Vertices list
     "Paths": [], # list of paths
     "Locations": [] # List of attractions
}
```

All path information and attributes in an SVG document can be obtained through the `svg2paths` function of the `svgpathtools` package:

```python
paths, attributes = svg2paths("SchoolMap.svg")
```

Both `paths` and `attributes` are list types, and they are listed in the order of the elements in the SVG from top to bottom.

Before the conversion operation, `paths` and `attributes` need to be classified. Because the `svg2paths` function will save all paths and nodes together, we need to split them one by one according to their different characteristics:

```python
# Define the list of receiving paths, nodes, and attractions
outputPaths = []
outputVertices = []
outputLocations = []

for i in range(len(paths)):
     if "r" not in attributes[i].keys():
         outputPaths.append(paths[i]) # path characteristics: no radius
     else:
         # This class is the style sheet of SVG, the specific name will vary according to your actual situation
         # Use different styles to distinguish nodes and attractions
         if attributes[i]["class"] == "cls-3":
             outputVertices.append(attributes[i])
         else:
             outputLocations.append(attributes[i])
```

For each node `Vertice`, `attributes` contains `data-name`, `cx`, `cy` attributes.

```python
verticesCount = 0
for i in outputVertices: # This outputVertices list contains attributes of all nodes
     graph["Vertices"].append({
         "Id": verticeCount, # Add an index to each node, starting from 0
         "Name": i["data-name"], # node name
         "X": float(i["cx"]), # node coordinate x
         "Y": float(i["cy"]), # node coordinate y
     })
     verticeCount += 1
```

We store these features as a dictionary. When each node is imported into a C# program, it will become a field of the corresponding `Vertice` object.

---

The same is true for path `Path`:

```python
pathCount = 0
for i in outputPaths: # This outputPaths list contains paths of all paths
     graph["Paths"].append({
         "Id": pathCount, # Add an index to each path, starting from 0
         "Name": f"Path_{pathCount}", # Path name, not required here for subsequent use
         "Distance": i.length(), # length of the path
         "StartVerticeId": -1, # path starting point (indicated by node index)
         "EndVerticeId": -1, # path end point (same as above)
         "Data": i.d(), # Description of the path (for front-end display)
         "IsEnabled": True, # Whether the path is enabled (can be disabled in admin mode)
     })
     pathCount += 1
```

Note that it is not the `attributes` attribute that is required for paths, but `paths`.
Therefore, the return value of the `svg2paths` function is a pair of tuples, which include path attributes (for points, the circle is described in the form of a curve) and labels, which can be used in different situations.

In addition, the start and end points of the path need to be calculated with special functions, and these can only be done after all nodes (including nodes and scenic spots) are entered. So it is set to `-1` here first. Later, tools can be used to check for missing paths.

Regarding how to find the start and end points of the path, it is relatively simple to implement. I won’t go into details in this document, and interested readers can go to the given `JsonDataScripts.py` source code to read.

---

The location `Location` is special because the location has a "description" ( `Info`) field, which is impossible to include in an SVG file. Therefore, you can only add it with Python after entering Python. However, if you want to add in Python, you need to obtain the index or name first, so in the process of entering the scenic spot, you need to repeatedly start the Python script twice:

```python
for i in outputLocations:
graph["Locations"].append({
"Id": verticeCount,
"Name": i["data-name"],
"X": float(i["cx"]),
"Y": float(i["cy"]), # All of the above are consistent with Vertice
"LocatedVerticeIds": [], # These two lists are deprecated
"LocatedPathIds": [],
"Info": "" if ADDINFO else locationInfo[i["data-name"]], # Important
})
verticeCount += 1 # numbered consecutively with Vertice nodes
```

Pay attention to this key point:

```python
"Info": "" if ADDINFO else locationInfo[i["data-name"]]
```

`ADDINFO` is a boolean and defaults to `False`. `locationInfo` stores the corresponding description information for each scenic spot.
You can use the name of the scenic spot `Name` to find the corresponding description information `Info`.

```python
locationInfo = {
     "Big Playground": "...",
     "Second canteen": "...", # Just an example, you can check the details in the source code
}
```

Before starting the script for the first time, the `keys` in `locationInfo` need to be generated.
After changing `ADDINFO` to `True`, the program no longer writes data to `graph.json`, but outputs a list containing all `Location` attractions names. You can copy-paste it directly into the braces of `locationInfo` in your Python code.

```python
DEBUG = not ADDINFO
if DEBUG:
# output to graph.json ...
else:
for location in graph["Locations"]:
    print(f',"{location["Name"]}": \t\t\t""') # Print the names of attractions in turn
```

After editing all attractions description information `Info`, reset `ADDINFO` to `False`, and the program can start writing data to `graph.json`.

#### Read data file in C# program

After the data processing is completed, the data import work is very simple. It's as simple as just one line of code to do all the work:

```cs
public partial class MainWindow : Window
{
     private Graph? _graph;
     //...
        
     public MainWindow()
     {
         // read data from file
         var jsonString = File.ReadAllText(@"./data/graph.json");

         // convert to object
         _graph = JsonConvert. DeserializeObject<Graph>(jsonString);

         // Initialize the Dijkstra matrix
         _graph. InitializePathWeights();
     }
}
```

So far, all data collection and data processing work has been completed.

### 2. Optimize and modify the traditional Dijkstra algorithm

To achieve the calculation of the shortest path, there are roughly three algorithms that can be used, namely:

- DFS depth-first search;
- Dijkstra;
-A*

Among them, Dijkstra's algorithm will calculate the shortest path information of all other points from a starting point. Therefore, Dijkstra's algorithm does not need to consume any additional calculations when the starting point does not change but only the end point.

This also makes Dijkstra's comprehensive time complexity the shortest among the three. After using the priority queue (binary heap) to optimize the internal data structure of the algorithm, Dijkstra's search time complexity for the shortest path to a single destination is $O(N \log N)$, and the search time complexity for all start and end points Then multiply it by $N$.

#### Implementation of Dijkstra's Algorithm

There are two return values of Dijkstra's algorithm, one is the shortest path from the starting point to each point, and returns an array. Each element of the array represents the required distance from the starting point to the node whose index is the subscript of the array. For example: we select the point where the index `0` is located as the starting point, then `distance[1]` means starting from the starting point with the index `0` to the end point with the index `1`, the distance is `distance[1]`. Therefore, Dijkstra's algorithm is also called the single-source shortest path algorithm.

Another return value of Dijkstra's algorithm is the predecessor node of each terminal. In other words, when we calculate the shortest path from the starting point to all destinations, for each destination, its previous point on the path is always determined. The previous node is called the predecessor node. Dijkstra's algorithm can return the predecessor node for each end point and save it in the form of an array.

#### Return both the shortest path and the predecessor node

In general command line programs, only one final result is needed to find the shortest path. But if it is necessary to display the route on a graphical interface, each route is an object that exists in memory. Since I want to display this object on the screen, obviously I have to get all the nodes I have passed, and then find all the routes.

```cs
public Tuple<double[], int[]> Dijkstra(int startIndex);
```

In the definition of Dijkstra's algorithm, the return value of this program is a `(distance, prePoint)` tuple. Store distance and predecessor nodes separately. (Note that the distance must be of `double` type).

At the same time, this method is stateful. The matrix of distances between two points is stored in the `Graph` object during program initialization.

```cs
public class Graph
{
     public double[][] PathWeights;
}
```

At this time, only one starting point needs to be obtained to calculate the positions of all end points.

Then push back all the routes according to the end point.

### 3. System Test

The user interaction test of this program requires you to try in sequence according to the operations in the "Product Display" section above.

In the dataset section, you can use any school/location's routes as data. But you need to draw your own map and provide the route and node information of the map. After you have prepared this part of `.svg` map data, you can follow the instructions in the "Quick Start" section and replace `.\images\SchoolMap.jpg` with the corresponding map (the resolution must be 1200x900) .

## Seven, self-summarization

It took about 3 days to implement the underlying core functions of this project, 2 days to design and improve the front-end user interface, and the subsequent continuous improvement and optimization lasted about a week. Anyway, this is one of the most complex and challenging projects I have done this year.

### 1. Problems encountered in the implementation process

#### UI aspects

First is the question of visualization. When designing the user interface in the early stage, I tried to implement a UI by myself through WPF handwriting. Later, I found that the UI implemented in this way is not good-looking and difficult to design, and it cannot reach a relatively high degree of completion.

![Early Project UI](attachments/Early Project UI.png)

When refactoring with Material Design Themes later, it is too late to make major adjustments to the basic interface layout. At the same time, the underlying code of the UI is relatively chaotic and highly coupled.

#### XAML conversion script

Following the UI part above, the UI of the original interface is completely implemented by the XAML code of C#. For example, for each node or path, it is 5 lines of XAML code. As you can imagine, for hundreds of nodes and paths, this is thousands of lines of code. My idea at the time was to generate these front-end codes through Python scripts. Although this method can indeed produce results, it is definitely not a correct programming habit.

In fact, behind every XAML element is a C# object, and I can program them directly in C# to realize these functions. This way I can take full advantage of the advantages that the programming language has, such as `for` loops, direct reading of data, etc.

In the end this helped me optimize the readability of the code, and also helped me get rid of redundant database table fields and achieve streamlining.

### 2. Functions that can be added and optimized

#### Increase the preference function of path calculation

In the process of designing the test, I found that sometimes although the calculated absolute path is the shortest, it is not always the best result. For example, sometimes from the starting point to the end point, it is necessary to pass through the interior of several buildings, and it is unknown whether the internal structure of these buildings is clear, whether it is allowed to pass, or whether it is open. Therefore, I have provided a "walk through building" option, which when selected will provide a route that allows access to the interior of the building.

Likewise, there's Highway Only, for cars and large groups, and Attractions First, for off-campus visitors and leadership inspections.

These functions are all extensions based on the core functions. There is no need to change the original database fields, just add some judgments when entering the called method. However, due to time constraints, it was not possible to realize it in time. When I have free time in the future, I will improve this code and open source it to the Internet.

#### Display of navigation routes and sights

Careful readers can see that the "Route" tab only displays a distance information, and there is nothing below it, which appears empty.

!["Route" tab](attachments/Jump to "Route" and display distance.png)

In fact, this is not a design error. Below the text box showing the total distance, the original plan was to display a list of all the important sights the route passed. Including the name of the scenic spot, description information, distance from the previous scenic spot (relative distance), and orientation (east, south, northwest, etc.).

This is not complicated to implement on the back end. First of all, the name and description information of the scenic spot can be directly extracted from the database, and the distance from the previous scenic spot, as long as you get the objects of all the paths between two points, you can access their `Data` fields and add them up , you can get a relative distance.
And if you want to measure the orientation, you can regard the starting point and the ending point as two coordinates on the plane Cartesian coordinate system, and the starting point and the ending point are a vector. Calculate the angle between this vector and the coordinate axis to get the orientation (use the `Math` library to calculate the cosine value `cos`, and use the arccos function `arccos` to reverse the angle).

Similarly, this requirement was not realized due to lack of time, and it had to be shelved in the end.

### 3. Some regrets: ideas that could not be realized due to technical reasons

#### Using the MVVM design pattern

![](attachments/project structure MVVM.png)

In my file structure, there is an empty `ViewModels` folder. Obviously this is for the MVVM design pattern. But because I am not familiar enough with C# and MVVM Toolkit, this has not been implemented.

(Fortunately, in another topic of data structure course design, you can already use the basic MVVM idea for page navigation).

#### System use WPF

The front-end design of this project can be described as a disaster. Although the finish and texture of the final product are acceptable, the code style and project management are disasters on the production site. This kind of product made entirely by personal temporary support and overtime work is definitely not worth admiring. Therefore, it is imminent for me to learn some basic front-end technologies and C# design patterns.

### 4. Course Summary

In this course design, I spent about a week to complete the development of the project, and spent more than a week to summarize and sort out, and then I came up with this course design document. This is a practical experience of great significance to me. It has allowed me to gain confidence in software development, and at the same time, it has made me more proficient in sorting out and mastering the knowledge of data structures in a semester. After taking the first step, it made me see how long the road ahead is. Fortunately, that is not an unattainable goal. I hope that I can play better in my future studies and various practical operations.

## Eight, open source reference

The open source reference part includes the development environment, open source framework, and open source library used in this project.
Sincerely thank all open source authors and software for their support in my development and learning.

### Development environment, IDE

- C# development environment, "the first in the universe" IDE: Visual Studio Community 2022
- Python development environment: Visual Studio Code
- Visual Studio plugin: JetBrains ReSharper
- Note and document software: Obsidian
- Vector drawing software: Adobe Illustrator 2022
- Front-end prototyping software: MasterGo
- Mind map, flowchart design software: ProcessOn

### Open Source Frameworks, Libraries, Plugins

- C# WPF front-end framework based on Google Material Design: [MaterialDesignInXamlTookit](https://github.com/MaterialDesignInXAML/MaterialDesignInXamlToolkit);
- A plugin that supports `.json` format files for billions of C# and .NET programs: [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/);
- A `.svg` extraction tool based on the Python `numpy` library: [svgpathtools](https://github.com/mathandy/svgpathtools).

### Auxiliary tools

- Online `.jpg` to `.ico` website: [convertico](https://convertio.co/jpg-ico/).

## Nine, reference

The following user and site documentation resources helped me address core issues during this project. I would like to express my gratitude here, and I hope it will be helpful to you who are looking at the document.

- [Trigger event from dropdown menu text changed](https://stackoverflow.com/questions/6914942/how-do-i-get-textchanged-events-from-an-editable-combobox)
- [Add `Click` event to `Button` in C# program](https://stackoverflow.com/questions/728432/how-to-programmatically-click-a-button-in-wpf)
- [C# `Expander` combined with `ItemsControl` to batch add list](https://stackoverflow.com/questions/35794528/wpf-expander-in-items-control-with-user-control-fit-available-space)
- [Using `ObservableCollection<T>` for dynamic binding](https://cloud.tencent.com/developer/article/1828783)
- [WPF Dynamic Binding](https://blog.csdn.net/niuge8905/article/details/78760872)

And, the textbook-like [Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/csharp/) documentation.

## Ten, ToDo-List

Record all bugs and feature improvements currently encountered:

- [ ] After clicking to clear the information, the length of the route will not be cleared;
- [x] Show text error when hovering over button (shows Material Design's default tooltip text);
- [ ] Replace MessageBox with MaterialDesign-style Dialog
- [ ] In admin mode, click on the disabled (red) route to switch back to the wrong color, which is light green (should be turquoise)
- [ ] Change dropdown to AutoSuggestBox (optional)
- [ ] Add specific route display under "Route"
